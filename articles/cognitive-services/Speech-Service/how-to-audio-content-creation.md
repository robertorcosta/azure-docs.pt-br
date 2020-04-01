---
title: Criação de conteúdo em áudio - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Audio Content Creation é uma ferramenta on-line que permite personalizar e ajustar a saída de texto para voz da Microsoft para seus aplicativos e produtos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 8184d30471b4d9171a23c8d03e17b2a54dca2ece
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397831"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Melhorar a síntese com a criação de conteúdo de áudio

[Audio Content Creation](https://aka.ms/audiocontentcreation) é uma ferramenta on-line que permite personalizar e ajustar a saída de texto para voz da Microsoft para seus aplicativos e produtos. Você pode usar esta ferramenta para ajustar vozes públicas e personalizadas para expressões naturais mais precisas e gerenciar sua saída na nuvem.

A ferramenta Criação de conteúdo em áudio é baseada no [SSML (Speech Synthesis Markup Language, linguagem de marcação de síntese de fala).](speech-synthesis-markup.md) Para simplificar a personalização e a sintonia, a Criação de Conteúdo em Áudio permite que você inspecione visualmente suas saídas de texto para fala em tempo real.

## <a name="how-does-it-work"></a>Como ele funciona?

Este diagrama mostra as etapas necessárias para ajustar e exportar saídas personalizadas de fala para texto. Use os links abaixo para saber mais sobre cada etapa.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. O primeiro passo é [criar uma conta do Azure, registrar um recurso speech e obter uma chave de assinatura](#create-a-speech-resource). Depois de ter uma chave de assinatura, você pode usá-la para chamar o serviço Speech e acessar a [Criação de Conteúdo em Áudio](https://aka.ms/audiocontentcreation).
2. [Crie um arquivo de sintonia de áudio](#create-an-audio-tuning-file) usando texto simples ou SSML.
3. Escolha a voz e a linguagem que você gostaria de sintonizar. A Criação de Conteúdo em Áudio inclui todas as [vozes de texto para voz](language-support.md#text-to-speech)da Microsoft . Você pode usar o padrão, neural, ou sua própria voz personalizada.
   >[!NOTE]
   > O acesso fechado está disponível para vozes neurais personalizadas, que permitem criar vozes de alta definição semelhantes à fala natural. Para obter mais detalhes, consulte [o processo Gating](https://aka.ms/ignite2019/speech/ethics).

4. Revise o resultado padrão. Em seguida, use a ferramenta de ajuste para ajustar pronúncia, tom, taxa, entonação, estilo de voz e muito mais. Para obter uma lista completa de opções, consulte [Speech Synthesis Markup Language](speech-synthesis-markup.md).
5. Salve e [exporte seu áudio sintonizado](#export-tuned-audio). Quando você salva a faixa de sintonia no sistema, você pode continuar a trabalhar e iterar na saída. Quando você está satisfeito com a saída, você pode criar uma tarefa de criação de áudio com o recurso de exportação. Você pode observar o status da tarefa de exportação e baixar a saída para uso com seus aplicativos e produtos.
6. O último passo é usar a voz afinada personalizada em seus aplicativos e produtos.

## <a name="create-a-speech-resource"></a>Criar um recurso de fala

Siga essas etapas para criar um recurso Speech e conectá-lo com o Speech Studio.

1. Siga estas instruções [para se inscrever em uma conta do Azure](get-started.md#new-resource) e criar um recurso [Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource). Certifique-se de que seu nível de preços está definido como **S0**. Se você estiver usando uma das vozes neurais, certifique-se de criar seu recurso em uma [região suportada](regions.md#standard-and-neural-voices).
2. Faça login na [Criação de Conteúdo em Áudio](https://aka.ms/audiocontentcreation).
3. Selecione um projeto existente ou clique **em Criar novo**.
4. Você pode modificar sua assinatura a qualquer momento com a opção **Configurações,** localizada na navegação superior.

## <a name="create-an-audio-tuning-file"></a>Criar um arquivo de sintonia de áudio

Existem duas maneiras de colocar seu conteúdo na ferramenta Criação de conteúdo em áudio.

**Opção 1:**

1. Depois de entrar na [Criação de Conteúdo em Áudio,](https://aka.ms/audiocontentcreation)clique em Sintonia de **áudio** para criar um novo arquivo de sintonia de áudio.
2. Quando a janela de edição aparecer, você pode inserir até 10.000 caracteres.
3. Não se esqueça de salvar.

**Opção 2:**

1. Depois de entrar na [Criação de Conteúdo de Áudio,](https://aka.ms/audiocontentcreation)clique em **Carregar** para importar um ou mais arquivos de texto. Tanto o texto simples quanto o SSML são suportados.
2. Ao carregar seus arquivos de texto, certifique-se de que o conteúdo atenda a esses requisitos.

   | Propriedade | Valor / Notas |
   |----------|---------------|
   | Formato de arquivo | Texto sem formatação (.txt)<br/> Texto SSML (.txt)<br/> Arquivos zip não são suportados |
   | Formato de codificação | UTF-8 |
   | Nome do arquivo | Cada arquivo deve ter um nome único. Duplicatas não são suportadas. |
   | Comprimento do texto | Os arquivos de texto não devem exceder 10.000 caracteres. |
   | Restrições ao SSML | Cada arquivo SSML só pode conter um único pedaço de SSML. |

### <a name="plain-text-example"></a>Exemplo de texto simples

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Exemplo de texto SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportar áudio sintonizado

Depois de revisar sua saída de áudio e ficar satisfeito com sua sintonia e ajuste, você pode exportar o áudio.

1. Na ferramenta [Criação de conteúdo de áudio,](https://aka.ms/audiocontentcreation) clique em **Exportar** para criar uma tarefa de criação de áudio.
2. Escolha o formato de saída para o áudio afinado. Uma lista de formatos suportados e taxas de amostra estão disponíveis abaixo.
3. Você pode visualizar o status da tarefa na guia **Exportar tarefa.** Se a tarefa falhar, consulte a página de informações detalhadas para obter um relatório completo.
4. Quando a tarefa estiver concluída, seu áudio estiver disponível para download na guia **Biblioteca de Áudio.**
5. Clique em **Baixar**. Agora você está pronto para usar seu áudio personalizado sintonizado em seus aplicativos ou produtos.

### <a name="supported-audio-formats"></a>Formatos com suporte de áudio

| Formatar | Taxa de amostragem de 16 kHz | Taxa de amostragem de 24 kHz |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | áudio-16khz-128kbitrate-mono-mp3 | áudio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Confira também

* [API de áudio longo](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Estúdio de Fala](https://speech.microsoft.com)
