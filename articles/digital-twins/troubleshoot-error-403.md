---
title: 'Falha na solicitação do Azure digital gêmeos com o status: 403 (proibido)'
description: "As causas e resoluções para ' solicitação de serviço falharam. Status: 403 (proibido) ' no gêmeos digital do Azure."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: 86fd6a5d7ca1cb9c828a4ad095720f1664b82caa
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201402"
---
# <a name="service-request-failed-status-403-forbidden"></a>Falha na solicitação de serviço. Status: 403 (proibido)

Este artigo descreve as causas e as etapas de resolução para receber um erro 403 de solicitações de serviço para o Azure digital gêmeos. 

## <a name="symptoms"></a>Sintomas

Esse erro pode ocorrer em muitos tipos de solicitações de serviço que exigem autenticação. O efeito é que a solicitação de API falha, retornando um status de erro de `403 (Forbidden)` .

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Causa #1

Geralmente, esse erro indica que suas permissões de RBAC (controle de acesso baseado em função) para o serviço não estão configuradas corretamente. Muitas ações para uma instância do gêmeos digital do Azure exigem que você tenha a função de *proprietário do gêmeos digital do Azure (versão prévia)* **na instância que você está tentando gerenciar**. 

### <a name="cause-2"></a>Causa #2

Se você estiver usando um aplicativo cliente para se comunicar com o Azure digital gêmeos que está se Autenticando com um [registro de aplicativo](how-to-create-app-registration.md), esse erro pode acontecer porque o registro do aplicativo não tem permissões configuradas para o serviço gêmeos do Azure digital.

O registro do aplicativo deve ter permissões de acesso configuradas para as APIs do Azure digital gêmeos. Em seguida, quando o aplicativo cliente for autenticado no registro do aplicativo, ele receberá as permissões configuradas pelo registro do aplicativo.

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>#1 da solução

A primeira solução é verificar se o usuário do Azure tem a função de _**proprietário (versão prévia) do gêmeos digital do Azure**_ na instância que você está tentando gerenciar. Se você não tiver essa função, configure-a.

Observe que essa função é diferente de...
* a função de *proprietário* em toda a assinatura do Azure. O *proprietário do gêmeos digital do Azure (versão prévia)* é uma função no gêmeos digital do Azure e tem o escopo definido para essa instância de gêmeos digital do Azure individual.
* a função de *proprietário* no gêmeos digital do Azure. Essas são duas funções de gerenciamento de gêmeos digitais do Azure diferentes e o *proprietário do gêmeos digital do Azure (versão prévia)* é a função que deve ser usada para gerenciamento durante a visualização.

#### <a name="check-current-setup"></a>Verificar a configuração atual

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Corrigir problemas 

Se você não tiver essa atribuição de função, alguém com uma função de proprietário em sua **assinatura do Azure** deverá executar o comando a seguir para dar ao usuário do Azure a função de *proprietário (versão prévia) do Azure digital gêmeos* na **instância do gêmeos digital do Azure**. 

Se você for um proprietário na assinatura, poderá executar esse comando por conta própria. Se não estiver, entre em contato com um proprietário para executar esse comando em seu nome.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Para obter mais detalhes sobre esse requisito de função e o processo de atribuição, consulte a [seção *configurar permissões de acesso do usuário* ](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) de *como: configurar uma instância e autenticação (CLI ou Portal)*.

Se você já tiver essa atribuição de função *e* estiver usando um registro de aplicativo do Azure ad para autenticar um aplicativo cliente, poderá continuar para a próxima solução se essa solução não resolver o problema 403.

### <a name="solution-2"></a>#2 da solução

Se você estiver usando um registro de aplicativo do Azure AD para autenticar um aplicativo cliente, a segunda solução possível é verificar se o registro do aplicativo tem permissões configuradas para o serviço gêmeos digital do Azure. Se eles não estiverem configurados, configure-os.

#### <a name="check-current-setup"></a>Verificar a configuração atual

Para verificar se as permissões foram configuradas corretamente, navegue até a [página Visão geral do registro de aplicativo do Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) na portal do Azure. Você mesmo pode acessar essa página procurando *registros de aplicativo* na barra de pesquisa do Portal.

Alterne para a guia *todos os aplicativos* para ver todos os registros de aplicativo que foram criados em sua assinatura.

Você deve ver o registro do aplicativo que acabou de criar na lista. Selecione-o para abrir seus detalhes.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Registros de aplicativo página no portal do Azure":::

Primeiro, verifique se as configurações de permissões do gêmeos digital do Azure foram definidas corretamente no registro. Para fazer isso, selecione *manifesto* na barra de menus para exibir o código do manifesto do registro do aplicativo. Role até a parte inferior da janela de código e procure esses campos em `requiredResourceAccess` . Os valores devem corresponder aos da captura de tela abaixo:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Registros de aplicativo página no portal do Azure":::

Em seguida, selecione *permissões de API* na barra de menus para verificar se este registro de aplicativo contém permissões de leitura/gravação para o gêmeos digital do Azure. Você deverá ver uma entrada como esta:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Registros de aplicativo página no portal do Azure":::

#### <a name="fix-issues"></a>Corrigir problemas

Se qualquer um deles aparecer de forma diferente do descrito, siga as instruções sobre como configurar um registro de aplicativo em [*como criar um registro de aplicativo*](how-to-create-app-registration.md).

## <a name="next-steps"></a>Próximas etapas

Leia as etapas de configuração para criar e autenticar uma nova instância de gêmeos digital do Azure:
* [*Como: configurar uma instância e autenticação (CLI)*](how-to-set-up-instance-cli.md)

Leia mais sobre segurança e permissões no Azure digital gêmeos:
* [*Conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md)