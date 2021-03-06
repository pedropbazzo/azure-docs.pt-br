---
title: Estratégias de acesso a dados
description: Agora o Azure Data Factory dá suporte a intervalos de endereço IP Estático.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: a4d8d7eaed40b876adecb82f339be4a4c434325f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616849"
---
# <a name="data-access-strategies"></a>Estratégias de acesso a dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um objetivo de segurança primordial de uma organização é proteger os armazenamentos de dados contra o acesso aleatório pela Internet, seja um armazenamento de dados local ou em nuvem/SaaS. 

Normalmente, um armazenamento de dados em nuvem controla o acesso usando os mecanismos abaixo:
* Link privado de uma rede virtual para fontes de dados habilitadas para ponto de extremidade privado
* Regras de firewall que limitam a conectividade por endereço IP
* Mecanismos de autenticação que exigem que os usuários comprovem sua identidade
* Mecanismos de autorização que restringem os usuários a ações e dados específicos

> [!TIP]
> Com a [introdução do intervalo de endereço IP Estático](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses), agora você pode permitir a lista de intervalos de IP para a região de tempo de execução de integração do Azure específica, para garantir que você não precise permitir todos os endereços IP do Azure nos armazenamentos de dados em nuvem. Dessa forma, você pode restringir os endereços IP que têm permissão para acessar os armazenamentos de dados.

> [!NOTE] 
> Os intervalos de endereços IP são bloqueados para Azure Integration Runtime e, no momento, são usados apenas para movimentação de dados, pipeline e atividades externas. Os fluxos de e Azure Integration Runtime que habilitam a rede virtual gerenciada agora não usam esses intervalos de IP. 

Isso deve funcionar em muitos cenários e entendemos que um endereço IP Estático exclusivo por tempo de execução de integração seria desejável, mas atualmente não seria possível usando o Azure Integration Runtime, que é sem servidor. Se necessário, você sempre pode configurar um Runtime de Integração Auto-Hospedada e usar o IP Estático com ele. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Estratégias de acesso a dados por meio do Azure Data Factory

* **[Link privado](https://docs.microsoft.com/azure/private-link/private-link-overview)** – você pode criar um Azure Integration Runtime em Azure data Factory rede virtual gerenciada e utilizará pontos de extremidade privados para se conectar com segurança a armazenamentos de dados com suporte. O tráfego entre a rede virtual gerenciada e as fontes de dados viaja pela rede de backbone da Microsoft e não é uma exposição à rede pública.
* **[Serviço Confiável](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** - O Armazenamento do Microsoft Azure (Blob, ADLS Gen2) dá suporte à configuração do firewall que permite selecionar serviços confiáveis da plataforma Azure para acessar a conta de armazenamento com segurança. Os serviços confiáveis imponham a autenticação de identidade gerenciada, o que garante que nenhum outro data factory possa se conectar a esse armazenamento, a menos que seja aprovado para fazer isso usando a identidade gerenciada. Você pode encontrar mais detalhes **[neste blog](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** . Portanto, é extremamente seguro e recomendado. 
* **IP Estático Exclusivo** - Você precisará configurar um runtime de integração auto-hospedada para obter um IP Estático para conectores de Data Factory. Esse mecanismo garante que você possa bloquear o acesso de todos os outros endereços IP. 
* **[Intervalo de IP Estático](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** - Você pode usar os endereços IP do Azure Integration Runtime para permitir listá-los no armazenamento (digamos S3, Salesforce, etc.). Certamente, ele restringe os endereços IP que podem se conectar aos armazenamentos de dados, mas também se baseia em regras de Autenticação/Autorização.
* **[Service Tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** - Uma marca de serviço representa um grupo de prefixos de endereço IP de determinado serviço do Azure (como o Azure Data Factory). A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços, minimizando a complexidade de atualizações frequentes das regras de segurança de rede. Ele é útil ao filtrar o acesso a dados em armazenamentos de dados hospedados de IaaS na rede virtual.
* **Permitir serviços do Azure** - Permite que todos os serviços do Azure sejam conectados, caso você escolha essa opção. 

Para obter mais informações sobre os mecanismos de segurança de rede com suporte em armazenamentos de dados no Azure Integration Runtime e Runtime de Integração Auto-Hospedada, confira as duas tabelas abaixo.  
* **Azure Integration Runtime**

    | Armazenamentos de dados                  | Mecanismo de segurança de rede com suporte em armazenamentos de dados | Link Privado     | Serviço confiável     | Intervalo de IP estático | Marcas de serviço | Permitir serviços do Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Armazenamentos de dados do Azure PaaS       | Azure Cosmos DB                                     | Sim              | -                   | Sim             | -            | Sim                  |
    |                              | Azure Data Explorer                                 | -                | -                   | Sim*            | Sim*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Sim             | -            | Sim                  |
    |                              | Banco de Dados do Azure para MariaDB, MySQL, PostgreSQL       | -                | -                   | Sim             | -            | Sim                  |
    |                              | Armazenamento de Arquivos do Azure                                  | Sim              | -                   | Sim             | -            | .                    |
    |                              | Armazenamento do Azure (BLOB, ADLS Gen2)                     | Sim              | Sim (somente autenticação MSI) | Sim             | -            | .                    |
    |                              | BD SQL do Azure, Azure Synapse Analytics), SQL ml  | Sim (somente banco de BD SQL do Azure/DW)        | -                   | Sim             | -            | Sim                  |
    |                              | Azure Key Vault (para buscar segredos/cadeia de conexão) | sim      | Sim                 | Sim             | -            | -                    |
    | Outros armazenamentos de dados PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage, etc.    | -                | -                   | Sim             | -            | -                    |
    | Azure laaS                   | SQL Server, Oracle, etc.                          | -                | -                   | Sim             | Sim          | -                    |
    | laaS no local              | SQL Server, Oracle, etc.                          | -                | -                   | Sim             | -            | -                    |
    
    **Aplicável apenas quando o Azure Data Explorer é injetado em rede virtual e o intervalo de IP pode ser aplicado no NSG/Firewall.* 

* **Runtime de Integração Auto-Hospedada (na Vnet/no local)**
    
    | Armazenamentos de dados                  | Mecanismo de segurança de rede com suporte em armazenamentos de dados         | IP Estático | Serviços confiáveis  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Armazenamentos de dados do Azure PaaS       | Azure Cosmos DB                                               | Sim       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Sim       | -                   |
    |                                | Banco de Dados do Azure para MariaDB, MySQL, PostgreSQL               | Sim       | -                   |
    |                                | Armazenamento de Arquivos do Azure                                            | Sim       | -                   |
    |                                | Armazenamento do Microsoft Azure (Blog, ADLS Gen2)                             | Sim       | Sim (somente autenticação MSI) |
    |                                | BD SQL do Azure, Azure Synapse Analytics), SQL ml          | Sim       | -                   |
    |                                | Azure Key Vault (para buscar segredos/cadeia de conexão) | Sim       | Sim                 |
    | Outros armazenamentos de dados PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage, etc.              | Sim       | -                   |
    | Azure laaS                     | SQL Server, Oracle, etc.                                  | Sim       | -                   |
    | laaS no local              | SQL Server, Oracle, etc.                                  | Sim       | -                   |    

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira os artigos relacionados a seguir:
* [Armazenamento de dados com suporte](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* ["Serviços Confiáveis" do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* ["Serviços Confiáveis" do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Identidade Gerenciada para Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
