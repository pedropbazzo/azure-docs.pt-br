---
title: Implantar o Firewall do Azure com vários endereços IP públicos usando o PowerShell
description: Neste artigo, você aprenderá a implantar um firewall do Azure com vários endereços IP públicos usando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: cbad025a0d0c4d679ea9cdc7557c81b5145798fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85610669"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Implantar um Firewall do Azure com vários endereços IP públicos usando o Azure PowerShell

Esse recurso habilita os seguintes cenários:

- **DNAT**: várias instâncias de porta padrão podem ser traduzidas em seus servidores de back-end. Por exemplo, se você tem dois endereços IP públicos, pode traduzir a porta TCP 3389 (RDP) para os dois endereços IP.
- **SNAT**: as portas adicionais estão disponíveis para conexões SNAT de saída, reduzindo a possibilidade de esgotamento da porta SNAT. Neste momento, o Firewall do Azure seleciona aleatoriamente o endereço IP público do código-fonte a ser usado para uma conexão. Se você tiver qualquer filtragem downstream em sua rede, precisará permitir todos os endereços IP públicos associados com seu firewall. Considere usar um [prefixo de endereço IP público](../virtual-network/public-ip-address-prefix.md) para simplificar essa configuração.
 
O Firewall do Azure com vários endereços IP públicos está disponível por meio do portal do Azure, do Azure PowerShell, da CLI do Azure, do REST e de modelos. Você pode implantar um firewall do Azure com até 250 endereços IP públicos.

Os exemplos de Azure PowerShell a seguir mostram como você pode configurar, adicionar e remover endereços IP públicos para o Firewall do Azure.

> [!NOTE]
> Não é possível remover a primeira ipConfiguration da página de configuração de endereço IP público do firewall do Azure. Se você quiser modificar o endereço IP, poderá usar Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Criar um firewall com dois ou mais endereços IP públicos

Este exemplo cria um firewall anexado à *vnet* da rede virtual com dois endereços IP públicos.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Adicionar um endereço IP público a um firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* é anexado ao firewall.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Remover um endereço IP público de um firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* é desanexado do firewall.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um Firewall do Azure com vários endereços IP públicos – modelo do Resource Manager](quick-create-multiple-ip-template.md)
