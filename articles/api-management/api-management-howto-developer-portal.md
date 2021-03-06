---
title: Visão geral do portal do desenvolvedor de gerenciamento de API do Azure
titleSuffix: Azure API Management
description: Saiba mais sobre o portal do desenvolvedor no gerenciamento de API. O portal do desenvolvedor é onde os consumidores podem encontrar suas APIs.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/28/2020
ms.author: apimpm
ms.openlocfilehash: 3642b95f5bd6d0207508ca85f1d22ce20b44eae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715463"
---
# <a name="azure-api-management-developer-portal-overview"></a>Visão Geral do portal do desenvolvedor do Gerenciamento de API do Azure

O portal do desenvolvedor é um site da Web, totalmente personalizado e gerado com a documentação de suas APIs. É aí que os consumidores de API podem descobrir suas APIs, saber como usá-las, solicitar acesso e experimentá-las.

Este artigo descreve as diferenças entre as versões autohospedadas e gerenciadas do portal do desenvolvedor no gerenciamento de API. Ele também explica sua arquitetura e fornece respostas para perguntas frequentes.

![Portal do desenvolvedor do Gerenciamento da API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> <a name="migrate-from-legacy"></a> O novo portal do desenvolvedor é incompatível com o portal do desenvolvedor herdado e a migração automatizada não é possível. Você precisa recriar manualmente o conteúdo (páginas, texto, arquivos de mídia) e personalizar a aparência do novo Portal. Consulte [o tutorial portal do desenvolvedor](api-management-howto-developer-portal-customize.md) para obter diretrizes.

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a> Versões gerenciadas e auto-hospedadas

Você pode criar seu portal do desenvolvedor de duas maneiras:

- **Versão gerenciada** – editando e personalizando o portal, que é incorporado à sua instância de gerenciamento de API e pode ser acessado por meio da URL `<your-api-management-instance-name>.developer.azure-api.net` . Consulte [Este artigo de documentação](api-management-howto-developer-portal-customize.md) para saber como acessar e personalizar o portal gerenciado.
- **Versão hospedada internamente** – Implantando e hospedando internamente seu portal fora de uma instância de gerenciamento de API. Essa abordagem permite que você edite a base de código do portal e estenda a funcionalidade básica fornecida, por exemplo, implementar widgets personalizados para integrações com sistemas de terceiros. Nesse cenário, você é o mantenedor do portal e é responsável por atualizar o portal para a versão mais recente. Para obter detalhes e instruções, consulte o [repositório do GitHub com o código-fonte do portal][1] e [o tutorial sobre como implementar um widget][3]. O [tutorial para a versão gerenciada](api-management-howto-developer-portal-customize.md) percorre o painel administrativo do portal, que é comum para as versões gerenciadas e hospedadas internamente.

## <a name="portal-architectural-concepts"></a>Conceitos de arquitetura do portal

Os componentes do portal podem ser divididos logicamente em duas categorias: *código* e *conteúdo*.

O *código* é mantido no [repositório do GitHub][1] e inclui:

- Widgets – que representam elementos visuais e combinam HTML, JavaScript, capacidade de estilo, configurações e mapeamento de conteúdo. Os exemplos são uma imagem, um parágrafo de texto, um formulário, uma lista de APIs etc.
- Definições de estilo – que especificam como os widgets podem ser estilizados
- Mecanismo – que gera páginas da Web estáticas do conteúdo do portal e é escrito em JavaScript
- Editor visual – que permite a experiência de criação e personalização no navegador

O *conteúdo* é dividido em duas subcategorias: conteúdo do *portal* e conteúdo de *Gerenciamento de API*.

O *conteúdo do portal* é específico para o portal e inclui:

- Páginas-por exemplo, página de aterrissagem, tutoriais de API, Postagens de blog
- Mídia-imagens, animações e outros conteúdos com base em arquivo
- Layouts-modelos, que são correspondidos em uma URL e definem como as páginas são exibidas
- Estilos – valores para definições de estilo, por exemplo, fontes, cores, bordas
- Configurações-configuração, por exemplo, favicon, metadados do site

O *conteúdo do portal*, exceto para mídia, é expresso como documentos JSON.

O *conteúdo de gerenciamento de API* inclui entidades como APIs, operações, produtos, assinaturas.

O portal é baseado em uma bifurcação adaptada da [estrutura Paperbits](https://paperbits.io/). A funcionalidade original do Paperbits foi estendida para fornecer widgets específicos de gerenciamento de API (por exemplo, uma lista de APIs, uma lista de produtos) e um conector para o serviço de gerenciamento de API para salvar e recuperar conteúdo.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Perguntas frequentes

Nesta seção, respondemos a perguntas comuns sobre o portal do desenvolvedor, que são de natureza geral. Para perguntas específicas para a versão hospedada internamente, consulte [a seção wiki do repositório do GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Como posso migrar da versão de visualização do portal?

Quando você iniciou pela primeira vez a versão de visualização do portal do desenvolvedor, provisionou a versão de visualização de seu conteúdo padrão no serviço de gerenciamento de API. O conteúdo padrão foi significativamente modificado na versão disponível para o público geral. Por exemplo, a versão de visualização do conteúdo padrão não inclui botões do OAuth nas páginas de logon, ele usa widgets diferentes para exibir APIs e depende de recursos limitados para estruturar páginas do portal do desenvolvedor. Mesmo que haja diferenças no conteúdo, o mecanismo do portal (incluindo widgets subjacentes) é atualizado automaticamente toda vez que você publica o portal do desenvolvedor.

Se você tiver personalizado bastante seu portal com base na versão de visualização do conteúdo, poderá continuar a usá-lo como está e posicionar novos widgets manualmente nas páginas do Portal. Caso contrário, é recomendável substituir o conteúdo do portal pelo novo conteúdo padrão.

Para redefinir o conteúdo em um portal gerenciado, clique em **Redefinir Conteúdo** na seção do menu **operações** . Esta operação removerá todo o conteúdo do portal e provisionar o novo conteúdo padrão. Você perderá todas as personalizações e alterações do portal do desenvolvedor. **Não é possível desfazer esta ação**.

![Redefinir o conteúdo do portal](media/api-management-howto-developer-portal/reset-content.png)

Se você estiver usando a versão hospedada automaticamente, execute `scripts.v2/cleanup.bat` e `scripts.v2/generate.bat` scripts do repositório GitHub para remover o conteúdo existente e provisionar novo conteúdo. Certifique-se de atualizar o código do portal para a versão mais recente do repositório do GitHub com antecedência.

Se você tiver acessado o portal pela primeira vez após o anúncio de disponibilidade geral em novembro de 2019, ele já deverá apresentar o novo conteúdo padrão e nenhuma ação adicional será necessária.

### <a name="does-the-portal-have-all-the-features-of-the-legacy-portal"></a>O portal tem todos os recursos do portal herdado?

O portal do desenvolvedor não dá mais suporte a *aplicativos*, *problemas*e à integração direta com o Facebook, Microsoft, Twitter e Google como provedores de identidade (você pode usar Azure ad B2C em vez disso).

### <a name="has-the-legacy-portal-been-deprecated"></a>O portal herdado foi preterido?

Os portais herdados de desenvolvedor e publicador agora são recursos *herdados* ; eles receberão apenas atualizações de segurança. Novos recursos serão implementados somente no novo portal do desenvolvedor.

A substituição dos portais herdados será anunciada separadamente. Se você tiver dúvidas, preocupações ou comentários, gere-os [em um problema dedicado do GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>A funcionalidade necessária não tem suporte no portal

Você pode abrir uma [solicitação de recurso](https://aka.ms/apimwish) ou [implementar a funcionalidade ausente por conta própria][3]. Se você você implementar a funcionalidade por conta própria, poderá hospedar o portal do desenvolvedor por conta própria ou abrir uma solicitação de pull no GitHub para incluir as alterações na versão gerenciada.

### <a name="how-can-i-automate-portal-deployments"></a>Como posso automatizar as implantações do portal?

Você pode acessar e gerenciar programaticamente o conteúdo do portal do desenvolvedor por meio da API REST, independentemente de estar usando uma versão gerenciada ou hospedada internamente.

A API está documentada na [seção wiki do repositório do GitHub][2]. Ele pode ser usado para automatizar migrações de conteúdo do portal entre ambientes, por exemplo, de um ambiente de teste para o ambiente de produção. Você pode saber mais sobre esse processo [neste artigo de documentação](https://aka.ms/apimdocs/migrateportal) sobre o github.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Como fazer mudar do gerenciado para a versão hospedada internamente?

Consulte o artigo detalhado na [seção wiki do repositório do portal do desenvolvedor no GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Posso ter vários portais de desenvolvedor em um serviço de gerenciamento de API?

Você pode ter um portal gerenciado e vários portais hospedados internamente. O conteúdo de todos os portais é armazenado no mesmo serviço de gerenciamento de API, portanto, será idêntico. Se você quiser diferenciar a aparência e a funcionalidade dos portais, poderá hospedá-los internamente com seus próprios widgets personalizados que personalizam dinamicamente as páginas no tempo de execução, por exemplo, com base na URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal dá suporte a modelos de Azure Resource Manager e/ou é compatível com o DevOps Resource Kit de gerenciamento de API?

Não.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>O conteúdo do portal foi salvo com a funcionalidade de backup/restauração no gerenciamento de API?

Não.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>É necessário habilitar a conectividade VNet adicional para as dependências do portal gerenciado?

Na maioria dos casos, não.

Se o serviço de gerenciamento de API estiver em uma VNet interna, o portal do desenvolvedor só poderá ser acessado de dentro da rede. O nome do host do ponto de extremidade de gerenciamento deve ser resolvido para o VIP interno do serviço do computador que você usa para acessar a interface administrativa do Portal. Verifique se o ponto de extremidade de gerenciamento está registrado no DNS. No caso de uma configuração incorreta, você verá um erro: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Se o serviço de gerenciamento de API estiver em uma VNet interna e você o estiver acessando por meio do gateway de aplicativo da Internet, certifique-se de habilitar a conectividade com o portal do desenvolvedor e os pontos de extremidade de gerenciamento do gerenciamento de API.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Atribuí um domínio de gerenciamento de API personalizado e o portal publicado não funciona

Depois de atualizar o domínio, você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Adicionei um provedor de identidade e não consigo vê-lo no portal

Depois de configurar um provedor de identidade (por exemplo, AAD, AAD B2C), você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Configurei a delegação e o portal não a utiliza

Depois de configurar a delegação, você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Minhas outras alterações de configuração de gerenciamento de API não foram propagadas no portal do desenvolvedor

A maioria das alterações de configuração (por exemplo, VNet, entrada e termos do produto) requer [a republicação do portal](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Estou recebendo um erro de CORS ao usar o console interativo

O console interativo faz uma solicitação de API do lado do cliente do navegador. Resolva o problema de CORS adicionando [uma política de CORS](api-management-cross-domain-policies.md#CORS) em suas API (s).

Você pode verificar o status da política de CORS na seção **visão geral do portal** do serviço de gerenciamento de API no portal do Azure. Uma caixa de aviso indica uma política ausente ou configurada incorretamente.

![Portal do desenvolvedor do Gerenciamento da API](media/api-management-howto-developer-portal/cors-azure-portal.png)

Aplique automaticamente a política CORS clicando no botão **habilitar CORS** .

Você também pode habilitar o CORS manualmente.

1. Clique no link **aplicar manualmente no nível global** para ver o código de política gerado.
2. Navegue até **todas as APIs** na seção **APIs** do serviço de gerenciamento de API no portal do Azure.
3. Clique no **</>** ícone na seção **processamento de entrada** .
4. Insira a política na **<inbound>** seção do arquivo XML. Verifique se o **<origin>** valor corresponde ao domínio do seu portal do desenvolvedor.

> [!NOTE]
> 
> Se você aplicar a política CORS no escopo do produto, em vez do escopo de API (s), e sua API usar a autenticação de chave de assinatura por meio de um cabeçalho, o console não funcionará.
>
> O navegador emite automaticamente uma solicitação HTTP OPTIONS, que não contém um cabeçalho com a chave de assinatura. Devido à chave de assinatura ausente, o gerenciamento de API não pode associar a chamada de opções a um produto, portanto, ele não pode aplicar a política CORS.
>
> Como alternativa, você pode passar a chave de assinatura em um parâmetro de consulta.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quais permissões preciso para editar o portal do desenvolvedor?

Se você estiver vendo o `Oops. Something went wrong. Please try again later.` erro ao abrir o portal no modo administrativo, talvez você não esteja perdendo as permissões necessárias (RBAC do Azure).

Os portais herdados exigiam a permissão `Microsoft.ApiManagement/service/getssotoken/action` no escopo do serviço ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ) para permitir que o administrador do usuário acesse os portais. O novo portal requer a permissão `Microsoft.ApiManagement/service/users/token/action` no escopo `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

Você pode usar o seguinte script do PowerShell para criar uma função com a permissão necessária. Lembre-se de alterar o `<subscription-id>` parâmetro. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Depois que a função é criada, ela pode ser concedida a qualquer usuário da seção de **controle de acesso (iam)** no portal do Azure. A atribuição dessa função a um usuário atribuirá a permissão no escopo do serviço. O usuário poderá gerar tokens SAS em nome de *qualquer* usuário no serviço. No mínimo, essa função precisa ser atribuída ao administrador do serviço. O comando do PowerShell a seguir demonstra como atribuir a função a um usuário `user1` no escopo mais baixo para evitar a concessão de permissões desnecessárias ao usuário: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Depois que as permissões tiverem sido concedidas a um usuário, o usuário deverá sair e entrar novamente no portal do Azure para que as novas permissões entrem em vigor.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Estou vendo o `Unable to start the portal. See if settings are specified correctly (...)` erro

Esse erro é mostrado quando uma `GET` chamada a `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` falha. A chamada é emitida pelo navegador pela interface administrativa do Portal.

Se o serviço de gerenciamento de API estiver em uma VNet, consulte a pergunta de conectividade de VNet acima.

A falha de chamada também pode ser causada por um certificado TLS/SSL, que é atribuído a um domínio personalizado e não é confiável para o navegador. Como uma mitigação, você pode remover o ponto de extremidade de gerenciamento domínio personalizado-o gerenciamento de API voltará ao ponto de extremidade padrão com um certificado confiável.

### <a name="whats-the-browser-support-for-the-portal"></a>Qual é o suporte do navegador para o portal?

| Navegador                     | Com suporte       |
|-----------------------------|-----------------|
| Apple Safari                | Sim<sup>1</sup> |
| Google Chrome               | Sim<sup>1</sup> |
| Microsoft Edge              | Sim<sup>1</sup> |
| Microsoft Internet Explorer | Não              |
| Mozilla Firefox             | Sim<sup>1</sup> |

 <small><sup>1</sup> com suporte nas duas versões de produção mais recentes.</small>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o novo portal do desenvolvedor:

- [Acessar e personalizar o portal do desenvolvedor gerenciado](api-management-howto-developer-portal-customize.md)
- [Configurar a versão hospedada internamente do portal][2]
- [Implementar seu próprio widget][3]

Procurar outros recursos:

- [Repositório GitHub com o código-fonte][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
