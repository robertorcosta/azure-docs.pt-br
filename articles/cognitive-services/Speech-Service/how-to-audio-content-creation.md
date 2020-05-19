---
title: Criação de conteúdo de áudio-serviço de fala
titleSuffix: Azure Cognitive Services
description: A criação de conteúdo de áudio é uma ferramenta online que permite que você personalize e ajuste a saída de conversão de texto em fala da Microsoft para seus aplicativos e produtos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 083580435c467a7d4b6a4cede0a821a2c271962f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589645"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Melhorar a síntese com a ferramenta de criação de conteúdo de áudio

A [criação de conteúdo de áudio](https://aka.ms/audiocontentcreation) é uma ferramenta online que permite que você personalize e ajuste a saída de conversão de texto em fala da Microsoft para seus aplicativos e produtos. Você pode usar essa ferramenta para ajustar as vozes pública e personalizada para expressões naturais mais precisas e gerenciar sua saída na nuvem.

A ferramenta de criação de conteúdo de áudio baseia-se na [SSML (linguagem de marcação de síntese de fala)](speech-synthesis-markup.md). Para simplificar a personalização e o ajuste, a criação de conteúdo de áudio permite que você inspecione visualmente suas saídas de texto em fala em tempo real.

## <a name="how-does-it-work"></a>Como funciona?

Este diagrama mostra as etapas necessárias para ajustar as saídas de conversão de texto em fala. Use os links abaixo para saber mais sobre cada etapa.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [Configure sua conta do Azure e o recurso de fala](#set-up-your-azure-account-and-speech-resource) para começar.
2. [Crie um arquivo de ajuste de áudio](#create-an-audio-tuning-file) usando texto sem formatação ou scripts de SSML.
3. Escolha a voz e o idioma para o conteúdo do script. A criação de conteúdo de áudio inclui todas as [vozes de texto em fala da Microsoft](language-support.md#text-to-speech). Você pode usar o padrão, neural ou sua própria voz personalizada.
   >[!NOTE]
   > O acesso restrito está disponível para vozes neurais personalizadas, que permitem criar vozes de alta definição semelhantes à fala de som natural. Para obter mais detalhes, consulte o [processo de retenção](https://aka.ms/ignite2019/speech/ethics).

4. Examine a saída de síntese padrão. Em seguida, aumente a saída ajustando pronúncia, quebra, pitch, taxa, intonation, estilo de voz e muito mais. Para obter uma lista completa de opções, consulte [linguagem de marcação de síntese de fala](speech-synthesis-markup.md). Aqui está um [vídeo](https://youtu.be/mUvf2NbfuYU) para mostrar como ajustar a saída de fala com a criação de conteúdo de áudio. 
5. Salve e [exporte seu áudio ajustado](#export-tuned-audio). Ao salvar a faixa de ajuste no sistema, você pode continuar a trabalhar e iterar na saída. Quando estiver satisfeito com a saída, você poderá criar uma tarefa de criação de áudio com o recurso exportar. Você pode observar o status da tarefa de exportação e baixar a saída para uso com seus aplicativos e produtos.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Configurar sua conta do Azure e o recurso de fala

1. Para trabalhar com a criação de conteúdo de áudio, você deve ter uma conta do Azure. Você pode criar uma conta do Azure usando sua conta da Microsoft. Siga estas instruções para [Configurar uma conta do Azure](get-started.md#new-resource). 
2. [Crie um recurso de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) para sua conta do Azure. Verifique se o tipo de preço está definido como **S0**. Se você estiver usando uma das vozes neurais, certifique-se de criar seu recurso em uma [região com suporte](regions.md#standard-and-neural-voices).
2. Depois de obter a conta do Azure e o recurso de fala, você pode usar os serviços de fala e acessar a [criação de conteúdo de áudio](https://aka.ms/audiocontentcreation).
3. Selecione o recurso de fala no qual você precisa trabalhar. Você também pode criar um novo recurso de fala aqui. 
4. Você pode modificar o recurso de fala a qualquer momento com a opção **configurações** , localizada na barra de navegação superior.

## <a name="create-an-audio-tuning-file"></a>Criar um arquivo de ajuste de áudio

Há duas maneiras de colocar seu conteúdo na ferramenta de criação de conteúdo de áudio.

**Opção 1:**

1. Clique em **novo arquivo** para criar um novo arquivo de ajuste de áudio.
2. Digite ou cole o conteúdo na janela de edição. Os caracteres para cada arquivo são de até 20.000. Se o seu script tiver mais de 20.000 caracteres, você poderá usar a opção 2 para dividir automaticamente o conteúdo em vários arquivos. 
3. Não se esqueça de salvar.

**Opção 2:**

1. Clique em **carregar** para importar um ou mais arquivos de texto. Há suporte para texto sem formatação e SSML.
2. Se o arquivo de script tiver mais de 20.000 caracteres, divida o arquivo por parágrafos, por caractere ou por expressões regulares. 
3. Ao carregar seus arquivos de texto, certifique-se de que o arquivo atende a esses requisitos.

   | Propriedade | Valor/observações |
   |----------|---------------|
   | Formato de arquivo | Texto sem formatação (.txt)<br/> Texto de SSML (. txt)<br/> Não há suporte para arquivos zip |
   | Formato de codificação | UTF-8 |
   | Nome do Arquivo | Cada arquivo deve ter um nome exclusivo. Não há suporte para duplicatas. |
   | Comprimento do texto | Os arquivos de texto não devem exceder 20.000 caracteres. |
   | Restrições de SSML | Cada arquivo SSML pode conter apenas uma única parte de SSML. |

### <a name="plain-text-example"></a>Exemplo de texto sem formatação

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Exemplo de texto de SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportar áudio ajustado

Depois de revisar sua saída de áudio e ficar satisfeito com seu ajuste e ajuste, você poderá exportar o áudio.

1. Clique em **Exportar** para criar uma tarefa de criação de áudio. É recomendável **exportar para a biblioteca de áudio** , pois ela dá suporte à saída de áudio longa e a experiência de saída de áudio completa. Você também pode baixar o áudio para o disco local diretamente, mas somente os primeiros 10 minutos estão disponíveis. 
2. Escolha o formato de saída para o áudio ajustado. Uma lista de formatos com suporte e taxas de exemplo está disponível abaixo.
3. Você pode exibir o status da tarefa na guia **exportar tarefa** . Se a tarefa falhar, consulte a página informações detalhadas para obter um relatório completo.
4. Quando a tarefa for concluída, o áudio estará disponível para download na guia **biblioteca de áudio** .
5. Clique em **Download**. Agora você está pronto para usar seu áudio ajustado personalizado em seus aplicativos ou produtos.

### <a name="supported-audio-formats"></a>Formatos com suporte de áudio

| Formatar | taxa de amostragem de 16 kHz | taxa de amostragem de 24 kHz |
|--------|--------------------|--------------------|
| WAV | riff-16kHz-16 bits-mono-PCM | riff-24kHz-16 bits-mono-PCM |
| mp3 | Audio-16kHz-128kbitrate-mono-mp3 | Audio-24kHz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Consulte também

* [API de áudio longo](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Estúdio de Fala](https://speech.microsoft.com)
