---
title: API de áudio longo (visualização)-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como a API de áudio longa é projetada para a síntese assíncrona de texto em fala de longa forma.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: be38d3e78108a15c9f7875a15156e0eeba5a6211
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167752"
---
# <a name="long-audio-api-preview"></a>API de áudio longo (visualização)

A API de áudio de longa duração é projetada para síntese assíncrona de texto em fala de longa forma (por exemplo: livros de áudio, artigos de notícias e documentos). Essa API não retorna áudio sintetizado em tempo real, em vez disso, a expectativa é que você pesquise as respostas e consuma as saídas conforme elas são disponibilizadas pelo serviço. Ao contrário da API de texto para fala usada pelo SDK de fala, a API de áudio de longa duração pode criar áudio sintetizado por mais de 10 minutos, tornando-o ideal para Publicadores e plataformas de conteúdo de áudio.

Benefícios adicionais da API de áudio longa:

* A fala sintetizada retornada pelo serviço usa as melhores vozes neurais.
* Não é necessário implantar um ponto de extremidade de voz, pois ele sintetiza vozes em nenhum modo de lote em tempo real.

> [!NOTE]
> A API de áudio longa agora dá suporte a [vozes neurais públicas](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) e a [vozes neurais personalizadas](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Fluxo de trabalho

Normalmente, ao usar a API de áudio longo, você enviará um arquivo de texto ou arquivos a serem sintetizados, sondando o status e, se o status for bem-sucedido, você poderá baixar a saída de áudio.

Este diagrama fornece uma visão geral de alto nível do fluxo de trabalho.

![Diagrama de fluxo de trabalho da API de áudio longo](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparar conteúdo para síntese

Ao preparar seu arquivo de texto, verifique se:

* É texto sem formatação (. txt) ou o texto SSML (. txt)
* É codificado como [UTF-8 com marca de ordem de byte (bom)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* É um arquivo único, não um zip
* Contém mais de 400 caracteres para texto sem formatação ou 400 [caracteres cobráveis](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) para texto de SSML e menos de 10.000 parágrafos
  * Para texto sem formatação, cada parágrafo é separado ao pressionar o exemplo **Inserir/retornar** -exibir [entrada de texto sem formatação](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Para o texto SSML, cada parte da SSML é considerada um parágrafo. As partes de SSML devem ser separadas por parágrafos diferentes – exibir [exemplo de entrada de texto de SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Para chinês (continente), chinês (RAE de Hong Kong), chinês (Taiwan), japonês e coreano, uma palavra será contada como dois caracteres. 

## <a name="python-example"></a>Exemplo de Python

Esta seção contém exemplos do Python que mostram o uso básico da API de áudio de longa duração. Crie um novo projeto Python usando seu IDE ou editor favorito. Em seguida, copie esse trecho de código em um arquivo chamado `voice_synthesis_client.py` .

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

Essas bibliotecas são usadas para analisar argumentos, construir a solicitação HTTP e chamar a API REST de áudio longo de conversão de texto em fala.

### <a name="get-a-list-of-supported-voices"></a>Obter uma lista de vozes com suporte

Esse código permite obter uma lista completa de vozes para uma região/um ponto de extremidade específico que você pode usar. Adicione o código a `voice_synthesis_client.py` :

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

Execute o script usando o comando `python voice_synthesis_client.py --voices -key <your_key> -region <region>` e substitua os seguintes valores:

* Substitua `<your_key>` por sua chave de assinatura do Serviço de Fala. Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).
* Substituir `<region>` pela região em que o recurso de fala foi criado (por exemplo: `eastus` ou `westus` ). Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).

Você verá uma saída parecida com esta:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Se o parâmetro **PublicVoice** for **true**, a voz será uma voz neural pública. Caso contrário, será uma voz neural personalizada.

### <a name="convert-text-to-speech"></a>Converter texto em fala

Prepare um arquivo de texto de entrada, em texto sem formatação ou de SSML, e adicione o seguinte código a `voice_synthesis_client.py` :

> [!NOTE]
> ' concatenateResult ' é um parâmetro opcional. Se esse parâmetro não estiver definido, as saídas de áudio serão geradas por parágrafo. Você também pode concatenar os áudios em 1 saída definindo o parâmetro. Por padrão, a saída de áudio é definida como riff-16kHz-16 bits-mono-PCM. Para obter mais informações sobre as saídas de áudio com suporte, consulte [formatos de saída de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

Execute o script usando o comando `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` e substitua os seguintes valores:

* Substitua `<your_key>` por sua chave de assinatura do Serviço de Fala. Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).
* Substituir `<region>` pela região em que o recurso de fala foi criado (por exemplo: `eastus` ou `westus` ). Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).
* Substitua `<input>` pelo caminho para o arquivo de texto que você preparou para conversão de texto em fala.
* Substituir `<locale>` pela localidade de saída desejada. Para obter mais informações, consulte [suporte a idiomas](language-support.md#neural-voices).
* Substitua `<voice_guid>` pela voz de saída desejada. Use uma das vozes retornadas pela chamada anterior ao ponto de `/voicesynthesis/voices` extremidade.

Você verá uma saída parecida com esta:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

O resultado contém o texto de entrada e os arquivos de saída de áudio gerados pelo serviço. Você pode baixar esses arquivos em um zip.

> [!NOTE]
> Se você tiver mais de 1 arquivo de entrada, será necessário enviar várias solicitações. Há algumas limitações que precisam estar cientes. 
> * O cliente tem permissão para enviar até **5** solicitações ao servidor por segundo para cada conta de assinatura do Azure. Se ele exceder a limitação, o cliente receberá um código de erro 429 (muitas solicitações). Reduza o valor da solicitação por segundo
> * O servidor tem permissão para executar e enfileirar até **120** solicitações para cada conta de assinatura do Azure. Se ele exceder a limitação, o servidor retornará um código de erro 429 (número excessivo de solicitações). Aguarde e evite enviar uma nova solicitação até que algumas solicitações sejam concluídas

### <a name="remove-previous-requests"></a>Remover solicitações anteriores

O serviço manterá até **20.000** solicitações para cada conta de assinatura do Azure. Se o valor da solicitação exceder essa limitação, remova as solicitações anteriores antes de fazer as novas. Se você não remover as solicitações existentes, receberá uma notificação de erro.

Adicione o seguinte código a `voice_synthesis_client.py`:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

Execute `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` para obter uma lista de solicitações de síntese que você fez. Você verá uma saída como esta:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Para excluir uma solicitação, execute `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` e substitua `<synthesis_id>` por um valor de ID de solicitação retornado da solicitação anterior.

> [!NOTE]
> As solicitações com o status ' running '/' Wait ' não podem ser removidas ou excluídas.

O concluído `voice_synthesis_client.py` está disponível no [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>Códigos de status HTTP

A tabela a seguir detalha os códigos de resposta HTTP e as mensagens da API REST.

| API | Código de status HTTP | Descrição | Solução |
|-----|------------------|-------------|----------|
| Criar | 400 | A síntese de voz não está habilitada nesta região. | Altere a chave de assinatura de fala com uma região com suporte. |
|        | 400 | Somente a assinatura de fala **padrão** para essa região é válida. | Altere a chave de assinatura de fala para o tipo de preço "padrão". |
|        | 400 | Excede o limite de solicitação de 20.000 para a conta do Azure. Remova algumas solicitações antes de enviar novas. | O servidor manterá até 20.000 solicitações para cada conta do Azure. Exclua algumas solicitações antes de enviar novas. |
|        | 400 | Este modelo não pode ser usado na síntese de voz: {ModelId}. | Certifique-se de que o estado de {ModelId} esteja correto. |
|        | 400 | A região da solicitação não corresponde à região do modelo: {ModelId}. | Certifique-se de que a região da {ModelId} corresponda à região da solicitação. |
|        | 400 | A síntese de voz só dá suporte ao arquivo de texto na codificação UTF-8 com o marcador de ordem de byte. | Verifique se os arquivos de entrada estão em codificação UTF-8 com o marcador de ordem de byte. |
|        | 400 | Somente entradas SSML válidas são permitidas na solicitação de síntese de voz. | Verifique se as expressões SSML de entrada estão corretas. |
|        | 400 | O nome de voz {voicename} não foi encontrado no arquivo de entrada. | O nome da voz SSML de entrada não está alinhado com a ID do modelo. |
|        | 400 | O número de parágrafos no arquivo de entrada deve ser menor que 10.000. | Verifique se o número de parágrafos no arquivo é menor que 10.000. |
|        | 400 | O arquivo de entrada deve ter mais de 400 caracteres. | Verifique se o arquivo de entrada excede 400 caracteres. |
|        | 404 | O modelo declarado na definição de síntese de voz não pode ser encontrado: {ModelId}. | Verifique se {ModelId} está correto. |
|        | 429 | Excede o limite de síntese de voz ativo. Aguarde até que algumas solicitações sejam concluídas. | O servidor tem permissão para executar e enfileirar até 120 solicitações para cada conta do Azure. Aguarde e evite enviar novas solicitações até que algumas solicitações sejam concluídas. |
| Todos       | 429 | Há muitas solicitações. | O cliente tem permissão para enviar até 5 solicitações ao servidor por segundo para cada conta do Azure. Reduza o valor da solicitação por segundo. |
| Excluir    | 400 | A tarefa de síntese de voz ainda está em uso. | Você só pode excluir solicitações **concluídas** ou **com falha**. |
| GetByID   | 404 | A entidade especificada não pode ser encontrada. | Verifique se a ID de síntese está correta. |

## <a name="regions-and-endpoints"></a>Regiões e endpoints

A API de áudio longa está disponível em várias regiões com pontos de extremidade exclusivos.

| Região | Ponto de extremidade |
|--------|----------|
| Leste da Austrália | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Canadá Central | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| Leste dos EUA | `https://eastus.customvoice.api.speech.microsoft.com` |
| Centro da Índia | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Centro-Sul dos Estados Unidos | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Sudeste Asiático | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sul do Reino Unido | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa Ocidental | `https://westeurope.customvoice.api.speech.microsoft.com` |
| Oeste dos EUA 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formatos de saída de áudio

Damos suporte a formatos de saída de áudio flexíveis. Você pode gerar saídas de áudio por parágrafo ou concatenar as saídas de áudio em uma única saída definindo o parâmetro ' concatenateResult '. Os seguintes formatos de saída de áudio são suportados pela API de áudio longa:

> [!NOTE]
> O formato de áudio padrão é riff-16kHz-16 bits-mono-PCM.

* riff-8kHz-16 bits-mono-PCM
* riff-16kHz-16 bits-mono-PCM
* riff-24kHz-16 bits-mono-PCM
* riff-48kHz-16 bits-mono-PCM
* Audio-16kHz-32kbitrate-mono-mp3
* Audio-16kHz-64kbitrate-mono-mp3
* Audio-16kHz-128kbitrate-mono-mp3
* Audio-24kHz-48kbitrate-mono-mp3
* Audio-24kHz-96kbitrate-mono-mp3
* Audio-24kHz-160kbitrate-mono-mp3

## <a name="sample-code"></a>Código de exemplo
O código de exemplo para a API de áudio de longa disponibilidade está disponível no GitHub.

* [Código de exemplo: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código de exemplo: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código de exemplo: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
