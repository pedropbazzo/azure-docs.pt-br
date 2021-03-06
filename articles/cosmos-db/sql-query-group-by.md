---
title: Cláusula GROUP BY no Azure Cosmos DB
description: Saiba mais sobre a cláusula GROUP BY para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: b602b56d37cec0e23d31318f6675d031bdd6bcdb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83700994"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Cláusula GROUP BY no Azure Cosmos DB

A cláusula GROUP BY divide os resultados da consulta de acordo com os valores de uma ou mais propriedades especificadas.

## <a name="syntax"></a>Sintaxe

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumentos

- `<scalar_expression_list>`

   Especifica as expressões que serão usadas para dividir os resultados da consulta.

- `<scalar_expression>`
  
   Qualquer expressão escalar é permitida, exceto para subconsultas escalares e agregações escalares. Cada expressão escalar deve conter pelo menos uma referência de propriedade. Não há limite para o número de expressões individuais ou a cardinalidade de cada expressão.

## <a name="remarks"></a>Comentários
  
  Quando uma consulta usa uma cláusula GROUP BY, a cláusula SELECT pode conter apenas o subconjunto de propriedades e funções do sistema incluídas na cláusula GROUP BY. Uma exceção é [funções de sistema agregadas](sql-query-aggregates.md), que podem aparecer na cláusula SELECT sem serem incluídas na cláusula GROUP BY. Você também pode incluir valores literais na cláusula SELECT.

  A cláusula GROUP BY deve vir depois da cláusula SELECT, FROM e WHERE e antes da cláusula OFFSET LIMIT. No momento, não é possível usar GROUP BY com uma cláusula ORDER BY, mas isso é planejado.

  A cláusula GROUP BY não permite nenhum dos seguintes:
  
- Propriedades de alias ou funções do sistema de alias (alias ainda é permitido dentro da cláusula SELECT)
- Subconsultas
- Funções do sistema agregadas (permitidas apenas na cláusula SELECT)

Não há suporte para consultas com uma função de sistema agregada e uma subconsulta com `GROUP BY`. Por exemplo, a seguinte consulta não tem suporte:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Exemplos

Esses exemplos usam o conjunto de dados de nutrição disponível no [Playground para Consultas do Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Por exemplo, esta é uma consulta que retorna a contagem total de itens em foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Alguns resultados são (a palavra-chave TOP é usada para limitar os resultados):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Esta consulta tem duas expressões usadas para dividir os resultados:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Alguns resultados são:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Esta consulta tem uma função de sistema na cláusula GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Alguns resultados são:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Esta consulta usa as duas palavras-chave e funções do sistema na expressão de propriedade do item:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Os resultados são:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Funções de agregação](sql-query-aggregates.md)
