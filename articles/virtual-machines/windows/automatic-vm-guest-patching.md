---
title: Aplicação automática de patches de convidado de VM para VMs do Windows no Azure
description: Saiba como corrigir automaticamente as máquinas virtuais do Windows no Azure
author: mayanknayar
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/09/2020
ms.author: manayar
ms.openlocfilehash: 0a777b9008864368a6d1731cae0374e55a4c585f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842862"
---
# <a name="preview-automatic-vm-guest-patching-for-windows-vms-in-azure"></a>Visualização: Aplicação de patch automática de convidado de VMs para Windows no Azure

Habilitar o patch automático de convidado de VM para suas VMs do Windows ajuda a facilitar o gerenciamento de atualizações, aplicando patches de máquinas virtuais com segurança e automaticamente para manter a conformidade com a segurança.

A aplicação automática de patches de convidado de VM tem as seguintes características:
- Os patches classificados como *críticos* ou de *segurança* são baixados e aplicados automaticamente na VM.
- Os patches são aplicados fora do horário de pico no fuso horário da VM.
- A orquestração de patch é gerenciada pelo Azure e os patches são aplicados após os princípios de disponibilidade-primeiro.
- A integridade da máquina virtual, conforme determinado por meio de sinais de integridade da plataforma, é monitorada para detectar falhas de patch.
- Funciona para todos os tamanhos de VM.

> [!IMPORTANT]
> A aplicação automática de patches de convidado de VM está atualmente em visualização pública. Um procedimento de aceitação é necessário para usar a funcionalidade de visualização pública descrita abaixo.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Como funciona a aplicação automática de patches de convidado de VM?

Se a aplicação automática de patches de convidado de VM estiver habilitada em uma VM, os patches *críticos* e de *segurança* disponíveis serão baixados e aplicados automaticamente na VM. Esse processo é ativado automaticamente todos os meses quando novos patches são lançados por meio de Windows Update. A avaliação e a instalação do patch são automáticas e o processo inclui a reinicialização da VM, conforme necessário.

A VM é avaliada periodicamente para determinar os patches aplicáveis para essa VM. Os patches podem ser instalados em qualquer dia na VM fora do horário de pico da VM. Essa avaliação automática garante que todos os patches ausentes sejam descobertos na primeira oportunidade possível.

Os patches são instalados dentro de 30 dias da versão mensal do Windows Update, após a primeira orquestração de disponibilidade descrita abaixo. Os patches são instalados somente fora do horário de pico para a VM, dependendo do fuso horário da VM. A VM deve estar em execução durante os horários de pico para que os patches sejam instalados automaticamente. Se uma VM for desligada durante uma avaliação periódica, a VM será automaticamente avaliada e os patches aplicáveis serão instalados automaticamente durante a próxima avaliação periódica quando a VM estiver ligada.

Para instalar patches com outras classificações de patch ou agendar a instalação de patch em sua própria janela de manutenção personalizada, você pode usar [Gerenciamento de atualizações](tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Disponibilidade-primeira aplicação de patches

O processo de instalação do patch é orquestrado globalmente pelo Azure para todas as VMs que têm a aplicação automática de patches de convidado de VM habilitada. Essa orquestração segue os princípios de disponibilidade-primeiros em diferentes níveis de disponibilidade fornecidos pelo Azure.

Para um grupo de máquinas virtuais que passa por uma atualização, a plataforma do Azure orquestrará as atualizações:

**Entre regiões:**
- Uma atualização mensal é orquestrada no Azure globalmente em uma maneira em fases para evitar falhas de implantação global.
- Uma fase pode ter uma ou mais regiões e uma atualização será movida para as próximas fases somente se as VMs qualificadas em uma fase forem atualizadas com êxito.
- Regiões emparelhadas geograficamente não são atualizadas simultaneamente e não podem estar na mesma fase regional.
- O sucesso de uma atualização é medido acompanhando a atualização da integridade da VM. A integridade da VM é controlada por meio de indicadores de integridade da plataforma para a VM.

**Dentro de uma região:**
- As VMs em diferentes Zonas de Disponibilidade não são atualizadas simultaneamente.
- As VMs que não fazem parte de um conjunto de disponibilidade são agrupadas em uma base de melhor esforço para evitar atualizações simultâneas para todas as VMs em uma assinatura.

**Em um conjunto de disponibilidade:**
- Todas as VMs em um conjunto de disponibilidade comum não são atualizadas simultaneamente.
-   As VMs em um conjunto de disponibilidade comum são atualizadas em limites de domínio de atualização e as VMs em vários domínios de atualização não são atualizadas simultaneamente.

A data de instalação do patch para uma determinada VM pode variar de mês a mês, pois uma VM específica pode ser selecionada em um lote diferente entre os ciclos de aplicação de patch mensais.

## <a name="supported-os-images"></a>Imagens do sistema operacional com suporte
Atualmente, somente as VMs criadas a partir de determinadas imagens da plataforma de sistema operacional têm suporte na visualização. Atualmente, não há suporte para imagens personalizadas na visualização.

Atualmente, há suporte para as seguintes SKUs de plataforma (e mais são adicionadas periodicamente):

| Publicador               | Oferta de sistema operacional      |  Sku               |
|-------------------------|---------------|--------------------|
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Server-Core |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-servidor-núcleo |

## <a name="patch-orchestration-modes"></a>Modos de orquestração de patch
As VMs do Windows no Azure agora dão suporte aos seguintes modos de orquestração de patch:

**AutomaticByPlatform:**
- Esse modo permite a aplicação automática de patches de convidado de VM para a máquina virtual do Windows e a instalação do patch subsequente é orquestrada pelo Azure.
- A definição desse modo também desabilita o Atualizações Automáticas nativo na máquina virtual do Windows para evitar a duplicação.
- Esse modo só tem suporte para VMs que são criadas usando as imagens de plataforma de sistema operacional com suporte acima.
- Para usar esse modo, defina a propriedade `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e defina a propriedade  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatfom` no modelo de VM.

**AutomaticByOS:**
- Esse modo permite Atualizações Automáticas na máquina virtual do Windows e os patches são instalados na VM por meio de Atualizações Automáticas.
- Esse modo é definido por padrão se nenhum outro modo de patch for especificado.
- Para usar esse modo, defina a propriedade `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e defina a propriedade  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` no modelo de VM.

**Manual:**
- Esse modo desabilita Atualizações Automáticas na máquina virtual do Windows.
- Esse modo deve ser definido ao usar soluções de aplicação de patch personalizadas.
- Para usar esse modo, defina a propriedade `osProfile.windowsConfiguration.enableAutomaticUpdates=false` e defina a propriedade  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` no modelo de VM.

> [!NOTE]
>A propriedade `osProfile.windowsConfiguration.enableAutomaticUpdates` só pode ser definida no momento quando a VM é criada pela primeira vez. Atualmente, não há suporte para alternar do manual para um modo automático ou dos modos automático para o modo manual. Há suporte para a alternância do modo AutomaticByOS para o modo AutomaticByPlatfom

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Requisitos para habilitar o patch automático de convidado de VM

- A máquina virtual deve ter o [agente de VM do Azure](../extensions/agent-windows.md) instalado.
- O serviço de Windows Update deve estar em execução na máquina virtual.
- A máquina virtual deve ser capaz de acessar Windows Update pontos de extremidade. Se sua máquina virtual estiver configurada para usar o Windows Server Update Services (WSUS), os pontos de extremidade relevantes do servidor do WSUS deverão estar acessíveis.
- Use a API de computação versão 2020-06-01 ou superior.

A habilitação da funcionalidade de visualização requer uma aceitação única para o recurso *InGuestAutoPatchVMPreview* por assinatura, conforme detalhado abaixo.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como habilitar a visualização para sua assinatura:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```

Depois que o recurso for registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para habilitar a visualização para sua assinatura.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Depois que o recurso for registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use o [registro de recurso AZ](/cli/azure/feature#az-feature-register) para habilitar a visualização para sua assinatura.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Depois que o recurso for registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```
## <a name="enable-automatic-vm-guest-patching"></a>Permitir a aplicação de patch automática de convidado de VM
Para habilitar o patch automático de convidado de VM, verifique se a propriedade *osProfile. windowsConfiguration. enableAutomaticUpdates* está definida como *true* na definição do modelo de VM. Essa propriedade só pode ser definida ao criar a VM.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como habilitar a aplicação automática de patches de convidado de VM:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) para habilitar a aplicação automática de patches de convidado de VM ao criar ou atualizar uma VM.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use [AZ VM Create](/cli/azure/vm#az-vm-create) para habilitar a aplicação automática de patches de convidado de VM ao criar uma nova VM. O exemplo a seguir configura o patch automático de convidado de VM para uma VM chamada *myVM* no grupo de recursos chamado *MyResource*Group:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Para modificar uma VM existente, use [AZ VM Update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Habilitação e avaliação

> [!NOTE]
>Pode levar mais de três horas para habilitar as atualizações automáticas de convidados de VM em uma VM, pois a habilitação é concluída durante os horários de picos da VM. Como a avaliação e a instalação do patch ocorrem somente fora do horário de pico, sua VM também deve estar em execução fora do horário de pico para aplicar patches.

Quando a aplicação automática de patches de convidado de VM está habilitada para uma VM, uma extensão de VM do tipo `Microsoft.CPlat.Core.WindowsPatchExtension` é instalada na VM. Essa extensão não precisa ser instalada ou atualizada manualmente, pois essa extensão é gerenciada pela plataforma do Azure como parte do processo automático de aplicação de patch de convidado de VM.

Pode levar mais de três horas para habilitar as atualizações automáticas de convidados de VM em uma VM, pois a habilitação é concluída durante os horários de picos da VM. A extensão também é instalada e atualizada fora do horário de pico da VM. Se o término do horário fora de pico da VM antes da habilitação puder ser concluída, o processo de habilitação será retomado durante o próximo horário de pico disponível. Se a VM tiver tido a Windows Update automática ativada por meio do modo de patch AutomaticByOS, a Windows Update automática será desativada para a VM quando a extensão for instalada.

Para verificar se a aplicação automática de patches de convidado de VM foi concluída e se a extensão de aplicação de patch está instalada na VM, você pode examinar a exibição de instância da VM. Se o processo de habilitação estiver concluído, a extensão será instalada e os resultados da avaliação para a VM estarão disponíveis em `patchStatus` . A exibição de instância da VM pode ser acessada por meio de várias maneiras, conforme descrito abaixo.

### <a name="rest-api"></a>API REST

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-06-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) com o `-Status` parâmetro para acessar a exibição de instância para sua VM.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

Atualmente, o PowerShell fornece apenas informações sobre a extensão de patch. Informações sobre o `patchStatus` também estarão disponíveis em breve por meio do PowerShell.

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use [AZ VM Get-Instance-View](/cli/azure/vm#az-vm-get-instance-view) para acessar a exibição de instância para sua VM.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Noções básicas sobre o status do patch para sua VM

A `patchStatus` seção da resposta de exibição de instância fornece detalhes sobre a avaliação mais recente e a última instalação de patch para sua VM.

Os resultados da avaliação para sua VM podem ser examinados na `availablePatchSummary` seção. Uma avaliação é realizada periodicamente para uma VM que tem aplicação de patch de convidado de VM automática habilitada. A contagem de patches disponíveis após uma avaliação é fornecida `criticalAndSecurityPatchCount` nos `otherPatchCount` resultados e. O patch automático de convidado de VM instalará todos os patches avaliados sob as classificações de patches *críticos* e de *segurança* . Qualquer outro patch avaliado é ignorado.

Os resultados da instalação do patch para sua VM podem ser examinados na `lastPatchInstallationSummary` seção. Esta seção fornece detalhes sobre a última tentativa de instalação de patch na VM, incluindo o número de patches que foram instalados, pendentes, com falha ou ignorados. Os patches são instalados somente durante o horário de pico na janela de manutenção da VM. Os patches pendentes e com falha são automaticamente repetidos durante a próxima janela de manutenção de horários fora do horário de pico.

## <a name="on-demand-patch-assessment"></a>Avaliação de patch sob demanda
Se a aplicação automática de patches de convidado de VM já estiver habilitada para sua VM, uma avaliação de patch periódica será executada na VM durante os horários de fora de pico da VM. Esse processo é automático e os resultados da avaliação mais recente podem ser examinados por meio da exibição de instância da VM, conforme descrito anteriormente neste documento. Você também pode disparar uma avaliação de patch sob demanda para sua VM a qualquer momento. A avaliação de patch pode levar alguns minutos para ser concluída e o status da avaliação mais recente é atualizado na exibição de instância da VM.

Habilitar a funcionalidade de visualização requer uma aceitação única para o recurso *InGuestPatchVMPreview* por assinatura. A versão prévia do recurso para avaliação de patch sob demanda pode ser habilitada seguindo o [processo de habilitação de visualização](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) descrito anteriormente para aplicação automática de patches de convidado de VM.

> [!NOTE]
>A avaliação de patch sob demanda não dispara automaticamente a instalação do patch. Os patches avaliados e aplicáveis para a VM serão instalados somente durante os horários de pico de disponibilidade da VM, seguindo o processo de aplicação de patch da primeira atualização descrito anteriormente neste documento.

### <a name="rest-api"></a>API REST
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) para avaliar os patches disponíveis para sua máquina virtual.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use [AZ VM avalie-patches](/cli/azure/vm#az-vm-assess-patches) para avaliar os patches disponíveis para sua máquina virtual.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Saiba mais sobre como criar e gerenciar máquinas virtuais do Windows](tutorial-manage-vm.md)
