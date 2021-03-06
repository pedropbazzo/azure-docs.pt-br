---
title: Copiar dados de uma tabela SAP
description: Saiba como copiar dados de uma tabela SAP para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 608694c07894c8bdff8b1101d607e07ea4383764
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279816"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiar dados de uma tabela SAP usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de uma tabela SAP. Para obter mais informações, confira [Visão geral da atividade de cópia](copy-activity-overview.md).

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada sobre cada conector SAP, análise e diretrizes.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Há suporte para este conector de tabela SAP para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de uma tabela SAP para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector de tabela SAP dá suporte a:

- Copiando dados de uma tabela SAP em:

  - Componente central SAP ERP (SAP ECC) versão 7, 1 ou posterior (em uma pilha recente de pacotes de suporte SAP lançada após 2015).
  - SAP Business Warehouse (SAP BW) versão 7, 1 ou posterior (em uma pilha recente de pacotes de suporte SAP lançada após 2015).
  - SAP S/4HANA.
  - Outros produtos no SAP Business Suite versão 7, 1 ou posterior (em uma pilha recente do pacote de suporte SAP lançado após 2015).

- Copiar dados de uma tabela transparente do SAP, uma tabela em pool, uma tabela clusterizada e uma exibição.
- Copiar dados usando a autenticação básica ou a SNC (comunicações de rede segura) se a SNC estiver configurada.
- Conectando-se a um servidor de aplicativos SAP ou servidor de mensagens SAP.
- Recuperando dados por meio da RFC padrão ou personalizada.

A versão 7, 1 ou posterior refere-se à versão do SAP NetWeaver em vez da versão do SAP ECC. Por exemplo, o SAP ECC 6,0 EHP 7 em geral tem a versão NetWeaver >= 7,4. Caso você não tenha certeza sobre o seu ambiente, aqui estão as etapas para confirmar a versão do seu sistema SAP:
1.  Use a GUI do SAP para se conectar ao sistema SAP. 
2.  Vá para **System**  ->  **status**do sistema. 
3.  Verifique o lançamento do SAP_BASIS, verifique se ele é igual ou maior que 701.  
      ![Verificar SAP_BASIS](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector de tabela SAP, você precisa:

- Configure um tempo de execução de integração auto-hospedado (versão 3,17 ou posterior). Para obter mais informações, consulte [criar e configurar um tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md).

- Baixe o conector SAP de 64 bits [para Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) do site da SAP e instale-o no computador do Integration Runtime de hospedagem interna. Durante a instalação, certifique-se de selecionar a opção **instalar assemblies no GAC** na janela **etapas de instalação opcionais** .

  ![Instalar o conector do SAP para .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O usuário SAP que está sendo usado no conector de tabela do SAP Data Factory deve ter as seguintes permissões:

  - Autorização para usar destinos de chamada de função remota (RFC).
  - Permissões para a atividade executar do objeto de autorização S_SDSAUTH.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir as entidades de Data Factory específicas para o conector de tabela do SAP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado de Hub aberto do SAP BW:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` precisa ser definida como `SapTable`. | Sim |
| `server` | O nome do servidor no qual a instância SAP está localizada.<br/>Use para se conectar a um servidor de aplicativos SAP. | Não |
| `systemNumber` | O número de sistema do sistema SAP.<br/>Use para se conectar a um servidor de aplicativos SAP.<br/>Valor permitido: um número decimal de dois dígitos representado como uma cadeia de caracteres. | Não |
| `messageServer` | O nome do host do servidor de mensagens SAP.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `messageServerService` | O nome do serviço ou o número da porta do servidor de mensagens.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `systemId` | A ID do sistema SAP em que a tabela está localizada.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `logonGroup` | O grupo de logon do sistema SAP.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `clientId` | A ID do cliente no sistema SAP.<br/>Valor permitido: um número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| `language` | O idioma que o sistema SAP usa.<br/>O valor padrão é `EN`.| Não |
| `userName` | O nome do usuário que tem acesso ao servidor SAP. | Sim |
| `password` | A senha do usuário. Marque este campo com o `SecureString` tipo para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| `sncMode` | O indicador de ativação da SNC para acessar o servidor SAP onde a tabela está localizada.<br/>Use se você quiser usar o SNC para se conectar ao servidor SAP.<br/>Os valores permitidos são `0` (off, o padrão) ou `1` (on). | Não |
| `sncMyName` | O nome do SNC do iniciador para acessar o servidor SAP onde a tabela está localizada.<br/>Aplica-se quando `sncMode` está ativado. | Não |
| `sncPartnerName` | O nome do SNC do parceiro de comunicação para acessar o servidor SAP onde a tabela está localizada.<br/>Aplica-se quando `sncMode` está ativado. | Não |
| `sncLibraryPath` | A biblioteca do produto de segurança externa para acessar o servidor SAP onde a tabela está localizada.<br/>Aplica-se quando `sncMode` está ativado. | Não |
| `sncQop` | O nível de qualidade de proteção da SNC a ser aplicado.<br/>Aplica-se quando `sncMode` está ativado. <br/>Os valores permitidos são `1` (autenticação), `2` (integridade), `3` (privacidade), `8` (padrão), `9` (máximo). | Não |
| `connectVia` | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Um tempo de execução de integração auto-hospedado é necessário, conforme mencionado anteriormente em [pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: conectar-se a um servidor de aplicativos SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Exemplo 2: conectar-se a um servidor de mensagens SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Exemplo 3: conectar usando o SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades para definir conjuntos de os, consulte [DataSets](concepts-datasets-linked-services.md). A seção a seguir fornece uma lista das propriedades compatíveis pelo conjunto de dados da tabela do SAP.

Para copiar dados de e para o SAP BW serviço vinculado de Hub aberto, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` precisa ser definida como `SapTableResource`. | Sim |
| `tableName` | O nome da tabela SAP da qual copiar dados. | Sim |

### <a name="example"></a>Exemplo

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades para definir atividades, consulte [pipelines](concepts-pipelines-activities.md). A seção a seguir fornece uma lista das propriedades com suporte pela origem da tabela do SAP.

### <a name="sap-table-as-source"></a>Tabela SAP como fonte

Para copiar dados de uma tabela SAP, há suporte para as seguintes propriedades:

| Propriedade                         | Descrição                                                  | Obrigatório |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | A propriedade `type` precisa ser definida como `SapTableSource`.         | Sim      |
| `rowCount`                         | O número de linhas a serem recuperadas.                              | Não       |
| `rfcTableFields`                 | Os campos (colunas) a serem copiados da tabela SAP. Por exemplo, `column0, column1`. | Não       |
| `rfcTableOptions`                | As opções para filtrar as linhas em uma tabela SAP. Por exemplo, `COLUMN0 EQ 'SOMEVALUE'`. Consulte também a tabela do operador de consulta SAP mais adiante neste artigo. | Não       |
| `customRfcReadTableFunctionModule` | Um módulo de função RFC personalizado que pode ser usado para ler dados de uma tabela SAP.<br>Você pode usar um módulo de função RFC personalizado para definir como os dados são recuperados do seu sistema SAP e retornados para Data Factory. O módulo de função personalizada deve ter uma interface implementada (importação, exportação, tabelas) semelhante a `/SAPDS/RFC_READ_TABLE2` , que é a interface padrão usada pelo data Factory.<br>Data Factory | Não       |
| `partitionOption`                  | O mecanismo de partição para ler de uma tabela SAP. As opções compatíveis incluem: <ul><li>`None`</li><li>`PartitionOnInt` (inteiro normal ou valores inteiros com preenchimento zero à esquerda, como `0000012345` )</li><li>`PartitionOnCalendarYear` (4 dígitos no formato "aaaa")</li><li>`PartitionOnCalendarMonth` (6 dígitos no formato "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 dígitos no formato "aaaammdd")</li></ul> | Não       |
| `partitionColumnName`              | O nome da coluna usada para particionar os dados.                | Não       |
| `partitionUpperBound`              | O valor máximo da coluna especificada no `partitionColumnName` que será usado para continuar com o particionamento. | Não       |
| `partitionLowerBound`              | O valor mínimo da coluna especificada no `partitionColumnName` que será usado para continuar com o particionamento. (Observação: `partitionLowerBound` não pode ser "0" quando a opção de partição é `PartitionOnInt` ) | Não       |
| `maxPartitionsNumber`              | O número máximo de partições para dividir os dados.     | Não       |
| `sapDataColumnDelimiter` | O caractere único que é usado como delimitador passado para SAP RFC para dividir os dados de saída. | Não |

>[!TIP]
>Se sua tabela SAP tiver um grande volume de dados, como vários bilhões de linhas, use `partitionOption` e `partitionSetting` para dividir os dados em partições menores. Nesse caso, os dados são lidos por partição e cada partição de dados é recuperada do servidor SAP por meio de uma única chamada RFC.<br/>
<br/>
>`partitionOption`Como `partitionOnInt` exemplo, o número de linhas em cada partição é calculado com esta fórmula: (total de linhas caindo entre `partitionUpperBound` e `partitionLowerBound` )/ `maxPartitionsNumber` .<br/>
<br/>
>Para carregar partições de dados em paralelo para acelerar a cópia, o grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) configuração na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, data Factory gera e executa quatro consultas simultaneamente com base na opção de partição especificada e nas configurações, e cada consulta recupera uma parte dos dados de sua tabela SAP. É altamente recomendável fazer `maxPartitionsNumber` um múltiplo do valor da `parallelCopies` propriedade. Ao copiar dados para o armazenamento de dados baseado em arquivo, ele também é recriado para gravar em uma pasta como vários arquivos (apenas especifique o nome da pasta); nesse caso, o desempenho é melhor do que gravar em um único arquivo.

No `rfcTableOptions` , você pode usar os seguintes operadores de consulta SAP comuns para filtrar as linhas:

| Operador | Descrição |
| :------- | :------- |
| `EQ` | Igual a |
| `NE` | É diferente de |
| `LT` | Menor que |
| `LE` | Menor que ou igual a |
| `GT` | Maior que |
| `GE` | Maior que ou igual a |
| `IN` | Como em `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Como em `LIKE 'Emma%'` |

### <a name="example"></a>Exemplo

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapeamentos de tipo de dados para uma tabela SAP

Quando você está copiando dados de uma tabela SAP, os seguintes mapeamentos são usados dos tipos de dados de tabela SAP para os tipos de dados provisórios Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo SAP ABAP | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| `C` Strings | `String` |
| `I` Valores | `Int32` |
| `F` Barra | `Double` |
| `D` Date | `String` |
| `T` Momento | `String` |
| `P` (BCD empacotado, moeda, Decimal, Qtd) | `Decimal` |
| `N` Numeric | `String` |
| `X` (Binary e RAW) | `String` |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos armazenamentos de dados compatíveis como fontes e coletores com a atividade de cópia no Azure Data Factory, confira [Armazenamentos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
