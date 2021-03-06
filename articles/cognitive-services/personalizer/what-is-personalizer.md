---
title: O que é o Personalizador?
description: O Personalizador é um serviço baseado em nuvem que permite escolher a melhor experiência a ser mostrada aos usuários, aprendendo com o comportamento deles em tempo real.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizer, Azure personalizer, machine learning
ms.openlocfilehash: ae17b799c2b222525db53d5bb8e0afdbbcf19975
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777234"
---
# <a name="what-is-personalizer"></a>O que é o Personalizador?

O Personalizador do Azure é um serviço baseado em nuvem que ajuda os seus aplicativos a escolherem o melhor item de conteúdo para mostrar aos usuários. Você pode usar o serviço do Personalizador para determinar qual produto deve ser sugerido aos compradores ou para descobrir o posicionamento ideal para um anúncio. Depois que o conteúdo é mostrado ao usuário, o sistema monitora o comportamento do usuário em tempo real e relata uma pontuação de recompensa ao serviço do Personalizador. Isso garante o aprimoramento contínuo do modelo de machine learning e a capacidade do Personalizador de selecionar o melhor item de conteúdo com base nas informações contextuais recebidas.

> [!TIP]
> O conteúdo é qualquer unidade de informação, como texto, imagens, URLs, emails ou qualquer outro item que você deseje selecionar e para mostrar aos usuários.

Antes de começar, sinta-se à vontade para experimentar o [Personalizador com esta demonstração interativa](https://personalizationdemo.azurewebsites.net/).

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Como o Personalizador seleciona o melhor item de conteúdo?

O Personalizador usa o **aprendizado por reforço** para selecionar o melhor item (_ação_) com base no comportamento coletivo e nas pontuações de recompensa entre todos os usuários. As ações são os itens de conteúdo, como artigos de notícias, filmes específicos ou produtos.

A chamada de **Classificação** usa o item de ação, em conjunto com recursos da ação, bem como recursos de contexto para selecionar o item de ação superior:

* **Ações com recursos** – itens de conteúdo com recursos específicos a cada item
* **Recursos de contexto** – recursos de seus usuários, seu contexto ou seu ambiente ao usar o aplicativo

A chamada de Classificação retorna a ID cujo item de conteúdo ou __ação__ será mostrado ao usuário no campo **ID da Ação de Recompensa**.

A __ação__ mostrada ao usuário é escolhida com modelos de machine learning, que tentam maximizar a quantidade total de recompensas ao longo do tempo.

### <a name="sample-scenarios"></a>Exemplo de cenários

Vamos dar uma olhada em alguns cenários em que o Personalizador pode ser usado para selecionar o melhor conteúdo a ser renderizado para um usuário.

|Tipo de conteúdo|Ações (com recursos)|Recursos de contexto|ID da Ação de Recompensa Retornada<br>(exibir este conteúdo)|
|--|--|--|--|
|Lista de notícias|a. `The president...` (nacional, política, [texto])<br>b. `Premier League ...` (global, esportes, [texto, imagem, vídeo])<br> c. `Hurricane in the ...` (regional, clima, [texto, imagem]|Dispositivo do qual as notícias são lidas<br>Mês ou temporada<br>|um `The president...`|
|Lista de filmes|1. `Star Wars` (1977, [ação, aventura, fantasia], George Lucas)<br>2. `Hoop Dreams` (1994, [documentário, esportes], Steve James<br>3. `Casablanca` (1942, [romance, drama, guerra], Michael Curtiz)|Dispositivo no qual o filme é assistido<br>tamanho da tela<br>Tipo de usuário<br>|3. `Casablanca`|
|Lista de produtos|i. `Product A` (3 kg, $$$$, entrega em 24 horas)<br>ii. `Product B` (20 kg, $$, entrega em 2 semanas com alfândega)<br>III. `Product C` (3 kg, $$$, entrega em 48 horas)|Dispositivo no qual a compra é lida<br>Camada de gastos do usuário<br>Mês ou temporada|ii. `Product B`|

O Personalizador usou o aprendizado por reforço para selecionar a melhor ação, conhecida como _ID da ação de recompensa_. O modelo de machine learning usa: 

* Um modelo treinado – Informações recebidas anteriormente do serviço de personalização usadas para aprimorar o modelo de machine learning
* Dados atuais – Ações específicas com recursos e recursos de contexto

## <a name="when-to-use-personalizer"></a>Quando usar o Personalizador

A [API](https://go.microsoft.com/fwlink/?linkid=2092082) de **Classificação** do Personalizador será chamada sempre que o seu aplicativo apresentar conteúdo. Isso é conhecido como um **evento**, indicado com uma _ID do evento_.

A **API** de [Recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) do Personalizador pode ser chamada em tempo real ou atrasada para se adequar melhor à sua infraestrutura. Você determina a pontuação de recompensa com base em suas necessidades empresariais. A pontuação de recompensa está entre 0 e 1. Ela pode ter um valor único, como 1 para bom e 0 para ruim ou um número produzido por um algoritmo criado considerando suas metas e métricas empresariais.

## <a name="content-requirements"></a>Requisitos de conteúdo

Use o Personalizador quando seu conteúdo:

* Tiver um conjunto limitado de itens (máximo de 50) entre os quais selecionar. Se tiver uma lista maior, [use um mecanismo de recomendação](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) para reduzir a lista a 50 itens.
* Tiver informações que descrevem o conteúdo que você deseja classificar: _ações com recursos_ e _recursos de contexto_.
* Tiver um mínimo de mil eventos relacionados a conteúdo por dia para que o Personalizador seja eficaz. Se o Personalizador não receber o tráfego mínimo necessário, o serviço levará mais tempo para determinar o melhor item de conteúdo.

Como o Personalizador usa informações coletivas quase em tempo real para retornar o melhor item de conteúdo, o serviço não:
* Persiste e gerencia informações de perfil do usuário
* Registra em log as preferências ou o histórico de usuários individuais
* Exige conteúdo limpo e rotulado

## <a name="how-to-design-for-and-implement-personalizer"></a>Como projetar para o Personalizador e implementá-lo

1. [Projete](concepts-features.md) e planeje para conteúdo, **_ações_** e **_contexto_**. Determine o algoritmo de recompensa para a pontuação de **_Recompensa_**.
1. Cada [Recurso do Personalizador](how-to-settings.md) criado é considerado um Loop de Aprendizado. O loop receberá as chamadas de Classificação e Recompensa para o conteúdo ou a experiência do usuário.

    |Tipo de recurso| Finalidade|
    |--|--|
    |[Modo de aprendiz](concept-apprentice-mode.md) `E0`|Treine o modelo do Personalizador sem afetar o aplicativo existente, em seguida, implante o Comportamento de aprendizado online em um ambiente de produção|
    |Padrão, `S0`|Comportamento de aprendizado online em um ambiente de produção|
    |Gratuito, `F0`| Treinar comportamento de aprendizado online em um ambiente não de produção|

1. Adicione o Personalizador ao aplicativo, site ou sistema:
    1. Adicione uma chamada de **Classificação** para o Personalizador em seu aplicativo, site ou sistema para determinar o melhor item de _conteúdo_ antes que o conteúdo seja mostrado ao usuário.
    1. Exiba o melhor item de _conteúdo_, que é a _ID da ação de recompensa_ retornada, para o usuário.
    1. Aplique a _lógica de negócios_ às informações coletadas sobre como o usuário se comportou para determinar a pontuação de **recompensa**, como:

    |Comportamento|Pontuação de recompensa calculada|
    |--|--|
    |O usuário selecionou o melhor item de _conteúdo_ (ID da ação de recompensa)|**1**|
    |O usuário selecionou outro conteúdo|**0**|
    |O usuário deixou em pausa ou rolou a tela de maneira indecisa antes de selecionar o melhor item de _conteúdo_ (ID da ação de recompensa)|**0.5**|

    1. Adicione uma chamada de **Recompensa** enviando uma pontuação de recompensa entre 0 e 1
        * Imediatamente após mostrar seu conteúdo
        * Ou algum tempo depois em um sistema offline
    1. [Avalie seu loop](concepts-offline-evaluation.md) com uma avaliação offline após um período de uso. Uma avaliação offline permite testar e avaliar a eficácia do serviço do Personalizador sem alterar o código nem afetar a experiência do usuário.

## <a name="complete-a-quickstart"></a>Concluir um guia de início rápido

Oferecemos guias de início rápido em C#, JavaScript e Python. Cada guia de início rápido é projetado para ensinar a você padrões de design básicos e para você executar o seu código em menos de dez minutos. 

* [Início Rápido: Como usar a biblioteca do cliente do Personalizador](sdk-learning-loop.md)

Depois de ter tido a oportunidade de começar a usar o serviço do Personalizador, experimente nossos tutoriais e saiba como usar o Personalizador em aplicativos Web, chatbots ou um Notebook do Azure.

* [Tutorial: Usar o Personalizador em um aplicativo Web do .NET](tutorial-use-personalizer-web-app.md)
* [Tutorial: Usar o Personalizador em um chatbot do .NET](tutorial-use-personalizer-chat-bot.md)
* [Tutorial: Usar o Personalizador no Notebook do Azure](tutorial-use-azure-notebook-generate-loop-data.md)

## <a name="reference"></a>Referência 

* [SDK para C#/.NET do Personalizador](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/personalizer?view=azure-dotnet)
* [SDK para Go do Personalizador](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/personalizer/v1.0/personalizer)
* [SDK para JavaScript do Personalizador](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)
* [SDK para Python do Personalizador](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python)
* [APIs REST](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como o Personalizador funciona](how-personalizer-works.md)
> [o que é o Aprendizado por Reforço?](concepts-reinforcement-learning.md)
