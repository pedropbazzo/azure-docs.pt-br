---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 5857e88a0d8392d9c20ed1b1e9b19b31c83a51fd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332404"
---
Neste início rápido, você aprende os padrões de design comuns para fazer uma síntese da conversão de texto em fala usando o SDK de Fala. Você começa fazendo a configuração e a sintetização básicas e passa para exemplos mais avançados de desenvolvimento de aplicativos personalizados, incluindo:

* Obter respostas como fluxos na memória
* Personalizar a taxa de bits e a taxa de amostragem de saída
* Enviar solicitações de sintetização usando SSML (linguagem de marcação de sintetização de voz)
* Usar vozes neurais

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Para fazer qualquer coisa, instale o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK de Fala para JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Dependendo de sua plataforma, use as seguintes instruções:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador da Web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Além disso, dependendo do ambiente de destino, use um dos seguintes:

# <a name="script"></a>[script](#tab/script)

Baixe e extraia o arquivo *microsoft.cognitiveservices.speech.sdk.bundle.js* do <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">SDK de Fala para JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> e coloque-o em uma pasta acessível para o arquivo HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver direcionando a um navegador da Web e usando a tag `<script>`, o prefixo `sdk` não será necessário. O prefixo `sdk` é um alias usado para dar nome ao módulo `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Para obter mais informações sobre `import`, confira <a href="https://javascript.info/import-export" target="_blank">exportar e importar <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para obter mais informações sobre `require`, confira <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário?<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---


## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true). Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados.

> [!NOTE]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

Há algumas maneiras de inicializar um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true):

* Com uma assinatura: passe uma chave e a região associada.
* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

Neste exemplo, você cria um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) usando uma chave de assinatura e uma região. Confira a página [suporte a regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o identificador de sua região. Você também cria um código clichê básico a ser usado no restante do artigo e o modifica para personalizações diferentes.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizar fala em um arquivo

Em seguida, você cria um objeto [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest&preserve-view=true), que executa conversões de texto em fala e gera saídas para alto-falantes, arquivos ou outros fluxos de saída. O [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest&preserve-view=true) aceita como parâmetros o objeto [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) criado na etapa anterior e um objeto [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true), que especifica como os resultados de saída devem ser tratados.

Para começar, crie um `AudioConfig` para gravar automaticamente a saída em um arquivo `.wav` usando a função estática `fromAudioFileOutput()`.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Em seguida, instancie um `SpeechSynthesizer` passando os objetos `speechConfig` e `audioConfig` como parâmetros. Em seguida, para executar a sintetização de voz e gravar em um arquivo, basta executar `speakTextAsync()` com uma cadeia de caracteres de texto. O retorno de chamada de resultado é um ótimo lugar para chamar `synthesizer.close()`; na verdade, essa chamada é necessária para que a síntese funcione corretamente.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Execute o programa e um arquivo `.wav` sintetizado é gravado na localização especificada. Esse é um bom exemplo do uso mais básico, mas, depois, você examina a personalização da saída e a manipulação da resposta de saída como um fluxo na memória para trabalhar com cenários personalizados.

## <a name="synthesize-to-speaker-output"></a>Sintetizar para saída em alto-Falante

Em alguns casos, você pode querer que a fala sintetizada saia diretamente para um alto-falante. Para fazer isso, crie uma instância de `AudioConfig` usando a função estática `fromDefaultSpeakerOutput()`. Isso gera a saída no dispositivo de saída que está ativo.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Obter o resultado como um fluxo na memória

Para muitos cenários de desenvolvimento de aplicativos de fala, é provável que você precise dos dados de áudio resultantes como um fluxo na memória, em vez de gravar diretamente em um arquivo. Isso permite criar comportamentos personalizados, incluindo:

* Abstrair a matriz de bytes resultante como um fluxo pesquisável para serviços downstream personalizados.
* Integrar o resultado com outras APIs ou serviços.
* Modificar os dados de áudio, gravar cabeçalhos de `.wav` personalizados etc.

É simples fazer essa alteração no exemplo anterior. Primeiro, remova o bloco `AudioConfig`, pois daqui em diante você gerenciará o comportamento de saída manualmente para ter mais controle. Em seguida, passe `undefined` para o `AudioConfig` no construtor `SpeechSynthesizer`. 

> [!NOTE]
> Passar `undefined` para o `AudioConfig`, em vez de omiti-lo como no exemplo da saída para o alto-falante acima, não reproduzirá o áudio por padrão no dispositivo de saída ativo.

Desta vez, salve o resultado em uma variável [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest&preserve-view=true). A propriedade `SpeechSynthesisResult.audioData` retorna um `ArrayBuffer` dos dados de saída. Você pode trabalhar com esse `ArrayBuffer` manualmente.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Daqui, você pode implementar qualquer comportamento personalizado usando o objeto `ArrayBuffer` resultante.

## <a name="customize-audio-format"></a>Personalizar o formato de áudio

A seguinte seção mostra como personalizar os atributos da saída de áudio, incluindo:

* Tipo de arquivo de áudio
* Taxa de amostragem
* Profundidade de bits

Para alterar o formato do áudio, use a propriedade `speechSynthesisOutputFormat` no objeto `SpeechConfig`. Essa propriedade espera um `enum` do tipo [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest&preserve-view=true), que você usa para selecionar o formato de saída. Confira os documentos de referência para ver uma lista dos [formatos de áudio](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest&preserve-view=true) disponíveis.

Há várias opções de diferentes tipos de arquivo dependendo de seus requisitos. Observe que, por definição, formatos brutos como `Raw24Khz16BitMonoPcm` não incluem cabeçalhos de áudio. Use formatos brutos somente quando souber que sua implementação downstream pode decodificar um fluxo de bits fragmentado ou se você planejar criar cabeçalhos manualmente com base na profundidade de bits, na taxa de amostragem, no número de canais etc.

Neste exemplo, você especifica um formato RIFF de alta fidelidade `Riff24Khz16BitMonoPcm` definindo o `speechSynthesisOutputFormat` no objeto `SpeechConfig`. Semelhante ao exemplo na seção anterior, obtenha os dados `ArrayBuffer` de áudio e interaja com eles.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Executar o programa novamente gravará um arquivo `.wav` no caminho especificado.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Usar SSML para personalizar as características de fala

A linguagem SSML (linguagem marcação de sintetização de voz) permite ajustar a densidade, a pronúncia, a taxa de fala, o volume e outras características da saída da conversão de texto em fala enviando suas solicitações de um esquema XML. Esta seção mostra alguns exemplos de uso práticos, mas, para ver um guia mais detalhado, confira o [artigo de instruções sobre SSML](../../../speech-synthesis-markup.md).

Para começar a usar SSML para personalização, você faz uma alteração simples que muda a voz.
Primeiro, crie um arquivo XML para a configuração SSML no diretório do projeto raiz, neste exemplo, `ssml.xml`. O elemento raiz sempre é `<speak>`, e encapsular o texto em um elemento `<voice>` permite alterar a voz usando o parâmetro `name`. Este exemplo altera a voz para uma voz masculina que fala inglês (Reino Unido). Observe que essa é uma voz **padrão**, que tem preços e disponibilidades diferentes das vozes **neurais**. Confira a [lista completa](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) de vozes **padrão** com suporte.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Em seguida, você precisa alterar a solicitação de sintetização de voz para fazer referência ao arquivo XML. A solicitação é basicamente a mesma, mas em vez de usar a função `speakTextAsync()`, você usa `speakSsmlAsync()`. Essa função espera uma cadeia de caracteres XML, portanto, primeiro você cria uma função para carregar um arquivo XML e retorná-lo como uma cadeia de caracteres.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Para obter mais informações sobre `readFileSync`, confira <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Sistema de arquivos do Node.js<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Desse ponto em diante, o objeto resultante é exatamente o mesmo que nos exemplos anteriores.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

A saída funciona, mas há mais algumas alterações simples que você pode fazer para ajudá-la a parecer mais natural. A velocidade geral de fala está um pouco rápida, então vamos adicionar uma marca `<prosody>` e reduzir a velocidade para **90%** da taxa padrão. Além disso, a pausa após a vírgula na frase é curta demais e não soa natural. Para corrigir esse problema, adicione uma marca `<break>` para atrasar a fala e configure o parâmetro de tempo como **200 ms**. Execute a síntese novamente para ver como essas personalizações afetaram a saída.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Vozes neurais

As vozes neurais são algoritmos de sintetização de voz que usam a tecnologia das redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para alternar para uma voz neural, altere o `name` para uma das [opções de voz neural](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Em seguida, adicione um namespace de XML para `mstts` e encapsule o texto na marca `<mstts:express-as>`. Use o parâmetro `style` para personalizar o estilo de fala. Este exemplo usa `cheerful`, mas tente defini-lo como `customerservice` ou `chat` para ver a diferença no estilo de fala.

> [!IMPORTANT]
> As vozes neurais têm suporte **somente** com os recursos de Fala criados nas regiões *Leste dos EUA*, *Sudeste da Ásia* e *Oeste da Europa*.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
