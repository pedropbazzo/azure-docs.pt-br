---
title: Atualização da Azure Load Balancer pública básica para a Standard
description: Este artigo mostra como atualizar os Load Balancer internos do Azure básico para o público Load Balancer padrão
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 225252f2cd47c36de2c7eed4ed1e5dae3ebd81b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078761"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>Atualização de Load Balancer interno do Azure-conexão de saída necessária
O [Azure Standard Load Balancer](load-balancer-overview.md) oferece um conjunto avançado de funcionalidades e alta disponibilidade por meio de redundância de zona. Para saber mais sobre Load Balancer SKU, confira [tabela de comparação](https://docs.microsoft.com/azure/load-balancer/skus#skus). Como o Load Balancer interno padrão não fornece conexão de saída, fornecemos uma solução para criar um Load Balancer público padrão.

Há quatro estágios em uma atualização:

1. Migrar a configuração para o Load Balancer público padrão
2. Adicionar VMs a pools de back-end de Load Balancer públicos padrão
3. Configurar regras de NSG para sub-redes/VMs que devem ser disparadas de/para a Internet

Este artigo aborda a migração de configuração. A adição de VMs a pools de back-end pode variar dependendo do seu ambiente específico. No entanto, algumas recomendações gerais de alto nível [são fornecidas](#add-vms-to-backend-pools-of-standard-load-balancer).

## <a name="upgrade-overview"></a>Visão geral da atualização

Há um script de Azure PowerShell disponível que faz o seguinte:

* Cria uma Load Balancer pública de SKU padrão no grupo de recursos e no local que você especificar.
* Copia diretamente as configurações do Load Balancer interno do SKU básico para o recém-criado Load Balancer público padrão.
* Cria uma regra de saída que habilita a conectividade de saída.

### <a name="caveatslimitations"></a>Caveats\Limitations

* O script dá suporte à atualização de Load Balancer interna em que a conexão de saída é necessária. Se a conexão de saída não for necessária para nenhuma das VMs, consulte [esta página](upgrade-basicInternal-standard.md) para obter uma prática recomendada.
* O Standard Load Balancer tem um novo endereço público. É impossível mover os endereços IP associados a Load Balancer internos básicos existentes diretamente para o Load Balancer público padrão, pois eles têm SKUs diferentes.
* Se o balanceador de carga padrão for criado em uma região diferente, você não poderá associar as VMs existentes na região antiga ao Standard Load Balancer recém-criado. Para contornar essa limitação, certifique-se de criar uma nova VM na nova região.
* Se seu Load Balancer não tiver nenhuma configuração de IP de front-end ou pool de back-ends, provavelmente você encontrará um erro ao executar o script.  Verifique se eles não estão vazios.

## <a name="download-the-script"></a>Baixar o script

Baixe o script de migração do  [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureLBUpgrade/2.0).
## <a name="use-the-script"></a>Usar o script

Há duas opções para você dependendo da configuração e das preferências do ambiente do PowerShell local:

* Se você não tiver os módulos AZ do Azure instalados ou não se lembrar de desinstalar os módulos AZ do Azure, a melhor opção é usar a `Install-Script` opção para executar o script.
* Se você precisar manter os módulos AZ do Azure, sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se você tem os módulos AZ do Azure instalados, execute `Get-InstalledModule -Name az` . Se você não vir nenhum módulo AZ instalado, poderá usar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instalar usando o método de Install-Script

Para usar essa opção, você não deve ter os módulos AZ do Azure instalados no seu computador. Se eles estiverem instalados, o comando a seguir exibirá um erro. Você pode desinstalar os módulos AZ do Azure ou usar a outra opção para baixar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzurePublicLBUpgrade`

Esse comando também instala os módulos AZ necessários.  

### <a name="install-using-the-script-directly"></a>Instalar usando o script diretamente

Se você tiver alguns módulos AZ do Azure instalados e não puder desinstalá-los (ou não quiser desinstalá-los), poderá baixar manualmente o script usando a guia **download manual** no link de download de script. O script é baixado como um arquivo nupkg bruto. Para instalar o script desse arquivo nupkg, consulte [download de pacote manual](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Use `Connect-AzAccount` para se conectar ao Azure.

1. Use `Import-Module Az` para importar os módulos AZ.

1. Examine os parâmetros necessários:

   * **oldRgName: [String]: Required** – este é o grupo de recursos para o Load Balancer básico existente que você deseja atualizar. Para localizar esse valor de cadeia de caracteres, navegue até portal do Azure, selecione sua fonte de Load Balancer básica e clique na **visão geral** do balanceador de carga. O grupo de recursos está localizado nessa página.
   * **oldLBName: [String]: Required** – este é o nome do balanceador básico existente que você deseja atualizar. 
   * **newrgName: [String]: Required** – este é o grupo de recursos no qual o Standard Load Balancer será criado. Pode ser um novo grupo de recursos ou um existente. Se você escolher um grupo de recursos existente, observe que o nome do Load Balancer deve ser exclusivo dentro do grupo de recursos. 
   * **NewLocation: [String]: Required** – este é o local no qual o Standard Load Balancer será criado. É recomendável herdar o mesmo local do Load Balancer básico escolhido para o Standard Load Balancer para uma melhor associação com outros recursos existentes.
   * **newLBName: [String]: Required** – este é o nome do Standard Load Balancer a ser criado.
1. Execute o script usando os parâmetros apropriados. Pode levar de cinco a sete minutos para ser concluído.

    **Exemplo**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Adicionar VMs a pools de back-end de Standard Load Balancer

Primeiro, verifique se o script criou com êxito um novo Load Balancer público padrão com a configuração exata migrada do seu Load Balancer público básico. Você pode verificar isso no portal do Azure.

Lembre-se de enviar uma pequena quantidade de tráfego por meio do Standard Load Balancer como um teste manual.
  
Aqui estão alguns cenários de como adicionar VMs a pools de back-end do Load Balancer público padrão criado recentemente pode ser configurado e nossas recomendações para cada um deles:

* **Mover VMs existentes de pools de back-end de Load Balancer públicas públicos antigos para pools de back-end de Load Balancer públicas padrão recém-criados**.
    1. Para realizar todas as tarefas deste início rápido, entre no [portal do Azure](https://portal.azure.com).
 
    1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione o **Standard Load Balancer recém-criado** na lista de recursos.
   
    1. Em **Configurações**, selecione **Pools de back-end**.
   
    1. Selecione o pool de back-end que corresponde ao pool de back-end do Load Balancer básico, selecione o seguinte valor: 
      - **Máquina virtual**: lista suspensa e selecione as VMs do pool de back-end correspondente do Load Balancer básico.
    1. Clique em **Salvar**.
    >[!NOTE]
    >Para VMs que têm IPs públicos, você precisará criar endereços IP padrão primeiro, em que o mesmo endereço IP não é garantido. Desassocie as VMs de IPs básicos e associe-as aos endereços IP padrão recém-criados. Em seguida, você poderá seguir as instruções para adicionar VMs ao pool de back-end de Standard Load Balancer. 

* **Criar novas VMs para adicionar aos pools de back-end do Load Balancer público padrão criado recentemente**.
    * Mais instruções sobre como criar uma VM e associá-las ao Standard Load Balancer podem ser encontradas [aqui](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Criar uma regra de saída para conexão de saída

Siga as [instruções](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) para criar uma regra de saída para que você possa
* Defina o NAT de saída do zero.
* Dimensione e ajuste o comportamento do NAT de saída existente.

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>Criar regras NSG para VMs que evitem a comunicação de ou para a Internet
Se você quiser evitar que o tráfego da Internet chegue às suas VMs, poderá criar uma [regra NSG](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) na interface de rede das VMs.

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Há alguma limitação com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Consulte [Advertências/limitações](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script de Azure PowerShell também alterna o tráfego do meu Load Balancer básico para o Standard Load Balancer recém-criado?

Não. O script de Azure PowerShell migra apenas a configuração. A migração de tráfego real é sua responsabilidade e no seu controle.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Ocorreu alguns problemas com o uso desse script. Como posso obter ajuda?
  
Você pode enviar um email para o slbupgradesupport@microsoft.com , abrir um caso de suporte com o suporte do Azure ou fazer ambos.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Standard Load Balancer](load-balancer-overview.md)
