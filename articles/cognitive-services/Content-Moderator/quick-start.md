---
title: 'Início Rápido: Experimentar o Content Moderator na Web'
titleSuffix: Azure Cognitive Services
description: Use a ferramenta online de Análise do Content Moderator para testar a funcionalidade básica do Content Moderator sem precisar escrever nenhum código.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, moderação de conteúdo
ms.openlocfilehash: 025c8fcf98a31d7b3380ee2530428d08428493fb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596803"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Início Rápido: Experimentar o Content Moderator na Web

Neste início rápido, você usará a ferramenta de revisão online Content Moderator para testar a funcionalidade básica do Content Moderator sem precisar escrever nenhum código. Se você deseja integrar esse serviço ao aplicativo de moderação de conteúdo mais rapidamente, confira os outros guias de início rápido na seção [Próximas etapas](#next-steps).

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador da Web

## <a name="set-up-the-review-tool"></a>Configurar a ferramenta de Análise

A Ferramenta de Revisão do Content Moderator é uma ferramenta baseada na Web que permite que revisores humanos auxiliem o serviço cognitivo na tomada de decisões. Neste guia, você passará pelo curto processo de configuração da ferramenta de revisão para poder ver como o serviço Moderador de Conteúdo funciona. Vá para o site da [Ferramenta de Revisão do Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e inscreva-se.

![Home Page do Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Criar uma equipe de análise

Em seguida, crie uma equipe de revisão. Em um cenário de trabalho, esse será o grupo de pessoas que analisará manualmente as decisões de moderação do serviço. Para criar uma equipe, você precisará selecionar uma **Região** e fornecer um **Nome da Equipe** e uma **ID da Equipe**. Se você deseja convidar colegas para a equipe, pode fazê-lo digitando seus endereços de e-mail aqui.

> [!NOTE]
> O **Nome da Equipe** é um nome amigável para sua equipe de revisão. Esse é o nome exibido no portal do Azure. A **ID da Equipe** é o que é usado para identificar programaticamente sua equipe de revisão.

> [!div class="mx-imgBorder"]
> ![Convidar membro da equipe](images/create-team.png)

Se optar por criptografar dados usando uma CMK (chave gerenciada pelo cliente), você deverá fornecer a **ID do Recurso** para seu recurso do Content Moderator no tipo de preço E0. O recurso que você fornecer deverá ser novo. 

> [!div class="mx-imgBorder"]
> ![Convidar membro da equipe com a CMK](images/create-team-cmk.png)

Se tentar reutilizar um recurso do Content Moderator, você verá este aviso: 

> [!div class="mx-imgBorder"]
> ![Falha da CMK](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Carregar o conteúdo de exemplo

Agora você está pronto para carregar o conteúdo de exemplo. Selecione **tente > imagem**, **tente > texto**, ou **tente > vídeo**.

![Experimentar Imagem ou Moderação de Texto](images/tryimagesortext.png)

Envie seu conteúdo para moderação. Internamente, a ferramenta de análise chama as APIs de moderação para verificar seu conteúdo. Quando a varredura estiver concluída, você verá uma mensagem informando que há resultados aguardando sua análise.

![Moderar arquivos](images/submitted.png)

## <a name="review-moderation-tags"></a>Revisar tags de moderação

Revise as tags de moderação aplicadas. Você pode ver quais tags foram aplicadas ao seu conteúdo e qual foi a pontuação em cada categoria. Consulte a [imagem](image-moderation-api.md), [texto](text-moderation-api.md), e [vídeo](video-moderation-api.md) indicam tópicos de moderação para saber mais sobre as marcas um conteúdo diferente.

![Analisar resultados](images/reviewresults_text.png)

Em um projeto, você ou sua equipe de revisão podem alterar essas tags ou adicionar mais tags, conforme necessário. Você enviará essas alterações com o botão **Próximo**. À medida que seu aplicativo de negócios chama as APIs do moderador, o conteúdo marcado ficará na fila aqui, pronto para ser analisado pelas equipes de revisão humanas. Você pode revisar rapidamente grandes volumes de conteúdo usando essa abordagem.

Neste ponto, você usou a Ferramenta de Revisão do Content Moderator para ver exemplos do que o serviço Content Moderator pode fazer. Em seguida, você pode aprender mais sobre a ferramenta de revisão e como integrá-la em um projeto de software usando as APIs de revisão ou pular para a seção [Próximas etapas](#next-steps) para aprender a usar as próprias APIs de moderação seu aplicativo.

## <a name="learn-more-about-the-review-tool"></a>Saiba mais sobre a ferramenta de revisão

Para saber mais sobre como usar a Ferramenta Revisão do Content Moderator, consulte o guia [Ferramenta de revisão](Review-Tool-User-Guide/human-in-the-loop.md) e veja as APIs da ferramenta de revisão para saber como ajustar a experiência de revisão humana:
- A [API de Trabalho](try-review-api-job.md) examina o conteúdo por meio de APIs de moderação e gera análises na ferramenta de análise. 
- A [API de Revisão](try-review-api-review.md) cria diretamente análises de imagem, texto ou vídeo para moderadores humanos sem primeiro examinar o conteúdo. 
- A [API do Fluxo de Trabalho](try-review-api-workflow.md) cria, atualiza e obtém detalhes sobre os fluxos de trabalho personalizados que sua equipe cria.

Ou então, continue com as próximas etapas para começar a usar as APIs de moderação em seu código.

## <a name="next-steps"></a>Próximas etapas

Aprenda a usar as próprias APIs de moderação no seu aplicativo.
- Implemente a moderação de imagem. Use o [console de API](try-image-api.md) ou siga o [guia de início rápido da biblioteca de clientes](client-libraries.md) para examinar as imagens e detectar conteúdo adulto e racista em potencial usando marcas, pontuações de confiança e outras informações extraídas.
- Implementar a moderação de texto. Use o [console de API](try-text-api.md) ou o [guia de início rápido da biblioteca de clientes](client-libraries.md) para examinar o conteúdo de texto para encontrar conteúdo ofensivo, classificação de textos indesejados assistida por computador (versão prévia) e dados pessoais.
- Implementar a moderação de vídeo. Siga as [guia de instruções de moderação de vídeo para C# ](video-moderation-api.md) para verificar a vídeos e detectar possível conteúdo adulto. 
