---
title: Acessar arquivos em armazenamento no SQL sob demanda (versão prévia)
description: Descreve a consulta de arquivos de armazenamento usando recursos de SQL sob demanda (versão prévia) no Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8884f62ba015cc4b33b75a133f21264dac6430e5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91288980"
---
# <a name="access-external-storage-in-synapse-sql-on-demand"></a>Acessar o armazenamento externo no SQL do Synapse (sob demanda)

Este documento descreve como o usuário pode ler dados dos arquivos armazenados no Armazenamento do Azure no SQL do Synapse (sob demanda). Os usuários têm as seguintes opções para acessar o armazenamento:

- Função [OPENROWSET](develop-openrowset.md), que habilita consultas ad hoc sobre os arquivos no Armazenamento do Azure.
- A [tabela externa](develop-tables-external-tables.md), que é uma estrutura de dados predefinida criada na parte superior do conjunto de arquivos externos.

O usuário pode usar [métodos de autenticação diferentes](develop-storage-files-storage-access-control.md) como autenticação de passagem do Azure AD (padrão para entidades do Azure AD) e autenticação SAS (padrão para entidades de segurança do SQL).

## <a name="query-files-using-openrowset"></a>Consultar arquivos usando OPENROWSET

O OPENROWSET permitirá que os usuários consultem arquivos externos no Armazenamento do Azure se tiverem acesso ao armazenamento. O usuário que está conectado ao ponto de extremidade sob demanda do SQL do Synapse deve usar a seguinte consulta para ler o conteúdo dos arquivos no armazenamento do Azure:

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

O usuário pode acessar o armazenamento usando as seguintes regras de acesso:

- O usuário do Azure AD – `OPENROWSET` usará a identidade do chamador do Azure AD para acessar o Armazenamento do Azure ou acessar o armazenamento com acesso anônimo.
- Usuário do SQL – `OPENROWSET` acessará o armazenamento com acesso anônimo ou poderá ser representado usando o token SAS ou a identidade gerenciada do workspace.

### <a name="impersonation"></a>[Representação](#tab/impersonation)

As entidades de segurança do SQL também podem usar OPENROWSET para consultar diretamente os arquivos protegidos com tokens SAS ou identidade gerenciada do workspace. Se um usuário do SQL executar essa função, um usuário avançado com a permissão `ALTER ANY CREDENTIAL` deverá criar uma credencial no escopo do servidor que corresponda à URL na função (usando o nome e o contêiner de armazenamento) e conceder a permissão REFERENCES para essa credencial ao chamador da função OPENROWSET:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

Se não houver nenhuma CREDENCIAL no nível do servidor que corresponda à URL ou se o usuário do SQL não tiver a permissão de referências para essa credencial, o erro será retornado. As entidades de segurança SQL não podem representar o uso de uma identidade do Azure AD.

### <a name="direct-access"></a>[Acesso direto](#tab/direct-access)

Nenhuma configuração adicional é necessária para permitir que os usuários do Azure AD acessem os arquivos usando as respectivas identidades.
Qualquer usuário pode acessar o armazenamento do Azure que permite acesso anônimo (não é necessária nenhuma configuração adicional).

---

> [!NOTE]
> Esta versão do OPENROWSET foi projetada para uma exploração de dados rápida e fácil usando a autenticação padrão. Para utilizar a representação ou a identidade gerenciada, use OPENROWSET com a DATASOURCE descrita na próxima seção.

## <a name="query-data-sources-using-openrowset"></a>Consultar fontes de dados usando OPENROWSET

OPENROWSET permite que o usuário consulte os arquivos colocados em alguma fonte de dados externa:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

O usuário que executa essa consulta precisa conseguir acessar os arquivos. Os usuários deverão ser representados usando o [Token SAS](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) ou a [Identidade gerenciada do workspace](develop-storage-files-storage-access-control.md?tabs=managed-identity) se não puderem acessar diretamente os arquivos usando suas [identidade do Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity) ou [acesso anônimo](develop-storage-files-storage-access-control.md?tabs=public-access).

### <a name="impersonation"></a>[Representação](#tab/impersonation)

`DATABASE SCOPED CREDENTIAL` especifica como acessar os arquivos na fonte de dados referenciada (no momento, SAS e Identidade Gerenciada). O usuário avançado com permissão de `CONTROL DATABASE` precisaria criar `DATABASE SCOPED CREDENTIAL`, que serão usadas para acessar o armazenamento, e `EXTERNAL DATA SOURCE`, que especifica a URL da fonte de dados e da credencial que deve ser usada:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

O chamador deve ter uma das seguintes permissões para executar a função OPENROWSET:

- Uma das permissões para executar OPENROWSET:
  - `ADMINISTER BULK OPERATIONS` permite que o logon execute a função OPENROWSET.
  - `ADMINISTER DATABASE BULK OPERATIONS` permite que o usuário no escopo do banco de dados execute a função OPENROWSET.
- `REFERENCES DATABASE SCOPED CREDENTIAL` para a credencial que é referenciada no `EXTERNAL DATA SOURCE`.

### <a name="direct-access"></a>[Acesso direto](#tab/direct-access)

O usuário pode criar uma EXTERNAL DATA SOURCE sem CREDENTIAL que fará referência ao armazenamento de acesso público OU usará a autenticação de passagem do Azure AD:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>EXTERNAL TABLE

O usuário com as permissões para ler a tabela pode acessar arquivos externos usando uma EXTERNAL TABLE criada na parte superior do conjunto de arquivos e pastas do Armazenamento do Azure.

O usuário que tem [permissões para criar tabela externa](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions&preserve-view=true) (por exemplo CREATE TABLE e ALTER ANY CREDENTIAL ou REFERENCES DATABASE SCOPED CREDENTIAL) pode usar o seguinte script para criar uma tabela sobre a fonte de dados do Armazenamento do Azure:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

O usuário que lê os dados dessa tabela deve ser capaz de acessar os arquivos. Os usuários deverão ser representados usando [token SAS](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) ou [identidade gerenciada do workspace](develop-storage-files-storage-access-control.md?tabs=managed-identity) se não puderem acessar diretamente os arquivos usando suas [identidade do Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity) ou [acesso anônimo](develop-storage-files-storage-access-control.md?tabs=public-access).

### <a name="impersonation"></a>[Representação](#tab/impersonation)

DATABASE SCOPED CREDENTIAL especifica como acessar os arquivos na fonte de dados referenciada. O usuário com a permissão CONTROL DATABASE precisaria criar uma DATABASE SCOPED CREDENTIAL para acessar o armazenamento e uma EXTERNAL DATA SOURCE que especificaria a URL da fonte de dados e a credencial que deveriam ser usadas:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[Acesso direto](#tab/direct-access)

O usuário pode criar uma EXTERNAL DATA SOURCE sem CREDENTIAL que fará referência ao armazenamento de acesso público OU usará a autenticação de passagem do Azure AD:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>Ler arquivos externos com EXTERNAL TABLE

EXTERNAL TABLE permite que você leia dados dos arquivos que são referenciados por meio da fonte de dados usando a instrução SQL SELECT padrão:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

O chamador deve ter as seguintes permissões para ler os dados:
- Permissão `SELECT` na tabela externa
- Permissão `REFERENCES DATABASE SCOPED CREDENTIAL` se `DATA SOURCE` tiver `CREDENTIAL`

## <a name="permissions"></a>Permissões

A tabela a seguir lista as permissões necessárias para as operações listadas acima.

| Consulta | Permissões necessárias|
| --- | --- |
| OPENROWSET (BULK) sem datasource | `ADMINISTER BULK OPERATIONS`, `ADMINISTER DATABASE BULK OPERATIONS` ou logon do SQL devem ter REFERENCES CREDENTIAL::\<URL> para armazenamento protegido por SAS |
| OPENROWSET(BULK) com datasource sem credencial | `ADMINISTER BULK OPERATIONS` ou `ADMINISTER DATABASE BULK OPERATIONS`, |
| OPENROWSET(BULK) com datasource com credencial | `REFERENCES DATABASE SCOPED CREDENTIAL` e um de `ADMINISTER BULK OPERATIONS` ou `ADMINISTER DATABASE BULK OPERATIONS` |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` e `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY EXTERNAL FILE FORMAT` e `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` e `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Para criar tabela – `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY DATA SOURCE` e `ALTER ANY EXTERNAL FILE FORMAT`. Para ler dados: `ADMINISTER BULK OPERATIONS` ou `REFERENCES CREDENTIAL` ou `SELECT TABLE` para cada tabela/exibição/função na consulta + permissão para leitura e gravação no armazenamento |

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para continuar com os seguintes artigos de instruções:

- [Consultar dados no armazenamento](query-data-storage.md)

- [Consultar arquivo CSV](query-single-csv-file.md)

- [Consultar arquivos Parquet](query-parquet-files.md)

- [Consultar arquivos JSON](query-json-files.md)

- [Consultar pastas e vários arquivos](query-folders-multiple-csv-files.md)

- [Usar funções de particionamento e metadados](query-specific-files.md)

- [Tipos aninhados de consulta](query-parquet-nested-types.md)

- [Como criar e usar exibições](create-use-views.md)
