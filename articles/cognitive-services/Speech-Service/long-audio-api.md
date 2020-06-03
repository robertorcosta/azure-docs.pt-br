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
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: 550579b40470d7a1ad02031b8140e7d0a7164f46
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310590"
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

## <a name="submit-synthesis-requests"></a>Enviar solicitações de síntese

Depois de preparar o conteúdo de entrada, siga o [início rápido de síntese de áudio de forma longa](https://aka.ms/long-audio-python) para enviar a solicitação. Se você tiver mais de um arquivo de entrada, será necessário enviar várias solicitações. 

Os **códigos de status http** indicam erros comuns.

| API | Código de status HTTP | Descrição | Proposta |
|-----|------------------|-------------|----------|
| Criar | 400 | A síntese de voz não está habilitada nesta região. | Altere a chave de assinatura de fala com uma região com suporte. |
|        | 400 | Somente a assinatura de fala **padrão** para essa região é válida. | Altere a chave de assinatura de fala para o tipo de preço "padrão". |
|        | 400 | Excede o limite de solicitação de 20.000 para a conta do Azure. Remova algumas solicitações antes de enviar novas. | O servidor manterá até 20.000 solicitações para cada conta do Azure. Exclua algumas solicitações antes de enviar novas. |
|        | 400 | Este modelo não pode ser usado na síntese de voz: {ModelId}. | Certifique-se de que o estado de {ModelId} esteja correto. |
|        | 400 | A região da solicitação não corresponde à região do modelo: {ModelId}. | Certifique-se de que a região da {ModelId} corresponda à região da solicitação. |
|        | 400 | A síntese de voz só dá suporte ao arquivo de texto na codificação UTF-8 com o marcador de ordem de byte. | Verifique se os arquivos de entrada estão em codificação UTF-8 com o marcador de ordem de byte. |
|        | 400 | Somente entradas SSML válidas são permitidas na solicitação de síntese de voz. | Verifique se as expressões SSML de entrada estão corretas. |
|        | 400 | O nome de voz {voicename} não foi encontrado no arquivo de entrada. | O nome da voz SSML de entrada não está alinhado com a ID do modelo. |
|        | 400 | A quantidade de parágrafo no arquivo de entrada deve ser menor que 10.000. | Verifique se o parágrafo no arquivo é menor que 10.000. |
|        | 400 | O arquivo de entrada deve ter mais de 400 caracteres. | Verifique se o arquivo de entrada excede 400 caracteres. |
|        | 404 | O modelo declarado na definição de síntese de voz não pode ser encontrado: {ModelId}. | Verifique se {ModelId} está correto. |
|        | 429 | Excede o limite de síntese de voz ativo. Aguarde até que algumas solicitações sejam concluídas. | O servidor tem permissão para executar e enfileirar até 120 solicitações para cada conta do Azure. Aguarde e evite enviar novas solicitações até que algumas solicitações sejam concluídas. |
| Tudo       | 429 | Há muitas solicitações. | O cliente tem permissão para enviar até 5 solicitações ao servidor por segundo para cada conta do Azure. Reduza o valor da solicitação por segundo. |
| Excluir    | 400 | A tarefa de síntese de voz ainda está em uso. | Você só pode excluir solicitações **concluídas** ou **com falha**. |
| GetByID   | 404 | A entidade especificada não pode ser encontrada. | Verifique se a ID de síntese está correta. |

## <a name="regions-and-endpoints"></a>Regiões e endpoints

A API de áudio longa está disponível em várias regiões com pontos de extremidade exclusivos.

| Região | Ponto de Extremidade |
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

## <a name="quickstarts"></a>Inícios rápidos

Oferecemos guias de início rápido projetados para ajudá-lo a executar a API de áudio longo com êxito. Esta tabela inclui uma lista de guias de início rápido da API de áudio longa organizados por idioma.

* [Início rápido: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Código de exemplo
O código de exemplo para a API de áudio de longa disponibilidade está disponível no GitHub.

* [Código de exemplo: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código de exemplo: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código de exemplo: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
