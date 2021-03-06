---
title: A CLI de fala do Azure
titleSuffix: Azure Cognitive Services
description: A CLI de fala é uma ferramenta de linha de comando para usar o serviço de fala sem escrever nenhum código. A CLI de fala requer uma configuração mínima, e é fácil começar imediatamente a experimentar com os principais recursos do serviço de fala para ver se os casos de uso podem ser atendidos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067419"
---
# <a name="what-is-the-speech-cli"></a>O que é a CLI de Fala?

A CLI de fala é uma ferramenta de linha de comando para usar o serviço de fala sem escrever nenhum código. A CLI de fala requer configuração mínima e é fácil começar imediatamente a experimentar com os principais recursos do serviço de fala para ver se os casos de uso podem ser atendidos. Em alguns minutos, você pode executar fluxos de trabalho de teste simples, como reconhecimento de fala em lotes, de um diretório de arquivos ou de conversão de texto em uma coleção de cadeias de caracteres de um arquivo. Além dos fluxos de trabalho simples, a CLI de fala está pronta para produção e pode ser expandida para executar processos maiores usando `.bat` scripts de shell ou automatizados.

A maioria dos principais recursos do SDK de fala está disponível na CLI de fala, e alguns recursos e personalizações avançados são simplificados na CLI de fala. Considere as diretrizes a seguir para decidir quando usar a CLI de fala ou o SDK de fala.

Use a CLI de fala quando:
* Você deseja experimentar os recursos do Speech Service com a configuração mínima e nenhum código
* Você tem requisitos relativamente simples para um aplicativo de produção usando o serviço de fala

Use o SDK de fala quando:
* Você deseja integrar a funcionalidade do serviço de fala em uma linguagem ou plataforma específica (por exemplo, C#, Python, C++)
* Você tem requisitos complexos que podem exigir solicitações de serviço avançadas ou o desenvolvimento de comportamento personalizado, incluindo streaming de resposta

## <a name="core-features"></a>Principais recursos

* Reconhecimento de fala – converter a fala em texto de arquivos de áudio ou diretamente de um microfone, ou transcrever uma conversa gravada.

* Síntese de fala – converta conversão de texto em fala usando a entrada de arquivos de texto ou a entrada diretamente da linha de comando. Personalize as características de saída de fala usando [as configurações de SSML e as](speech-synthesis-markup.md) [vozes padrão ou neural](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Tradução de fala – traduza o áudio em um idioma de origem para texto ou áudio em um idioma de destino.

* Executar em recursos de computação do Azure – envie comandos da CLI de fala para serem executados em um recurso de computação remota do Azure usando `spx webjob` .

## <a name="get-started"></a>Introdução

Para começar a usar a CLI de fala, consulte o [artigo básico](spx-basics.md). Este artigo mostra como executar alguns comandos básicos e também mostra comandos ligeiramente mais avançados para execução de operações em lote para a conversão de texto em texto e para fala. Depois de ler o artigo básico, você deve ter um entendimento suficiente da sintaxe para começar a escrever alguns comandos personalizados ou automatizar as operações de serviço de fala simples.

## <a name="next-steps"></a>Próximas etapas

- [Noções básicas da CLI de fala](spx-basics.md)
- Se o seu caso de uso for mais complexo, [obtenha o SDK de fala](speech-sdk.md)
