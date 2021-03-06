---
title: Otimizar o custo de leituras e gravações no Azure Cosmos DB
description: Este artigo explica como reduzir os custos do Azure Cosmos DB ao executar a leitura e gravar operações nos dados.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: 38084bf30df2a597e7a7bc46ba4c52cf371c3c7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318242"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Otimizar o custo das leituras e gravações no Azure Cosmos DB

Este artigo descreve o custo necessário para ler e gravar dados do Azure Cosmos DB que é calculado. As operações de leitura incluem [leituras de ponto e consultas](sql-query-getting-started.md). As operações de gravação incluem inserir, substituir, excluir e Upsert de itens.  

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e gravações

O Azure Cosmos DB garante um desempenho previsível em termos de taxa de transferência e latência, usando um modelo de taxa de transferência provisionada. A taxa de transferência provisionada é representada em termos de [Unidades de Solicitação](request-units.md) por segundo ou RU/s. Uma Unidade de Solicitação (RU) é uma abstração lógica de recursos de computação como CPU, memória, e/s, etc. que são necessárias para executar uma solicitação. Taxa de transferência (RUs) é reservada e dedicada ao contêiner ou banco de dados para fornecer latência e taxa de transferência previsível. Taxa de transferência provisionada permite que o Azure Cosmos DB forneça um desempenho previsível e consistente, a garantia de baixa latência e alta disponibilidade em qualquer escala. Unidades de solicitação representam a moeda normalizada que simplifica o raciocínio sobre quantos recursos um aplicativo precisa.

Você não precisa pensar em diferenciar unidades de solicitação entre leituras e gravações. O modelo unificado de moeda de unidades de solicitação cria eficiência alternadamente, use a mesma capacidade de taxa de transferência para leituras e gravações. A tabela a seguir mostra o custo de leituras de ponto e gravações em termos de RU/s para itens que são de 1 KB e 100 KB de tamanho.

|**Tamanho do item**  |**Custo de uma leitura de ponto** |**Custo de uma gravação**|
|---------|---------|---------|
|1 KB |1 RU |5 RUs |
|100 KB |10 RUs |50 RUs |

Fazer uma leitura pontual para um item que é de 1 KB de tamanho custa um RU. Gravar um item de 1 KB custa cinco RUs. Os custos de leitura e gravação são aplicáveis ao usar a sessão padrão [nível de consistência](consistency-levels.md).  As considerações a respeito de RUs incluem: item de tamanho, contagem de propriedades, consistência de dados, propriedades indexadas, indexação e padrões de consulta.

As [leituras pontuais](sql-query-getting-started.md) custam significativamente menos ru do que as consultas. As leituras de ponto, diferentemente das consultas, não precisam usar o mecanismo de consulta para acessar dados podem economizar RU. A cobrança de RU da consulta depende da complexidade da consulta e do número de itens que o mecanismo de consulta precisa carregar.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Otimizar o custo de leituras e gravações

Ao executar operações de gravação, você deve provisionar capacidade suficiente para dar suporte ao número de gravações necessárias por segundo. Você pode aumentar a taxa de transferência provisionada usando o SDK, portal, CLI antes de executar as gravações e, em seguida, reduzir a taxa de transferência depois que as gravações são concluídas. Sua taxa de transferência para o período de gravação é a taxa de transferência mínima necessária para os dados fornecidos, além de produtividade necessária para inserir a carga de trabalho supondo que sem outras cargas de trabalho estão em execução.

Se você estiver executando outras cargas de trabalho simultaneamente, por exemplo, consulta/leitura/atualização/exclusão, adicione as unidades de solicitação adicionais necessárias para essas operações também. Se as operações de gravação são limitadas por taxa, você pode personalizar a política de repetição/retirada usando os SDKs do Azure Cosmos DB. Por exemplo, você pode aumentar a carga até que uma pequena taxa de solicitações seja limitada pela taxa. Em caso de limite de taxa, o aplicativo cliente deve retornar nas solicitações de limitação de taxa para o intervalo de repetição especificada. Antes de repetir as gravações, você deve ter uma quantidade mínima de espaço de tempo entre as repetições. O suporte à política de repetição está incluído no SQL .NET, Java, Node. js, além de SDKs Python e todas as versões com suporte dos SDKs do .NET Core.

Você também pode inserir dados em massa para o Azure Cosmos DB ou copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Cosmos DB usando o [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). O Azure Data Factory integra-se nativamente com a API do Azure Cosmos DB em massa para fornecer o melhor desempenho ao gravar dados.

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)
