---
title: Atualizar para o SDK do .NET versão 11
titleSuffix: Azure Cognitive Search
description: Migre o código para o SDK do .NET Pesquisa Cognitiva do Azure versão 11 de versões mais antigas. Conheça as novidades e quais alterações de código são necessárias.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f6953f145621e11506a009fa59d67a5f40508a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539564"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Atualizar para o Azure Pesquisa Cognitiva SDK do .NET versão 11

Se você estiver usando a versão 10,0 ou anterior do [SDK do .net](/dotnet/api/overview/azure/search), este artigo o ajudará a atualizar para a versão 11.

A versão 11 é uma biblioteca de cliente totalmente reprojetada, liberada pela equipe de desenvolvimento do SDK do Azure (as versões anteriores foram produzidas pela equipe de desenvolvimento do Azure Pesquisa Cognitiva). A biblioteca foi reprojetada para maior consistência com outras bibliotecas de cliente do Azure, assumindo uma dependência no [Azure. Core](/dotnet/api/azure.core) e [System.Text.Jsem](/dotnet/api/system.text.json)e implementando abordagens familiares para tarefas comuns.

Algumas das principais diferenças que você observará na nova versão incluem:

+ Um pacote e uma biblioteca em oposição a vários
+ Um novo nome de pacote: `Azure.Search.Documents` em vez de `Microsoft.Azure.Search` .
+ Três clientes em vez de dois: `SearchClient` , `SearchIndexClient` , `SearchIndexerClient`
+ Diferenças de nomenclatura em uma variedade de APIs e pequenas diferenças estruturais que simplificam algumas tarefas

> [!NOTE]
> Examine o [**log de alterações**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) para obter uma lista discriminada de alterações no SDK do .NET versão 11.

## <a name="package-and-library-consolidation"></a>Consolidação de pacote e biblioteca

A versão 11 consolida vários pacotes e bibliotecas em um. Após a migração, você terá menos bibliotecas para gerenciar.

+ [Azure.Search.Docpacote uments](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [Referência de API para a biblioteca de cliente](/dotnet/api/overview/azure/search.documents-readme)

## <a name="client-differences"></a>Diferenças de cliente

Quando aplicável, a tabela a seguir mapeia as bibliotecas de cliente entre as duas versões.

| Escopo de operações | Microsoft. Azure. Search &nbsp; (V10) | Azure.Search.Documents &nbsp; (v11) |
|---------------------|------------------------------|------------------------------|
| Cliente usado para consultas e para popular um índice. | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| Cliente usado para índices, analisadores, mapas de sinônimos | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Cliente usado para indexadores, fontes de dados, habilidades | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**novo**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` existe em ambas as versões, mas dá suporte a coisas diferentes. Na versão 10, `SearchIndexClient` crie índices e outros objetos. Na versão 11, `SearchIndexClient` funciona com índices existentes. Para evitar confusão ao atualizar o código, lembre-se da ordem na qual as referências do cliente são atualizadas. Seguir a sequência em [etapas para atualizar](#UpgradeSteps) o deve ajudar a mitigar quaisquer problemas de substituição de cadeia de caracteres.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Nomenclatura e outras diferenças de API

Além das diferenças do cliente (observado anteriormente e, portanto, omitido aqui), várias outras APIs foram renomeadas e, em alguns casos, remodeladas. Diferenças de nome de classe são resumidas abaixo. Essa lista não é exaustiva, mas faz alterações na API do grupo por tarefa, o que pode ser útil para revisões em blocos de código específicos. Para obter uma lista discriminada de atualizações de API, consulte o [log de alterações](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) para `Azure.Search.Documents` no github.

### <a name="authentication-and-encryption"></a>Autenticação e criptografia

| Versão 10 | Equivalente à versão 11 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (existia no [SDK de visualização](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) como um recurso geralmente disponível) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Índices, analisadores, mapas de sinônimos

| Versão 10 | Equivalente à versão 11 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Campo](/dotnet/api/microsoft.azure.search.models.field) | [Searchfield](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [Erro de](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analisador](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (também, `AnalyzerName` para `LexicalAnalyzerName` ) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (também, `StandardTokenizerV2` para `LuceneStandardTokenizerV2` ) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Criador](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (também, `TokenizerName` para `LexicalTokenizerName` ) |
| [SynonymMap. Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Nenhum. Remova as referências a `Format` . |

As definições de campo são simplificadas: [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [simplestyle](/dotnet/api/azure.search.documents.indexes.models.simplefield), [complexfield](/dotnet/api/azure.search.documents.indexes.models.complexfield) são novas APIs para a criação de definições de campo.

### <a name="indexers-datasources-skillsets"></a>Indexadores, fontes de habilidades

| Versão 10 | Equivalente à versão 11 |
|------------|-----------------------|
| [Indexador](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Técnico](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Conjunto de habilidades](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Importação de Dados

| Versão 10 | Equivalente à versão 11 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Definições de consulta e resultados

| Versão 10 | Equivalente à versão 11 |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) ou [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1), dependendo se o resultado é um único documento ou vários. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [Searchoptions especificaram](/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>O que há na versão 11

Cada versão de uma biblioteca de cliente do Azure Pesquisa Cognitiva destina-se a uma versão correspondente da API REST. A API REST é considerada fundamental para o serviço, com SDKs individuais encapsulando uma versão da API REST. Como desenvolvedor do .NET, pode ser útil examinar a [documentação da API REST](/rest/api/searchservice/) se você quiser mais informações sobre objetos ou operações específicas.

A versão 11 tem como alvo o [serviço de pesquisa 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Como a versão 11 também é uma nova biblioteca de clientes criada desde o início, a maior parte do esforço de desenvolvimento se concentrou na equivalência da versão 10, com algum suporte a recursos da API REST ainda pendente.

A versão 11,0 dá suporte total aos seguintes objetos e operações:

+ Criação e gerenciamento de índice
+ Criação e gerenciamento de mapa de sinônimos
+ Todos os tipos de consulta e sintaxe (exceto filtros geoespaciais)
+ Objetos e operações do indexador para indexação de fontes de dados do Azure, incluindo fontes de dados e habilidades

A versão 11,1 adiciona o seguinte:

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (adicionado em 11,1)
+ [Propriedade de serializador](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (adicionada em 11,1) para dar suporte à serialização personalizada

### <a name="pending-features"></a>Recursos pendentes

Os seguintes recursos da versão 10 ainda não estão disponíveis na versão 11. Se você precisar desses recursos, aguarde a migração até que eles tenham suporte.

+ tipos geoespaciais
+ [Repositório de conhecimento](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização

As etapas a seguir o ajudarão a começar a usar uma migração de código ao percorrer o primeiro conjunto de tarefas necessárias, especialmente em relação às referências do cliente.

1. Instale o [ pacoteAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) clicando com o botão direito do mouse nas referências do projeto e selecionando "gerenciar pacotes NuGet..." no Visual Studio.

1. Substitua o uso de diretivas para Microsoft. Azure. Search pelo seguinte:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Para classes que exigem serialização JSON, substitua `using Newtonsoft.Json` por `using System.Text.Json.Serialization` .

1. Revisar o código de autenticação do cliente. Nas versões anteriores, você usaria Propriedades no objeto de cliente para definir a chave de API (por exemplo, a propriedade [SearchServiceClient. Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) ). Na versão atual, use a classe [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) para passar a chave como uma credencial, para que, se necessário, você possa atualizar a chave de API sem criar novos objetos de cliente.

   As propriedades do cliente foram simplificadas para apenas `Endpoint` , `ServiceName` e `IndexName` (quando apropriado). O exemplo a seguir usa a classe [URI](/dotnet/api/system.uri) do sistema para fornecer o ponto de extremidade e a classe de [ambiente](/dotnet/api/system.environment) para ler o valor da chave:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Adicione novas referências de cliente para objetos relacionados ao indexador. Se você estiver usando indexadores, fontes de fonte ou habilidades, altere as referências de cliente para [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Este cliente é novo na versão 11 e não tem o antecessor.

1. Atualizar referências de cliente para consultas e importação de dados. As instâncias de [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) devem ser alteradas para [SearchClient](/dotnet/api/azure.search.documents.searchclient). Para evitar a confusão de nomes, verifique se você capturou todas as instâncias antes de prosseguir para a próxima etapa.

1. Atualizar referências de cliente para objetos de índice, indexador, mapa de sinônimos e analisador. As instâncias de [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) devem ser alteradas para [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient). 

1. Tanto quanto possível, atualize classes, métodos e propriedades para usar as APIs da nova biblioteca. A seção [diferenças de nomenclatura](#naming-differences) é um lugar para começar, mas você também pode examinar o log de [alterações](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Se você tiver problemas para encontrar APIs equivalentes, sugerimos registrar um problema no [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) para que possamos melhorar a documentação ou investigar o problema.

1. Recriar a solução. Depois de corrigir quaisquer erros ou avisos de compilação, você pode fazer alterações adicionais em seu aplicativo para aproveitar a [nova funcionalidade](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Alterações recentes na versão 11

Considerando as alterações de varredura em bibliotecas e APIs, uma atualização para a versão 11 não é trivial e constitui uma alteração significativa no sentido de que seu código não será mais compatível com a versão 10 e anterior. Para obter uma revisão completa das diferenças, consulte o [log de alterações](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) para `Azure.Search.Documents` .

Em termos de atualizações de versão de serviço, nas quais as alterações de código na versão 11 estão relacionadas à funcionalidade existente (e não apenas a uma refatoração das APIs), você encontrará as seguintes alterações de comportamento:

+ O [algoritmo de classificação BM25](index-ranking-similarity.md) substitui o algoritmo de classificação anterior por uma tecnologia mais nova. Novos serviços usarão esse algoritmo automaticamente. Para os serviços existentes, você deve definir parâmetros para usar o novo algoritmo.

+ [Os resultados ordenados](search-query-odata-orderby.md) para valores nulos foram alterados nesta versão, com valores nulos aparecendo primeiro se a classificação for `asc` e a última se a classificação for `desc` . Se você escreveu código para manipular como valores nulos são classificados, você deve revisar e potencialmente remover esse código se ele não for mais necessário.

## <a name="next-steps"></a>Próximas etapas

+ [Azure.Search.Docpacote uments](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Exemplos no GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Docreferência da API ument](/dotnet/api/overview/azure/search.documents-readme)