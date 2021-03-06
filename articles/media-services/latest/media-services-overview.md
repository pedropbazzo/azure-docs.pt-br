---
title: Visão geral dos Serviços de Mídia do Azure v3
titleSuffix: Azure Media Services
description: Visão geral de alto nível dos Serviços de Mídia do Microsoft Azure v3 com links para guias de início rápido, tutoriais e exemplos de código.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: mvc
ms.openlocfilehash: 58a142f0be87bb56f9ded9ace5a5143931740e93
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019849"
---
# <a name="azure-media-services-v3-overview"></a>Visão geral dos Serviços de Mídia do Azure v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Os Serviços de Mídia do Azure são uma plataforma baseada em nuvem que permite a criação de soluções que apresentem uma transmissão de vídeo com qualidade de difusão, aumentem a acessibilidade e a distribuição, analisem o conteúdo e muito mais. Se você é desenvolvedor de aplicativos, call center, órgão governamental ou empresa de entretenimento, os Serviços de Mídia podem ajudar a criar aplicativos que oferecem experiências de mídia de qualidade superior para um público amplo, nos dispositivos móveis e navegadores mais populares da atualidade.

Os SDKs dos Serviços de Mídia v3 são baseados na [Especificação de OpenAPI dos Serviços de Mídia v3 (Swagger)](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> No momento, você pode usar o [portal do Azure](https://portal.azure.com/) para: gerenciar os [Eventos ao vivo](live-events-outputs-concept.md) dos Serviços de Mídia v3, exibir (não gerenciar) [Ativos](assets-concept.md) do v3 e [obter informações sobre como acessar APIs](./access-api-howto.md). Para todas as outras tarefas de gerenciamento (por exemplo, [Transformações e Trabalhos](transforms-jobs-concept.md) e [Proteção de conteúdo](content-protection-overview.md)), use a [API REST](/rest/api/media/), a [CLI](/cli/azure/ams) ou um dos [SDKs](media-services-apis-overview.md#sdks) com suporte.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança

Como um lembrete importante, você precisará respeitar todas as leis aplicáveis em seu uso dos Serviços de Mídia do Azure e não poderá usar os Serviços de Mídia ou qualquer serviço do Azure de maneira que viole os direitos de outras pessoas ou que possa ser prejudicial a outras pessoas.

Antes de carregar qualquer vídeo/imagem nos Serviços de Mídia, você precisará ter todos os direitos adequados para usar o vídeo/a imagem, incluindo, quando exigido por lei, todos os consentimentos necessários de indivíduos (se houver) no vídeo/na imagem, para uso, processamento e armazenamento dos respectivos dados nos Serviços de Mídia e no Azure. Algumas jurisdições podem impor requisitos legais especiais para a coleta, o processamento online e o armazenamento de determinadas categorias de dados, como dados biométricos. Antes de usar os Serviços de Mídia e o Azure para o processamento e o armazenamento de dados sujeitos a requisitos legais especiais, você precisará garantir a conformidade com os requisitos legais que possam se aplicar a você.

Para saber mais sobre conformidade, privacidade e segurança nos Serviços de Mídia, acesse a [Central de Confiabilidade](https://www.microsoft.com/trust-center/?rtc=1) da Microsoft. Para saber mais sobre as obrigações de privacidade, as práticas de tratamento e retenção de dados pela Microsoft, incluindo como excluir seus dados, examine a [Política de Privacidade](https://privacy.microsoft.com/PrivacyStatement), o “OST” [(Termos do Online Services)](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) e o “DPA” [(Adendo de Processamento de Dados)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) da Microsoft. Ao usar os Serviços de Mídia, você concorda em vincular-se ao OST, ao DPA e à política de privacidade.
 
## <a name="what-can-i-do-with-media-services"></a>O que posso fazer com os Serviços de Mídia?

Os Serviços de Mídia permitem criar diversos fluxos de trabalho de mídia na nuvem. Entre os exemplos do que você pode fazer com os Serviços de Mídia estão:

* Fornecer vídeos em vários formatos, para que eles possam ser reproduzidos em uma série de navegadores e dispositivos. Para a entrega de vídeos sob demanda e transmissões ao vivo para vários clientes, como dispositivos móveis, TV, computador etc., é necessário codificar e empacotar o conteúdo de áudio e vídeo de forma apropriada. Para ver como fornecer e transmitir esse tipo de conteúdo, confira [Início Rápido: Codificar e transmitir arquivos](stream-files-dotnet-quickstart.md).
* Transmita eventos esportivos ao vivo para um público amplo online, como jogos de futebol, basebol, esportes universitários e escolares, e muito mais.
* Transmita reuniões públicas e eventos, como fóruns, reuniões da câmara de vereadores e órgãos legislativos.
* Analise o conteúdo do áudio ou vídeo gravado. Por exemplo, para alcançar maior satisfação dos clientes, as organizações podem extrair a conversão de fala em texto e criar índices de pesquisa e painéis. Em seguida, elas podem extrair dados inteligentes sobre reclamações comuns, fontes de reclamações e outros dados relevantes.
* Crie um serviço de assinatura de vídeo e transmita conteúdo protegido por DRM quando um cliente (por exemplo, um estúdio de cinema) precisar restringir o acesso e uso de um trabalho protegido por direitos autorais.
* Forneça conteúdo offline para reprodução em aviões, trens e automóveis. Talvez um cliente precise baixar o conteúdo no telefone ou tablet para reprodução quando ele estiver desconectado da rede.
* Implemente uma plataforma de vídeo educacional de E-Learning com os Serviços de Mídia do Microsoft Azure e a [API de Serviços Cognitivos do Azure](../../index.yml?pivot=products&panel=ai) para fazer a conversão de fala em texto de legenda, traduzir para vários idiomas, e muito mais.
* Use os Serviços de Mídia do Azure junto com a [API de Serviços Cognitivos do Azure](../../index.yml?pivot=products&panel=ai) para adicionar subtítulos e legendas a vídeos para abranger um público mais amplo (por exemplo, pessoas com deficiência auditiva ou que desejam acompanhar a leitura em um idioma diferente).
* Habilite a CDN do Azure para conseguir um dimensionamento grande para lidar melhor com altas cargas instantâneas (por exemplo, o início de um evento de lançamento de produto).

## <a name="how-can-i-get-started-with-v3"></a>Como posso começar a v3? 

Saiba como codificar e empacotar conteúdo, transmitir vídeos sob demanda, fazer transmissões ao vivo e analisar vídeos com os Serviços de Mídia v3. Tutoriais, referências de API e outras documentações mostram como oferecer com segurança, sob demanda e ao vivo, fluxos de áudio ou vídeos que são dimensionados para milhões de usuários.

> [!TIP]
> Antes de iniciar o desenvolvimento, examine:<br/>* [Conceitos fundamentais](concepts-overview.md) (inclui conceitos importantes, como empacotamento, codificação e proteção).<br/>* [Desenvolvimento com APIs dos Serviços de Mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura etc.).

### <a name="sdks"></a>SDKs

Comece desenvolvendo com os [SDKs de cliente dos Serviços de Mídia do Azure v3](media-services-apis-overview.md#sdks).

### <a name="quickstarts"></a>Inícios rápidos  

Os inícios rápidos mostram instruções iniciais básicas para que os novos clientes experimentem os Serviços de Mídia rapidamente.

* [Transmitir arquivos de vídeo – .NET](stream-files-dotnet-quickstart.md)
* [Transmitir arquivos de vídeo – CLI](stream-files-cli-quickstart.md)
* [Transmitir arquivos de vídeo – Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Tutoriais

Os tutoriais mostram os procedimentos baseados em cenário para algumas das principais tarefas dos Serviços de Mídia.

* [Codificar o arquivo remoto e o fluxo de vídeo – REST](stream-files-tutorial-with-rest.md)
* [Codificar o arquivo carregado e o fluxo de vídeo – .NET](stream-files-tutorial-with-api.md)
* [Transmitir ao vivo – .NET](stream-live-tutorial-with-api.md)
* [Analisar vídeo – .NET](analyze-videos-tutorial-with-api.md)
* [Criptografia dinâmica AES-128 – .NET](protect-with-aes128.md)

### <a name="samples"></a>Exemplos

Use [este navegador de exemplo](/samples/browse/?products=azure-media-services) para procurar exemplos de código dos Serviços de Mídia do Azure.

### <a name="how-to-guides"></a>Guias de instruções

Os guias de instruções contêm exemplos de código que demonstram como concluir uma tarefa. Você encontrará vários exemplos nesta seção. Veja algumas opções:

* [Criar uma conta – CLI](./create-account-howto.md)
* [Acessar as APIs – CLI](./access-api-howto.md)
* [Codificar com HTTPS como entrada de trabalho – .NET](job-input-from-http-how-to.md)  
* [Monitorar eventos – Portal](monitor-events-portal-how-to.md)
* [Criptografar dinamicamente com vários DRM – .NET](protect-with-drm.md) 
* [Como codificar com uma transformação personalizada – CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre conceitos fundamentais](concepts-overview.md)