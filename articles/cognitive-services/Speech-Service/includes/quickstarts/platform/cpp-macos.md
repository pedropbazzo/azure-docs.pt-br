---
title: 'Início Rápido: Instalação da plataforma C++ do SDK de Fala (macOS) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para C++ no macOS com o SDK do serviço de Fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: d0f9bc44145f75d7c3b503fffe43042c650025f1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "75469748"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para C++ no macOS 10.13 e superior.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos do sistema

macOS 10.13 e superior

## <a name="install-speech-sdk"></a>Instalar SDK de Fala

1. Escolha um diretório para o qual os arquivos do SDK de Fala serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Essa variável facilita fazer referência a esse diretório em futuros comandos. Por exemplo, se você quiser usar o diretório `speechsdk` no seu diretório base, use um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Baixe e extraia os arquivos `.zip` que contêm a estrutura do SDK de Fala:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valide o conteúdo do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A listagem de diretório deve conter o aviso de terceiros e os arquivos de licença, bem como um diretório `MicrosoftCognitiveServicesSpeech.framework`.

Agora é possível avançar para as [Próximas etapas](#next-steps) abaixo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
