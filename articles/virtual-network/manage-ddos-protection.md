---
title: Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure
titlesuffix: Azure Virtual Network
description: Saiba como usar a telemetria da Proteção contra DDoS do Azure Standard no Azure Monitor para mitigar um ataque.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 706379649b47846b5c020dc76493a98e346c4a8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317677"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure

Saiba como habilitar e desabilitar a proteção contra DDoS (ataque de negação de serviço distribuído) e usar a telemetria para atenuar ataques contra DDoS com a Proteção contra DDoS Standard do Azure. A Proteção contra DDoS Standard protege os recursos do Azure como máquinas virtuais, balanceadores de carga e gateways de aplicativo que têm um [endereço IP público](virtual-network-public-ip-address.md) do Azure atribuído a eles. Para saber mais sobre a Proteção contra DDoS Standard e suas funcionalidades, consulte [Visão geral da Proteção contra DDoS Standard](ddos-protection-overview.md).

Antes de concluir qualquer etapa neste tutorial, faça logon no portal do Azure em https://portal.azure.com com uma conta atribuída à função [contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que as ações adequadas atribuídas estão listadas em [Permissões](#permissions-and-restrictions).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção contra DDoS

Um plano de proteção contra DDoS define um conjunto de redes virtuais que têm um padrão de proteção DDoS habilitada, entre assinaturas. Você pode configurar um plano de proteção contra DDoS para sua organização e vincular redes virtuais de várias assinaturas para o mesmo plano. O plano de proteção contra DDoS também está associado a uma assinatura, que você seleciona durante a criação do plano. O plano de proteção contra DDoS funciona em regiões e assinaturas. Exemplo – você pode criar o plano na região leste-EUA e vincular à assinatura #1 em seu locatário. O mesmo plano pode ser vinculado a redes virtuais de outras assinaturas em regiões diferentes, em seu locatário. A assinatura do plano está associada a gerar a fatura mensal recorrente para o plano, bem como encargos excedentes, caso o número de endereços IP públicos protegidos exceder 100. Para obter mais informações sobre os preços de DDoS, consulte [Detalhes do preço](https://azure.microsoft.com/pricing/details/ddos-protection/).

A criação de mais de um plano não é necessária para a maioria das organizações. O plano não pode ser movido entre as assinaturas. Se você quiser alterar a assinatura de um plano, você precisa [excluir o plano existente](#work-with-ddos-protection-plans) e criar um novo.

1. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
2. Pesquise por *DDoS*. Quando o **plano de proteção contra DDoS** aparecer nos resultados da pesquisa, selecione-o.
3. Selecione **Criar**.
4. Insira ou selecione seus próprios valores, ou insira ou selecione os seguintes valores de exemplo e, em seguida, selecione **Criar**:

    |Configuração        |Valor                                              |
    |---------      |---------                                          |
    |Nome           | myDdosProtectionPlan                              |
    |Subscription   | Selecione sua assinatura.                         |
    |Resource group | Selecione **criar novo** e insira o *myresourceattribute* |
    |Localização       | Leste dos EUA                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Habilitar DDoS para uma nova rede virtual

1. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Insira ou selecione seus próprios valores, ou insira ou selecione os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **Criar**:

    | Configuração         | Valor                                                        |
    | ---------       | ---------                                                    |
    | Nome            | myVirtualNetwork                                             |
    | Subscription    | Selecione sua assinatura.                                    |
    | Resource group  | Clique em **Usar existente** e selecione **myResourceGroup** |
    | Localização        | Leste dos EUA                                                      |
    | Proteção contra DDoS Standard | Selecione **Habilitar**. O plano selecionado pode estar na mesma assinatura ou em assinatura diferente que a rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.|

Você não pode mover uma rede virtual para outro grupo de recursos ou assinatura quando o DDoS padrão está habilitado para a rede virtual. Se você precisar mover uma rede virtual com um padrão de DDoS habilitado, desabilite o padrão de DDoS primeiro, mova a rede virtual e, em seguida, habilite padrão de DDoS. Após a movimentação, os limites da política ajustados automaticamente para todos os endereços IP públicos protegidos na rede virtual são redefinidos.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Habilitar DDoS para uma rede virtual existente

1. Crie um plano de proteção contra DDoS completando as etapas em [Criar um plano de proteção contra DDoS](#create-a-ddos-protection-plan), se você não tiver um plano de proteção contra DDoS existente.
2. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
3. Insira o nome da rede virtual para a qual você deseja habilitar o padrão de proteção contra DDoS na **caixa de documentos, serviços e recursos de pesquisa** na parte superior do portal. Quando o nome da rede virtual for exibido nos resultados da pesquisa, selecione-o.
4. Selecione **Proteção contra DDoS**, em **Configurações**.
5. Selecione **Padrão**. Em **Plano de proteção contra DDoS**, selecione um plano de proteção contra DDoS existente ou o plano que você criou na etapa 1 e, em seguida, selecione **Salvar**. O plano selecionado pode estar na mesma assinatura ou em assinatura diferente que a rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.

**Comandos** 
- CLI do Azure: [AZ Network DDoS-Protection Create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- PowerShell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Desabilitar DDoS para uma rede virtual

1. Insira o nome da rede virtual a qual você deseja desabilitar o padrão de proteção contra DDoS na **caixa de documentos, serviços e recursos de pesquisa** na parte superior do portal. Quando o nome da rede virtual for exibido nos resultados da pesquisa, selecione-o.
2. Selecione **em proteção contra DDoS Standard**, selecione **desabilitar**.

**Comandos** 
- CLI do Azure: [AZ Network DDoS-Protection Delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- PowerShell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Trabalhar com Planos de proteção contra DDoS

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *DDoS* na caixa **Filtro**. Selecione **Plano de proteção contra DDoS** quando aparecer nos resultados.
3. Selecione o plano de proteção que você deseja exibir na lista.
4. Todas as redes virtuais associadas ao plano são listadas.
5. Se você quiser excluir um plano, primeiro você deve desassociar todas as redes virtuais dele. Para desassociar um plano de uma rede virtual, consulte [Desabilitar DDoS para uma rede virtual](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurar alertas para métricas de proteção contra DDoS

Selecione uma das métricas de proteção contra DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque usando a configuração de alerta do Azure Monitor. Quando as condições forem atendidas, o endereço especificado receberá um email de alerta:

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Selecione **Métricas** em **Serviços Compartilhados**.
4. Insira ou selecione seus próprios valores, ou insira os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **Ok**:

    |Configuração                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |Nome                     | myDdosAlert                                                                                        |
    |Subscription             | Selecione a assinatura que contém o endereço IP público para o qual você deseja receber alertas.        |
    |Resource group           | Selecione o grupo de recursos que contém o endereço IP público para o qual você deseja receber alertas.      |
    |Recurso                 | Selecione o endereço IP público que contém o endereço IP público para o qual você deseja receber alertas. DDoS monitora os endereços IP públicos atribuídos aos recursos em uma rede virtual. Se você não tiver todos os recursos com endereços IP públicos na rede virtual, você deve primeiro criar um recurso com um endereço IP público. Você pode monitorar o endereço IP público de todos os recursos implantados por meio de Gerenciador de Recursos (não clássico) listado em [Rede Virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), exceto para os Ambientes de Serviço de Aplicativo do Azure e o Gateway de VPN do Azure. Para continuar este tutorial, você pode criar rapidamente uma máquina virtual do [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).                   |
    |Métrica                   | Sob ataque DDoS ou não                                                                            |
    |Limite                | 1 - **1** significa que você está sob ataque. **0** significa que você não está sob ataque.                         |
    |Período                   | Selecione o valor que você escolher.                                                                   |
    |Notificar por email         | Marque a caixa de seleção                                                                                  |
    |Administrador adicional | Insira seu endereço de email se você não é um proprietário de email, colaborador ou leitor para a assinatura. |

    Em poucos minutos de detecção de ataque, você receberá um email de métricas de Azure Monitor que é semelhante a figura a seguir:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque de DDoS para validar seu alerta, consulte [Validar detecção de DDoS](#validate-ddos-detection).

Você também pode aprender mais sobre [configuração de webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicativos lógicos](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criação de alertas.

## <a name="use-ddos-protection-telemetry"></a>Usar telemetria da proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente durante o tempo pelo qual um endereço IP público está sob mitigação. Você não vê a telemetria antes nem depois de um ataque ser mitigado.

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Selecione **métricas**, em **serviços compartilhados**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público para o qual você deseja a telemetria.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja a telemetria.
6. Uma série de **Métricas Disponíveis** aparecem no lado esquerdo da tela. Quando selecionadas, essas métricas são mostradas no **Gráfico de Métricas do Azure Monitor** na tela de visão geral.
7. Selecione o tipo de **agregação** como **máximo**

Os nomes de métrica apresentam diferentes tipos de pacotes e bytes versus pacotes, com um constructo básico de nomes de marcação em cada métrica, da seguinte maneira:

- **Nome de marcação removido** (por exemplo, **Pacotes de Entrada Removidos por DDoS**): o número de pacotes removidos pelo sistema de proteção contra DDoS.
- **Nome de marcação encaminhado** (por exemplo, **Pacotes de Entrada Encaminhados por DDoS**): o número de pacotes encaminhados pelo sistema de DDoS para o VIP de destino – tráfego que não foi filtrado.
- **Nenhum nome de marca** (por exemplo: **Pacotes de Entrada de DDoS**): o número total de pacotes que entraram no sistema de depuração – que representa a soma dos pacotes ignorados e encaminhados.

Para simular um ataque de DDoS para validar a telemetria, consulte [Validar detecção de DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Exibir políticas de mitigação de DDoS

A Proteção contra DDoS padrão aplica TCP SYN, TCP e UDP (três políticas de mitigação ajustadas automaticamente) para cada endereço IP público do recurso protegido, na rede virtual que tem o DDoS habilitado. Você pode exibir os limites da política selecionando os  **pacotes TCP de entrada para disparar a mitigação de DDoS** e **pacotes UDP de entrada para disparar** métricas de mitigação de DDoS com o tipo de **agregação** como ' Max ', conforme mostrado na figura a seguir:

![Exibir políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limites da política são configurados automaticamente por meio da nossa criação de perfil de tráfego de rede baseada em aprendizado de máquina do Azure. Somente quando o limite da política for excedido, ocorre a mitigação de DDoS para o endereço IP sob ataque.

## <a name="configure-ddos-attack-analytics"></a>Configurar a análise de ataque de DDoS
O padrão de Proteção contra DDoS do Azure fornece informações detalhadas de ataque e visualização com Análise de Ataque de DDoS. Os clientes que protegem suas redes virtuais contra ataques de DDoS têm visibilidade detalhada sobre o tráfego de ataque e as ações tomadas para reduzir o ataque por meio de relatórios de mitigação de ataque e logs de fluxo de mitigação. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Configurar relatórios de mitigação de ataque de DDoS
Relatórios de mitigação de ataque usam os dados do protocolo Netflow agregados para fornecer informações detalhadas sobre o ataque em seu recurso. Sempre que um recurso IP público estiver sob ataque, a geração de relatórios começará assim que a atenuação for iniciada. Também será gerado um relatório de incremental a cada 5 minutos e um relatório de pós-atenuação para todo o período de mitigação. Isso é para garantir que, no caso de o ataque de DDoS continuar por mais tempo, você poderá exibir o instantâneo mais recentes do relatório de atenuação a cada 5 minutos e um resumo completo quando a mitigação de ataque estiver concluída. 

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Em **Configurações**, selecione **Configurações de diagnóstico**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja registrar métricas.
6. Selecione **Ativar diagnóstico para coletar o log DDoSMitigationReports** e, em seguida, selecione tantas opções a seguir quanto você precisar:

    - **Arquivar em uma conta de armazenamento**: os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [arquivar logs de recursos](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos**: permite que um destinatário de log colete os logs usando um Hub de Eventos do Azure. Os Hubs de Eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [transmitir logs de recursos para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para log Analytics**: grava logs no serviço Azure monitor. Para saber mais sobre essa opção, consulte [coletar logs para uso em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Os relatórios de mitigação incrementais e pós-ataque incluem os seguintes campos
- Vetores de ataque
- Estatísticas de tráfego
- Motivo para pacotes ignorados
- Protocolos envolvidos
- Principais 10 regiões ou países de origem
- Principais 10 ASNs de origem

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Configurar logs de fluxo de mitigação de ataque de DDoS
Os Logs de Fluxo de Mitigação de Ataque permitem examinar o tráfego ignorado, o tráfego encaminhado e outros datapoints interessante durante um ataque de DDoS ativo quase em tempo real. Você pode ingerir o fluxo constante desses dados em seus sistemas SIEM por meio do hub de eventos para o monitoramento quase em tempo real, realizar ações possíveis e atender à necessidade de suas operações de defesa. 

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Em **Configurações**, selecione **Configurações de diagnóstico**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja registrar métricas.
6. Selecione **Ativar diagnóstico para coletar o log DDoSMitigationFlowLogs** e, em seguida, selecione tantas opções a seguir quanto você precisar:

    - **Arquivar em uma conta de armazenamento**: os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [arquivar logs de recursos](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos**: permite que um destinatário de log colete os logs usando um Hub de Eventos do Azure. Os Hubs de Eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [transmitir logs de recursos para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para log Analytics**: grava logs no serviço Azure monitor. Para saber mais sobre essa opção, consulte [coletar logs para uso em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Para exibir os dados de logs de fluxo na pasta de trabalho do Azure Analytics, você pode importar o painel de exemplo de https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Os logs de fluxo terão os seguintes campos: 
- IP de origem
- IP de destino
- Porta de origem 
- Porta de destino 
- Tipo de protocolo 
- Ação executada durante a mitigação

A análise de ataque só funcionará se a proteção contra DDoS Standard estiver habilitada na rede virtual do endereço IP público. 

## <a name="validate-ddos-detection"></a>Validar detecção de DDoS

A Microsoft firmou parceria com [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface em que você pode gerar tráfego contra endereços IP públicos com a Proteção contra DDoS habilitada para fins de simulação. A simulação do BreakPoint Cloud permitirá que você:

- Valide como a Proteção contra DDoS do Microsoft Azure protege seus recursos do Azure contra ataques de DDoS
- Otimize o processo de resposta a incidentes durante ataques de DDoS
- Documente a conformidade de DDoS
- Treine suas equipes de segurança de rede

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Exibir alertas de proteção contra DDoS na central de segurança do Azure

A central de segurança do Azure fornece uma lista de [alertas de segurança](/azure/security-center/security-center-managing-and-responding-alerts), com informações para ajudar a investigar e corrigir problemas. Com esse recurso, você obtém uma exibição unificada de alertas, incluindo alertas relacionados a ataques de DDoS e as ações tomadas para mitigar o ataque em tempo quase futuro.
Há dois alertas específicos que você verá para qualquer detecção e mitigação de ataque de DDoS:

- **Ataque de DDoS detectado para IP público**: esse alerta é gerado quando o serviço de proteção contra DDoS detecta que um de seus endereços IP públicos é o destino de um ataque de DDoS.
- **Ataque de DDoS mitigado para IP público**: esse alerta é gerado quando um ataque no endereço IP público é mitigado.
Para exibir os alertas, abra a **central de segurança** no portal do Azure. Em **proteção contra ameaças**, selecione **alertas de segurança**. A captura de tela a seguir mostra um exemplo dos alertas de ataque de DDoS.

![Alerta de DDoS na central de segurança do Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geográfica e contra ameaças e etapas de correção.

## <a name="permissions-and-restrictions"></a>Permissões e restrições

Para trabalhar com os planos de proteção DDoS, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as ações apropriadas listadas na tabela a seguir:

| Ação                                            | Nome                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Ler um plano de proteção DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Criar ou atualizar um plano de proteção DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Excluir um plano de proteção contra DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Participar de um plano de proteção DDoS              |

Para habilitar a proteção DDoS a uma rede virtual, sua conta deve também ser atribuída a ações [apropriadas para redes virtuais](manage-virtual-network.md#permissions).

### <a name="azure-policy"></a>Azure Policy
Para clientes que têm várias assinaturas e que desejam garantir que um único plano para a proteção contra DDoS do Azure Standard esteja implantado em seu locatário para controle de custo, você pode usar Azure Policy para [restringir a criação de planos padrão de proteção contra DDoS do Azure](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). Essa política bloqueará a criação de qualquer plano de DDoS, a menos que a assinatura tenha sido marcada anteriormente como uma exceção. Essa política também mostrará uma lista de todas as assinaturas que têm um plano de DDoS implantado, mas não devem, marcá-las como fora de conformidade. 

## <a name="next-steps"></a>Próximas etapas

- Criar e atribuir [Azure Policy definições](policy-samples.md) para redes virtuais
