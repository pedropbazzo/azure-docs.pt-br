---
title: Saiba como proteger o acesso aos dados no Azure Cosmos DB
description: Saiba mais sobre os conceitos de controle de acesso no Azure Cosmos DB, incluindo chaves primárias, chaves somente leitura, usuários e permissões.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 30444523bfc26fc0f4eb410957bcc9ee46aff725
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760862"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Proteger o acesso aos dados no Azure Cosmos DB

Este artigo fornece uma visão geral de como proteger o acesso aos dados armazenados no [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

O Azure Cosmos DB usa dois tipos de chaves para autenticar usuários e fornecer acesso aos seus dados e recursos. 

|Tipo de chave|Recursos|
|---|---|
|[Chaves primárias](#primary-keys) |Usadas para os recursos administrativos: contas de bancos de dados, bancos de dados, usuários e permissões|
|[Tokens de recurso](#resource-tokens)|Usado para recursos de aplicativo: contêineres, documentos, anexos, procedimentos armazenados, gatilhos e UDFs|

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Chaves primárias

As chaves primárias fornecem acesso a todos os recursos administrativos da conta do banco de dados. Chaves primárias:

- Fornecem acesso a contas, a bancos de dados, a usuários e a permissões. 
- Não pode ser usado para fornecer acesso granular a contêineres e documentos.
- São criadas durante a criação de uma conta.
- Podem ser geradas novamente a qualquer momento.

Cada conta consiste em duas chaves primárias: uma chave primária e uma chave secundária. A finalidade das chaves duplas é para que você possa gerar novamente, ou reverter as chaves, fornecendo acesso contínuo à sua conta e dados.

Além das duas chaves primárias para a conta de Cosmos DB, há duas chaves somente leitura. Essas chaves somente leitura só permitem operações de leitura na conta. As chaves somente leitura não fornecem acesso a recursos com permissões de leitura.

Chaves primárias primárias, secundárias, somente leitura e de leitura/gravação podem ser recuperadas e geradas novamente usando o portal do Azure. Para obter instruções, veja [Exibir, copiar e gerar novamente as chaves de acesso](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controle de acesso (IAM) no Portal do Azure - demonstrando a segurança do banco de dados NoSQL":::

### <a name="key-rotation"></a>Rotação de chaves<a id="key-rotation"></a>

O processo de rotação de sua chave primária é simples. 

1. Navegue até o portal do Azure para recuperar a chave secundária.
2. Substitua sua chave primária pela sua chave secundária em seu aplicativo. Certifique-se de que todos os clientes Cosmos DB em todas as implantações sejam reiniciados imediatamente e começarão a usar a chave atualizada.
3. Gire a chave primária no portal do Azure.
4. Valide se a nova chave primária funciona em todos os recursos. O processo de rotação de chaves pode levar qualquer ponto abaixo de um minuto a horas, dependendo do tamanho da conta de Cosmos DB.
5. Substitua a chave secundária pela nova chave primária.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Controle de acesso (IAM) no Portal do Azure - demonstrando a segurança do banco de dados NoSQL" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Exemplo de código para usar uma chave primária

O exemplo de código a seguir ilustra como usar um ponto de extremidade de conta Cosmos DB e uma chave primária para instanciar um DocumentClient e criar um banco de dados:

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

O exemplo de código a seguir ilustra como usar o ponto de extremidade da conta do Azure Cosmos DB e a chave primária para criar uma instância de um `CosmosClient` objeto:

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>Tokens de recurso <a id="resource-tokens"></a>

Os tokens de recurso fornecem acesso aos recursos do aplicativo em um banco de dados. Tokens de recurso:

- Fornece acesso a contêineres, chaves de partição, documentos, anexos, procedimentos armazenados, gatilhos e UDFs específicos.
- São criados quando um [usuário](#users) recebe [permissões](#permissions) para um recurso específico.
- São recriados quando um recurso de permissão recebe uma ação de uma chamada POST, GET ou PUT.
- Use um token de recurso de hash construído especificamente para o usuário, o recurso e a permissão.
- São associados a um período de validade personalizável. O intervalo de tempo válido padrão é de uma hora. O tempo de vida do token, no entanto, pode ser especificado explicitamente, até o máximo de cinco horas.
- Forneça uma alternativa segura para fornecer a chave primária.
- Permitem aos clientes ler, gravar e excluir recursos da conta do Cosmos DB de acordo com as permissões que receberam.

Você pode usar um token de recurso (criando Cosmos DB usuários e permissões) quando desejar fornecer acesso aos recursos em sua conta de Cosmos DB para um cliente que não pode ser confiável com a chave primária.  

Cosmos DB tokens de recurso fornecem uma alternativa segura que permite que os clientes leiam, gravem e excluam recursos em sua conta de Cosmos DB de acordo com as permissões que você concedeu e sem a necessidade de uma chave primária ou somente leitura.

Este é um padrão de design típico no qual tokens de recurso podem ser solicitados, gerados e fornecidos aos clientes:

1. Um serviço de camada intermediária é configurado para atender a um aplicativo móvel de compartilhamento de fotos do usuário.
2. O serviço de camada intermediária possui a chave primária da conta de Cosmos DB.
3. O aplicativo de fotos é instalado em dispositivos móveis de usuários finais.
4. No logon, o aplicativo de fotos estabelece a identidade do usuário com o serviço de camada intermediária. Esse mecanismo de estabelecimento de identidade depende apenas do aplicativo.
5. Depois que a identidade é estabelecida, o serviço de camada intermediária solicita permissões com base na identidade.
6. O serviço de camada intermediária envia um token de recurso de volta para o aplicativo móvel.
7. O aplicativo de telefone pode continuar usando o token de recurso para acessar diretamente recursos do Cosmos DB com as permissões definidas pelo token de recurso e no intervalo permitido por ele.
8. Quando o token de recurso expira, as solicitações seguintes recebem uma exceção 401 de não autorizado.  Nesse ponto, o aplicativo móvel restabelece a identidade e solicita um novo token de recurso.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Controle de acesso (IAM) no Portal do Azure - demonstrando a segurança do banco de dados NoSQL" border="false":::

A geração e o gerenciamento do token de recurso são manipulados pelas bibliotecas de cliente nativas do Cosmos DB; no entanto, se você usar a REST, deverá construir os cabeçalhos de solicitação/autenticação. Para obter mais informações sobre como criar cabeçalhos de autenticação para REST, consulte [controle de acesso em Cosmos DB recursos](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) ou o código-fonte para nosso [SDK do .net](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) ou [ SDK doNode.js](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Para obter um exemplo de um serviço de camada intermediária usado para gerar, ou tokens de recurso do agente, confira o [aplicativo ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## <a name="users"></a>Usuários<a id="users"></a>

Azure Cosmos DB usuários estão associados a um banco de dados Cosmos.  Cada banco de dados pode conter nenhum ou mais usuários do Cosmos DB. O exemplo de código a seguir mostra como criar um usuário Cosmos DB usando o [Azure Cosmos DB SDK do .net v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Cada usuário Cosmos DB tem um método ReadAsync () que pode ser usado para recuperar a lista de [permissões](#permissions) associadas ao usuário.

## <a name="permissions"></a>Permissões<a id="permissions"></a>

Um recurso de permissão é associado a um usuário e atribuído no contêiner, bem como no nível de chave de partição. Cada usuário pode conter zero ou mais permissões. Um recurso de permissão fornece acesso a um token de segurança que o usuário precisa ao tentar acessar um contêiner ou dados específicos em uma chave de partição específica. Há dois níveis de acesso disponíveis que podem ser fornecidos por um recurso de permissão:

- Tudo: o usuário tem permissão total com relação ao recurso.
- Leitura: O usuário pode apenas ler o conteúdo do recurso, mas não pode executar operações de gravação, atualização ou exclusão no recurso.

> [!NOTE]
> Para executar procedimentos armazenados, o usuário deve ter a permissão All no contêiner no qual o procedimento armazenado será executado.

### <a name="code-sample-to-create-permission"></a>Exemplo de código para criar permissão

O exemplo de código a seguir mostra como criar um recurso de permissão, ler o token de recurso do recurso de permissão e associar as permissões ao [usuário](#users) criado anteriormente.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Exemplo de código para ler permissão para o usuário

O trecho de código a seguir mostra como recuperar a permissão associada ao usuário criado acima e criar uma instância de um novo CosmosClient em nome do usuário, com escopo para uma única chave de partição.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Adicionar usuários e atribuir funções

Para adicionar o acesso de leitor de conta do Azure Cosmos DB à sua conta de usuário, peça ao proprietário de uma assinatura que execute as seguintes etapas no portal do Azure.

1. Abra o portal do Azure e selecione sua conta do Azure Cosmos DB.
2. Clique na guia **Controle de acesso (IAM)** e, em seguida, clique em **+ Adicionar atribuição de função**.
3. No painel **Adicionar atribuição de função**, na caixa **Função**, selecione **Função de leitor de conta do Cosmos DB**.
4. Na **caixa atribuir acesso a**, selecione **usuário, grupo ou aplicativo do Azure ad**.
5. Selecione o usuário, o grupo ou o aplicativo no diretório ao qual você deseja conceder acesso.  Você pode pesquisar o diretório por nome para exibição, endereço de email ou identificadores de objeto.
    O usuário, grupo ou aplicativo selecionado aparece na lista de membros selecionados.
6. Clique em **Salvar**.

A entidade agora poderá ler recursos do Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Excluir ou exportar dados do usuário

O Azure Cosmos DB permite que você pesquise, selecione, modifique e exclua todos os dados pessoais localizados no banco de dados ou nas coleções. O Azure Cosmos DB fornece APIs para localizar e excluir dados pessoais, no entanto, é sua responsabilidade usar as APIs e definir a lógica necessária para apagar os dados pessoais. Cada API multimodelo (SQL, MongoDB, Gremlin, Cassandra, tabela) fornece SDKs de linguagens diferentes que contêm métodos para pesquisar e excluir dados pessoais. Você também pode habilitar o recurso [TTL (vida útil)](time-to-live.md) para excluir os dados automaticamente após um período especificado, sem resultar em nenhum custo adicional.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a segurança do banco de dados Cosmos, consulte [segurança do banco de dados do cosmos DB](database-security.md).
- Para saber como construir tokens de autorização do Azure Cosmos DB, consulte [Controle de Acesso em recursos do Azure Cosmos DB](/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
- Exemplos de gerenciamento de usuário com usuários e permissões, [exemplos de gerenciamento de usuários do .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
