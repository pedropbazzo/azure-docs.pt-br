---
title: Introdução à sua conta de cobrança do Azure atualizada
description: Veja a introdução à sua conta de cobrança do Azure atualizada para entender as alterações à nova experiência de gerenciamento de custos e cobrança
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: banders
ms.openlocfilehash: fce0ca7df3851aa0e96b6ee630a4a1b0b85eb070
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371732"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Introdução à sua conta de cobrança do Azure atualizada

O gerenciamento de custos e faturas é um dos principais componentes da sua experiência de nuvem. Ele ajuda você a controlar e a compreender seus gastos na nuvem. Para facilitar o gerenciamento de seus custos e faturas, a Microsoft está atualizando sua conta de cobrança do Azure para incluir recursos aprimorados de cobrança e gerenciamento de custos. Este artigo descreve as atualizações à sua conta de cobrança e orienta você pelos novos recursos.

> [!IMPORTANT]
> Sua conta será atualizada quando você receber um email da Microsoft notificando sobre as atualizações à sua conta. A notificação é enviada 60 dias antes da atualização da sua conta.

## <a name="more-flexibility-with-your-new-billing-account"></a>Mais flexibilidade com sua nova conta de cobrança

O seguinte diagrama compara sua conta de cobrança antiga com a nova:

![Diagrama mostrando a comparação entre a hierarquia de cobrança nas contas antiga e nova](./media/mosp-new-customer-experience/comparison-old-new-account.png)

Sua nova conta de cobrança contém um ou mais perfis de cobrança que permitem que você gerencie suas notas fiscais e formas de pagamento. Cada perfil de cobrança contém uma ou mais seções de fatura que permitem organizar os custos na fatura do perfil de cobrança.

![Diagrama mostrando a nova hierarquia de cobrança](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

As funções na conta de cobrança têm o nível mais alto de permissões. Essas funções devem ser atribuídas a usuários que precisam exibir faturas e controlar os custos de toda a conta, como os gerentes de finanças ou de TI de uma organização ou o indivíduo que se inscreveu em uma conta. Para saber mais, confira [funções e tarefas da conta de cobrança](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Quando sua conta é atualizada, o usuário que tem uma função de administrador de conta na conta de cobrança antiga recebe uma função de proprietário na nova conta.

## <a name="billing-profiles"></a>Perfis de cobrança

Um perfil de cobrança é usado para gerenciar suas faturas e formas de pagamento. Uma fatura mensal é gerada no início do mês para cada perfil de cobrança de sua conta. A fatura contém os respectivos encargos do mês anterior para todas as assinaturas associadas ao perfil de cobrança.

Quando sua conta for atualizada, um perfil de cobrança será criado automaticamente para cada assinatura. Os encargos da assinatura são cobrados em seu respectivo perfil de cobrança e exibidos em sua fatura.

As funções nos perfis de cobrança têm permissões para exibir e gerenciar faturas e métodos de pagamento. Essas funções devem ser atribuídas a usuários que pagam faturas como membros da equipe de contabilidade em uma organização. Para saber mais, confira [funções e tarefas do perfil de cobrança](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). 

Quando sua conta for atualizada, para cada assinatura na qual você concedeu a permissão para [exibir faturas](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice), os usuários que tiverem uma função de proprietário, colaborador, leitor ou leitor de cobrança de RBAC do Azure receberão a função leitor no respectivo perfil de cobrança.

## <a name="invoice-sections"></a>Seções da fatura

Uma seção da fatura é usada para organizar os custos em sua fatura. Por exemplo, você pode precisar de uma única fatura, mas deseja organizar os custos por departamento, equipe ou projeto. Para este cenário, você tem um único perfil de cobrança no qual cria uma seção de fatura para cada departamento, equipe ou projeto.

Quando sua conta é atualizada, uma seção de fatura é criada para cada perfil de cobrança e a assinatura relacionada é atribuída à seção fatura. Ao adicionar mais assinaturas, você pode criar seções adicionais e atribuir as assinaturas às seções faturas. Você verá as seções na fatura do perfil de cobrança, refletindo o uso de cada assinatura que você atribuiu a ela.

As funções na seção da fatura têm permissões para controlar quem cria assinaturas do Azure. As funções devem ser atribuídas a usuários que configuram o ambiente do Azure para equipes em uma organização, como clientes potenciais de engenharia e arquitetos técnicos. Para obter mais informações, confira [funções e tarefas da seção da fatura](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Recursos aprimorados em sua nova experiência

Sua nova experiência inclui os seguintes recursos de cobrança e gerenciamento de custos que facilitam o gerenciamento de seus custos e faturas:

#### <a name="invoice-management"></a>Gerenciamento de faturas

**Período de cobrança mensal mais previsível** – em sua nova conta, o período de cobrança começa no primeiro dia do mês e termina no último dia do mês, independentemente de quando você se inscreve para usar o Azure. Uma fatura será gerada no início de cada mês e conterá todos os encargos do mês anterior.

**Obtenha uma única fatura para várias assinaturas** – você tem a flexibilidade de obter uma fatura para cada uma de suas assinaturas ou uma única fatura para várias assinaturas.

**Receba uma fatura mensal única para assinaturas do Azure, planos de suporte e produtos do Azure Marketplace** – você obterá uma fatura mensal para todos os encargos, incluindo encargos de uso para assinaturas do Azure e planos de suporte e compras do Azure Marketplace.

**Agrupar custos na fatura conforme suas necessidades** – você pode agrupar os custos em sua fatura conforme suas necessidades – por departamentos, projetos ou equipes.

**Defina um número de ordem de compra opcional na fatura** – para associar sua fatura aos seus sistemas financeiros internos, defina um número de ordem de compra. Gerencie e atualize-o a qualquer momento no portal do Azure.

#### <a name="payment-management"></a>Gerenciamento de pagamento

**Pague faturas imediatamente usando um cartão de crédito** – não há necessidade de aguardar o pagamento automático ser cobrado no cartão de crédito. Você pode usar qualquer cartão de crédito para pagar uma fatura de conclusão ou vencida no portal do Azure.

**Mantenha o controle de todos os pagamentos aplicados à conta** – exiba todos os pagamentos aplicados à sua conta, incluindo a forma de pagamento usada, o valor pago e a data de pagamento na portal do Azure.

#### <a name="cost-management"></a>Gerenciamento de custos

**Agende exportações mensais de dados de uso para uma conta de armazenamento** – publique automaticamente seus dados de custo e de uso para uma conta de armazenamento a cada dia, semana ou mês.

#### <a name="account-and-subscription-management"></a>Gerenciamento de assinatura e conta

**Atribua vários administradores para executar operações de cobrança** – atribua permissões de cobrança a vários usuários para gerenciar a cobrança da sua conta. Obtenha flexibilidade fornecendo permissões de leitura, gravação ou ambas a outras pessoas.

**Crie assinaturas adicionais diretamente no portal do Azure** – crie todas as suas assinaturas com um único clique no portal do Azure.

#### <a name="api-support"></a>Suporte a API

**Realize operações de gerenciamento de custos e cobrança por meio de APIs, SDK e PowerShell** – use as APIs de gerenciamento de custos, cobrança e consumo para extrair dados de cobrança e custo em suas ferramentas de análise de dados preferenciais.

**Execute todas as operações de assinatura por meio de APIs, SDK e PowerShell** – use as APIs de assinatura do Azure para automatizar o gerenciamento de suas assinaturas do Azure, incluindo a criação, a renomeação e o cancelamento de uma assinatura.

## <a name="get-prepared-for-your-new-experience"></a>Prepare-se para sua nova experiência

Recomendamos o seguinte para preparar-se para sua nova experiência:

**Período de cobrança mensal e data da fatura diferente**

Na nova experiência, sua fatura será gerada em torno do nono dia de cada mês e conterá todas as cobranças do mês anterior. Essa data pode ser diferente da data em que a fatura é gerada na conta antiga. Se você compartilhar suas faturas com outras pessoas, notifique-as sobre a alteração na data.

**Novas APIs de cobrança e gerenciamento de custos**

Se você estiver usando APIs de Cobrança ou Gerenciamento de Custos para consultar e atualizar seus dados de cobrança ou de custo, deverá usar novas APIs. A tabela a seguir lista as APIs que não funcionarão com a nova conta de cobrança e as alterações que você precisa fazer à sua nova conta de cobrança.

|API | Alterações  |
|---------|---------|
|[Contas de cobrança – Lista](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/billingaccounts/list) | Na API de Contas de Cobrança – Lista, sua conta de cobrança antiga tem agreementType **MicrosoftOnlineServiceProgram**, sua nova conta de cobrança terá agreementType **MicrosoftCustomerAgreement**. Se você assumir uma dependência de agreementType, atualize-a. |
|[Faturas – lista por assinatura para cobrança](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Essa API retornará apenas as faturas geradas antes da atualização da sua conta. Você precisar usar a API [Faturas – lista por conta de cobrança](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) para obter faturas geradas em sua nova conta de cobrança. |

## <a name="additional-information"></a>Informações adicionais

As seções a seguir fornecem informações adicionais sobre sua nova experiência.

**Nenhum tempo de inatividade de serviço** Os serviços do Azure em sua assinatura continuarão em execução sem nenhuma interrupção. A única atualização é à sua experiência de cobrança. Não haverá impacto aos recursos, grupos de recursos ou grupos de gerenciamento existentes.

**Nenhuma alteração nos recursos do Azure** O acesso aos recursos do Azure que foram definidos usando o Azure RBAC (controle de acesso baseado em função) não é afetado pela atualização.

**As faturas anteriores estão disponíveis na nova experiência** As faturas geradas antes da atualização da sua conta ainda estão disponíveis no portal do Azure.

**Faturas de conta atualizadas no meio do mês** Se a sua conta for atualizada no meio do mês, você obterá uma fatura para cobranças acumuladas até o dia em que sua conta for atualizada. Você receberá outra fatura pelo restante do mês. Por exemplo, sua conta tem uma assinatura e é atualizada em 15 de setembro. Você receberá uma fatura para cobranças acumuladas até 15 de setembro. Você receberá outra fatura pelo período entre 15 de setembro a 30 de setembro. Após setembro, você receberá uma fatura por mês.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

Confira os artigos a seguir para saber mais sobre a sua conta de cobrança.

- [Entenda as funções administrativas da sua nova conta de cobrança](../manage/understand-mca-roles.md)
- [Criar uma assinatura adicional do Azure para sua conta de cobrança](../manage/create-subscription.md)
- [Criar seções em sua fatura para organizar os custos](../manage/mca-section-invoice.md)
