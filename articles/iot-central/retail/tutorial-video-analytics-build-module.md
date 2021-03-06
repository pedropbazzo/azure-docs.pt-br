---
title: Tutorial – Modificar os módulos da Análise Dinâmica de Vídeo do Azure IoT Edge
description: Este tutorial mostra como modificar e criar os módulos de gateway da Análise Dinâmica de Vídeo usados pelo modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 4bbc25f98f494660fc4997af1c3a248172ef08d2
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873465"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Tutorial: Modificar e criar os módulos de gateway da Análise Dinâmica de Vídeo

Este tutorial mostra como modificar o código dos módulos da LVA (Análise Dinâmica de Vídeo) do IoT Edge.

Os tutoriais anteriores usam imagens predefinidas dos módulos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, você precisará do seguinte:

* [Node.js](https://nodejs.org/en/download/) v10 ou posterior
* [Visual Studio Code](https://code.visualstudio.com/Download) com a extensão do [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) instalada
* Mecanismo do [Docker](https://www.docker.com/products/docker-desktop)
* Um [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) para hospedar suas versões dos módulos.
* Uma conta dos [Serviços de Mídia do Azure](https://docs.microsoft.com/azure/media-services/). Se você concluiu os tutoriais anteriores, reutilize aquele que criou anteriormente.

## <a name="clone-the-repository"></a>Clonar o repositório

Caso ainda não tenha clonado o repositório, use o seguinte comando para cloná-lo em uma localização adequada no computador local:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Abra a pasta do repositório local *live-video-analytics* com o VS Code.

## <a name="edit-the-deploymentamd64json-file"></a>Edite o arquivo deployment.amd64.json

1. Caso ainda não tenha feito isso, crie uma pasta chamada *ref-apps/lva-edge-iot-central-gateway/storage* na cópia local do repositório **lva-gateway**. Essa pasta é ignorada pelo Git para evitar que você faça check-in acidental de informações confidenciais.
1. Copie o arquivo *deployment.amd64.json* da pasta *setup* para a pasta *storage*.
1. No VS Code, abra o arquivo *storage/deployment.amd64.json*.
1. Edite a seção `registryCredentials` para adicionar suas credenciais do Registro de Contêiner do Azure.
1. Edite a seção do módulo `LvaEdgeGatewayModule` para adicionar o nome da imagem e da sua conta do AMS no `env:amsAccountName:value`.
1. Edite a seção do módulo `lvaYolov3` e adicione o nome da imagem.
1. Edite a seção do módulo `lvaEdge` e adicione o nome da imagem.
1. Confira [Criar um aplicativo de análise de vídeo no Azure IoT Central](tutorial-video-analytics-create-app-yolo-v3.md) para obter mais informações sobre como concluir a configuração.

## <a name="build-the-code"></a>Compilar o código

1. Antes de tentar criar o código pela primeira vez, use o terminal do VS Code para executar o comando `npm install`. Esse comando instala os pacotes necessários e executa os scripts de instalação.

    > [!TIP]
    > Se você efetuar pull de uma versão mais recente do repositório GitHub do repositório, exclua a pasta *node_modules* e execute `npm install` novamente.

1. Edite o arquivo *./setup/imageConfig.json* para atualizar a imagem nomeada com base no nome do registro de contêiner:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Use o terminal do VS Code para executar o comando `docker login [your server].azurecr.io`. Use as mesmas credenciais que você forneceu no manifesto de implantação para os módulos.

1. Use o terminal do VS Code para executar o comando **npm version patch**. Esse script de build implanta as imagens no registro de contêiner. A saída na janela do terminal do VS Code mostra se o build foi bem-sucedido.

1. A versão da imagem do **LvaEdgeGatewayModule** é incrementada toda vez que o build é concluído. Você precisará usar essa versão no arquivo de manifesto de implantação.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu mais sobre o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo e os módulos LVA do IoT Edge, a próxima etapa sugerida é aprender a:

> [!div class="nextstepaction"]
> [Como criar soluções de varejo com o Azure IoT Central](overview-iot-central-retail.md)
