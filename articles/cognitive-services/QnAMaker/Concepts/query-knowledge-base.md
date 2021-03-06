---
title: Consultar a base de dados de conhecimento-QnA Maker
description: Uma base de dados de conhecimento deve ser publicada. Depois de publicado, a base de dados de conhecimento é consultada no ponto de extremidade de previsão de tempo de execução usando a API generateAnswer.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e903714aab35de40c1179045505e1520c65b3ebc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776911"
---
# <a name="query-the-knowledge-base-for-answers"></a>Consultar a base de dados de conhecimento para obter respostas

Uma base de dados de conhecimento deve ser publicada. Depois de publicado, a base de dados de conhecimento é consultada no ponto de extremidade de previsão de tempo de execução usando a API generateAnswer. A consulta inclui o texto da pergunta e outras configurações, para ajudar QnA Maker selecionar a melhor correspondência possível com uma resposta.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Como QnA Maker processa uma consulta de usuário para selecionar a melhor resposta

A base de dados de conhecimento treinada e [publicada](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker recebe uma consulta de usuário, de um bot ou outro aplicativo cliente, na [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). O diagrama a seguir ilustra o processo quando a consulta do usuário é recebida.

![O processo de modelo de classificação para uma consulta de usuário](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Processo do classificador

O processo é explicado na tabela a seguir.

|Etapa|Finalidade|
|--|--|
|1|O aplicativo cliente envia a consulta do usuário para a [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker pré-processa a consulta do usuário com detecção de idioma, grafias e separadores de palavras.|
|3|Esse pré-processamento é usado para alterar a consulta do usuário para obter os melhores resultados da pesquisa.|
|4|Essa consulta alterada é enviada a um índice de Pesquisa Cognitiva do Azure, que recebe o `top` número de resultados. Se a resposta correta não estiver nesses resultados, aumente o valor de `top` um pouco. Em geral, um valor de 10 para `top` funciona em 90% das consultas.|
|5|QnA Maker usa personalização sintática e com base em semântica para determinar a similaridade entre a consulta do usuário e os resultados de QnA buscados.|
|6|O modelo de categorizador aprendido por máquina usa os diferentes recursos, da etapa 5, para determinar as pontuações de confiança e a nova ordem de classificação.|
|7|Os novos resultados são retornados ao aplicativo cliente em ordem classificada.|
|||

Os recursos usados incluem, mas não são limitados a semântica de nível de palavra, importância de nível de termo em um corpus e modelos semânticos aprendidos profundos para determinar a similaridade e a relevância entre duas cadeias de caracteres de texto.

## <a name="http-request-and-response-with-endpoint"></a>Solicitação e resposta HTTP com o ponto de extremidade
Quando você publica sua base de dados de conhecimento, o serviço cria um ponto de extremidade HTTP baseado em REST que pode ser integrado ao seu aplicativo, normalmente um bot de bate-papo.

### <a name="the-user-query-request-to-generate-an-answer"></a>A solicitação de consulta do usuário para gerar uma resposta

Uma consulta de usuário é a questão que o usuário final solicita na base de dados de conhecimento, como `How do I add a collaborator to my app?` . A consulta geralmente está em um formato de linguagem natural ou algumas palavras-chave que representam a pergunta, como `help with collaborators` . A consulta é enviada para sua base de dados de conhecimento de uma solicitação HTTP em seu aplicativo cliente.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Você controla a resposta definindo propriedades como [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)e [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Use o [contexto de conversa](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) com a funcionalidade de [várias](../how-to/multiturn-conversation.md) instruções para manter a conversa a fim de refinar as perguntas e respostas, para encontrar a resposta correta e final.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>A resposta de uma chamada para gerar uma resposta

A resposta HTTP é a resposta recuperada da base de dados de conhecimento, com base na melhor correspondência para uma determinada consulta de usuário. A resposta inclui a resposta e a pontuação de previsão. Se você solicitou mais de uma resposta principal com a `top` propriedade, obterá mais de uma resposta principal, cada uma com uma pontuação.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Pontuação de confiança](./confidence-score.md)
