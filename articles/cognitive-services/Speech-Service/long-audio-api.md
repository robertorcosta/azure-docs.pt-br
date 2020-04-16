---
title: API de áudio longo (Visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como a API de áudio longo foi projetada para a síntese assíncrona de texto de longa forma para a fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401067"
---
# <a name="long-audio-api-preview"></a>API de áudio longo (Visualização)

A API de áudio longo foi projetada para a síntese assíncrona de texto de longa forma para a fala (por exemplo: livros de áudio). Esta API não retorna áudio sintetizado em tempo real, em vez disso, a expectativa é que você pesquise as respostas e consuma as saídas à medida que elas são disponibilizadas a partir do serviço. Ao contrário da API de texto para fala que é usada pelo Speech SDK, a API de áudio longo pode criar áudio sintetizado por mais de 10 minutos, tornando-o ideal para editores e plataformas de conteúdo de áudio.

Benefícios adicionais da API de áudio longo:

* A fala sintetizada retornada pelo serviço usa vozes neurais, o que garante saídas de áudio de alta fidelidade.
* Como as respostas em tempo real não são suportadas, não há necessidade de implantar um ponto final de voz.

> [!NOTE]
> A API de áudio longo agora suporta apenas [voz neural personalizada.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)

## <a name="workflow"></a>Fluxo de trabalho

Normalmente, ao usar a API de áudio longo, você enviará um arquivo de texto ou arquivos para serem sintetizados, pesquise sobre o status, então se o status for bem-sucedido, você poderá baixar a saída de áudio.

Este diagrama fornece uma visão geral de alto nível do fluxo de trabalho.

![Diagrama de fluxo de trabalho de API de áudio longo](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparar conteúdo para a síntese

Ao preparar seu arquivo de texto, certifique-se de que ele:

* É texto simples (.txt) ou texto SSML (.txt)
* É codificado como [UTF-8 com Byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* É um único arquivo, não um zip
* Contém mais de 400 caracteres para texto simples ou 400 [caracteres faturados](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) para texto SSML, e menos de 10.000 parágrafos
  * Para texto simples, cada parágrafo é separado clicando **em Enter/Return** - Exibir [exemplo de entrada de texto simples](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Para texto SSML, cada peça de SSML é considerada um parágrafo. As peças ssml devem ser separadas por diferentes parágrafos - Ver [exemplo de entrada de texto SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Para chineses (continente), chineses (Hong Kong), chineses (Taiwan), japoneses e coreanos, uma palavra será contada como dois caracteres. 

## <a name="submit-synthesis-requests"></a>Enviar solicitações de síntese

Depois de preparar o conteúdo de entrada, siga a [síntese de áudio de forma longa para](https://aka.ms/long-audio-python) enviar a solicitação. Se você tiver mais de um arquivo de entrada, você precisará enviar várias solicitações. Existem algumas limitações a serem conscientizadas: 
* O cliente pode enviar até 5 solicitações ao servidor por segundo para cada conta de assinatura do Azure. Se exceder a limitação, o cliente receberá um código de erro de 429 (muitas solicitações). Por favor, reduza o valor da solicitação por segundo
* O servidor pode executar e fazer fila até 120 solicitações para cada conta de assinatura do Azure. Se exceder a limitação, o servidor retornará um código de erro de 429 (muitas solicitações). Por favor, aguarde e evite enviar novas solicitações até que algumas solicitações sejam concluídas
* O servidor manterá até 20.000 solicitações para cada conta de assinatura do Azure. Se exceder a limitação, exclua algumas solicitações antes de enviar novas

## <a name="audio-output-formats"></a>Formatos de saída de áudio

Suportamos formatos flexíveis de saída de áudio. Você pode gerar saídas de áudio por parágrafo ou concatenar os áudios em uma saída, definindo o parâmetro 'concatenadoResultado'. Os seguintes formatos de saída de áudio são suportados pela API de áudio longo:

> [!NOTE]
> O formato de áudio padrão é riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* áudio-16khz-32kbitrate-mono-mp3
* áudio-16khz-64kbitrate-mono-mp3
* áudio-16khz-128kbitrate-mono-mp3
* áudio-24khz-48kbitrate-mono-mp3
* áudio-24khz-96kbitrate-mono-mp3
* áudio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Inícios rápidos

Oferecemos partidas rápidas projetadas para ajudá-lo a executar a API de áudio longo com sucesso. Esta tabela inclui uma lista de api de áudio longo rapidamente iniciados organizados por linguagem.

* [Quickstart: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Código de exemplo
O código de exemplo para API de áudio longo está disponível no GitHub.

* [Código de exemplo: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código da amostra: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código de amostra: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
