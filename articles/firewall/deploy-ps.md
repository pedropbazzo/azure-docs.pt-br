---
title: Implantar e configurar o Firewall do Azure usando o Azure PowerShell
description: Neste artigo, você aprenderá a implantar e configurar o Firewall do Azure usando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/28/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: c720d7c261421ade9dfce01f0b116123dcab1e55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071696"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Implantar e configurar o Firewall do Azure usando o Azure PowerShell

O controle do acesso à saída de rede é uma parte importante de um plano geral de segurança de rede. Por exemplo, você talvez queira limitar o acesso a sites. Ou você talvez queira limitar os endereços IP e portas de saída que podem ser acessados.

Uma maneira de controlar o acesso à saída de rede em uma sub-rede do Azure é com o Firewall do Azure. Com o Firewall do Azure, você pode configurar:

* Regras de aplicativo que definem FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados em uma sub-rede.
* Regras de rede que definem endereço de origem, protocolo, porta de destino e endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando o tráfego de rede para o firewall foi roteado como a sub-rede de gateway padrão.

Para este artigo, você cria uma VNet única simplificada com três sub-redes para facilitar a implantação. Para implantações de produção, é recomendado um [modelo de hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), em que o firewall é, por si só, a VNet. Os servidores de carga de trabalho estão em VNets emparelhadas na mesma região que uma ou mais sub-redes.

* **AzureFirewallSubnet**: o firewall está nesta sub-rede.
* **Workload-SN**: o servidor de carga de trabalho está nessa sub-rede. O tráfego de rede dessa sub-rede passa pelo firewall.
* **Jump-SN** -o servidor “jump” está nessa sub-rede. O servidor jump tem um endereço IP público a que você pode se conectar usando a Área de Trabalho Remota. A partir daí, você pode se conectar (usando outra Área de Trabalho Remota) ao servidor de carga de trabalho.

![Infraestrutura de rede do tutorial](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste artigo, você aprenderá como:


* Configurar um ambiente de rede de teste
* Implantar um firewall
* Criar uma rota padrão
* Configure uma regra de aplicativo para permitir acesso a www.google.com
* Configurar uma regra de rede para permitir o acesso a servidores DNS externos
* Testar o firewall

Se preferir, você pode concluir este procedimento usando o [portal do Azure](tutorial-firewall-deploy-portal.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer que você execute o PowerShell localmente. Você deve ter os módulos do Azure PowerShell instalados. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Depois de verificar a versão do PowerShell, execute `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="set-up-the-network"></a>Configurar a rede

Primeiro, crie um grupo de recursos para conter os recursos necessários à implantação do firewall. Em seguida, crie uma VNET, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para a implantação.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Criar uma VNET

Essa rede virtual tem três sub-redes:

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, confira [Perguntas frequentes sobre o Firewall do Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
Agora, crie a rede virtual:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie as máquinas virtuais de jump e carga de trabalho e coloque-as nas sub-redes apropriadas.
Quando solicitado, digite um nome de usuário e senha da máquina virtual.

Crie o Srv-Jump máquina virtual.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Crie uma máquina virtual de carga de trabalho sem endereço IP público.
Quando solicitado, digite um nome de usuário e senha da máquina virtual.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Implantar o firewall

Agora, implante o firewall na rede virtual.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Anote o endereço IP privado. Você o usará mais tarde quando criar a rota padrão.

## <a name="create-a-default-route"></a>Criar uma rota padrão

Criar uma tabela, com a propagação de rota BGP desabilitada

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicativo

A regra de aplicativo permite o acesso de saída ao www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. Para saber mais, veja [FQDNs de infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

A regra de rede permite o acesso de saída a dois endereços IP na porta 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Altere os endereços DNS primário e secundário para o adaptador de rede **Srv-Wprk**

Para fins de teste neste procedimento, configure os endereços DNS primários e secundários do servidor. Isso não é um requisito geral do Firewall do Azure.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Testar o firewall

Agora teste o firewall para confirmar se ele funciona conforme o esperado.

1. Anote o endereço IP privado para a máquina virtual **SRV-Work** :

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Conecte uma área de trabalho remota à máquina virtual **Srv-Jump** e entre. A partir daí, abra uma conexão de área de trabalho remota para o endereço IP privado de **trabalho SRV** e entre.

3. Em **SRV-trabalho**, abra uma janela do PowerShell e execute os seguintes comandos:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Os dois comandos devem retornar respostas, mostrando que as consultas DNS estão passando pelo firewall.

1. Execute os comandos a seguir:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   As `www.google.com` solicitações devem ter êxito e as `www.microsoft.com` solicitações devem falhar. Isso demonstra que as regras de firewall estão funcionando conforme o esperado.

Agora que você verificou se as regras de firewall estão funcionando:

* É possível resolver nomes DNS usando o servidor DNS externo configurado.
* Você pode navegar para o FQDN permitido, mas não para os outros.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou, se não for mais necessário, excluir o grupo de recursos **Test-FW-RG** para excluir todos os recursos relacionados ao firewall:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
