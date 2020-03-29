---
title: Preparar dados de teste para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Ao testar a precisão do reconhecimento de voz da Microsoft ou treinar seus modelos personalizados, você precisará de dados de áudio e texto. Nesta página, nós cobrimos os tipos de dados, como usá-los e gerenciá-los.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 969c1450966d2754e6e8f00126da52a1e88181fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942699"
---
# <a name="prepare-data-for-custom-speech"></a>Preparar dados para discurso personalizado

Ao testar a precisão do reconhecimento de voz da Microsoft ou treinar seus modelos personalizados, você precisará de dados de áudio e texto. Nesta página, nós cobrimos os tipos de dados, como usá-los e gerenciá-los.

## <a name="data-types"></a>Tipos de dados

Esta tabela lista os tipos de dados aceitos, quando cada tipo de dados deve ser usado e a quantidade recomendada. Nem todo tipo de dados é necessário para criar um modelo. Os requisitos de dados variam dependendo se você está criando um teste ou treinando um modelo.

| Tipo de dados | Usado para testes | Quantidade recomendada | Usado para treinamento | Quantidade recomendada |
|-----------|-----------------|----------|-------------------|----------|
| [Áudio](#audio-data-for-testing) | Sim<br>Usado para inspeção visual | 5+ arquivos de áudio | Não | N/A |
| [Áudio + Transcrições com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining) | Sim<br>Usado para avaliar a precisão | 0,5-5 horas de áudio | Sim | 1-1.000 horas de áudio |
| [Texto relacionado](#related-text-data-for-training) | Não | N/A | Sim | 1-200 MB de texto relacionado |

Os arquivos devem ser agrupados por tipo em um conjunto de dados e carregados como um arquivo .zip. Cada conjunto de dados só pode conter um único tipo de dados.

> [!TIP]
> Para começar rapidamente, considere usar dados de amostra. Consulte este repositório do GitHub para <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">obter dados de pesquisa de fala <span class="docon docon-navigate-external x-hidden-focus"></span> personalizada</a>

## <a name="upload-data"></a>Carregar dados

Para carregar seus dados, navegue até o <a href="https://speech.microsoft.com/customspeech" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"> </span>De fala personalizada </a>. No portal, clique **em Carregar dados** para iniciar o assistente e criar seu primeiro conjunto de dados. Você será solicitado a selecionar um tipo de dados de fala para o seu conjunto de dados, antes de permitir que você faça upload de seus dados.

![Selecione o áudio do Portal de Fala](./media/custom-speech/custom-speech-select-audio.png)

Cada conjunto de dados que você carrega deve atender aos requisitos para o tipo de dados escolhido. Seus dados devem ser formatados corretamente antes de serem carregados. Os dados formatados corretamente garantem que serão processados com precisão pelo serviço Custom Speech. Os requisitos estão listados nas seguintes seções.

Depois que seu conjunto de dados é carregado, você tem algumas opções:

* Você pode navegar até a guia **Teste** e inspecionar visualmente apenas áudio ou áudio + dados de transcrição com rótulo humano.
* Você pode navegar até a guia **Treinamento** e usar dados de áudio + transcrição humana ou dados de texto relacionados para treinar um modelo personalizado.

## <a name="audio-data-for-testing"></a>Dados de áudio para testes

Os dados de áudio são ideais para testar a precisão do modelo de fala-para-texto da Microsoft ou de um modelo personalizado. Tenha em mente que os dados de áudio são usados para inspecionar a precisão da fala em relação ao desempenho de um modelo específico. Se você está procurando quantificar a precisão de um modelo, use dados de [transcrição de áudio + rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining).

Use esta tabela para garantir que seus arquivos de áudio sejam formatados corretamente para uso com discurso personalizado:

| Propriedade                 | Valor                 |
|--------------------------|-----------------------|
| Formato de arquivo              | RIFF (WAV)            |
| Taxa de amostragem              | 8.000 Hz ou 16.000 Hz |
| Canais                 | 1 (mono)              |
| Comprimento máximo por áudio | 2 horas               |
| Formato de exemplo            | PCM, 16 bits           |
| Formato de arquivo           | .zip                  |
| Tamanho máximo de arquivo     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Ao carregar dados de treinamento e teste, o tamanho do arquivo .zip não pode exceder 2 GB. Se você precisar de mais dados para treinamento, divida-os em vários arquivos .zip e carregue-os separadamente. Mais tarde, você pode optar por treinar a partir de *vários* conjuntos de dados. No entanto, você só pode testar a partir de um *único* conjunto de dados.

Use <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">o <span class="docon docon-navigate-external x-hidden-focus"></span> SoX</a> para verificar propriedades de áudio ou converter áudio existente para os formatos apropriados. Abaixo estão alguns exemplos de como cada uma dessas atividades pode ser feita através da linha de comando SoX:

| Atividade | Descrição | Comando SoX |
|----------|-------------|-------------|
| Verifique o formato de áudio | Use este comando para verificar<br>o formato do arquivo de áudio. | `sox --i <filename>` |
| Converter formato de áudio | Use este comando para converter<br>o arquivo de áudio para um único canal, 16 bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Áudio + dados de transcrição com rótulo humano para testes/treinamento

Para medir a precisão da precisão de voz a texto da Microsoft ao processar seus arquivos de áudio, você deve fornecer transcrições rotuladas por humanos (palavra por palavra) para comparação. Embora a transcrição com rótulo humano seja muitas vezes demorada, é necessário avaliar a precisão e treinar o modelo para seus casos de uso. Tenha em mente que as melhorias no reconhecimento serão tão boas quanto os dados fornecidos. Por essa razão, é importante que apenas transcrições de alta qualidade sejam carregadas.

| Propriedade                 | Valor                               |
|--------------------------|-------------------------------------|
| Formato de arquivo              | RIFF (WAV)                          |
| Taxa de amostragem              | 8.000 Hz ou 16.000 Hz               |
| Canais                 | 1 (mono)                            |
| Comprimento máximo por áudio | 2 horas (teste) / 60 s (treinamento) |
| Formato de exemplo            | PCM, 16 bits                         |
| Formato de arquivo           | .zip                                |
| Tamanho máximo do zíper         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Ao carregar dados de treinamento e teste, o tamanho do arquivo .zip não pode exceder 2 GB. Você só pode testar a partir de um *único* conjunto de dados, certifique-se de mantê-lo dentro do tamanho de arquivo apropriado. Além disso, cada arquivo de treinamento não pode exceder 60 segundos, caso contrário, ele irá errar.

Para resolver questões como exclusão ou substituição de palavras, uma quantidade significativa de dados é necessária para melhorar o reconhecimento. Geralmente, recomenda-se fornecer transcrições palavra por palavra para cerca de 10 a 1.000 horas de áudio. As transcrições para todos os arquivos WAV devem estar contidas em um único arquivo de texto sem formatação. Cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguido pela transcrição correspondente. O nome do arquivo e transcrição devem ser separados por uma tabulação (\t).

  Por exemplo: 
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> A transcrição deve ser codificada como BOM (marca de ordem de byte) UTF-8.

As transcrições são normalizadas para texto para processamento pelo sistema. No entanto, existem algumas normalizações importantes que devem ser feitas antes de enviar os dados para o Speech Studio. Para que a linguagem apropriada use quando você preparar suas transcrições, consulte [Como criar uma transcrição rotulada por humanos](how-to-custom-speech-human-labeled-transcriptions.md)

Depois de reunir seus arquivos de áudio e transcrições correspondentes, empacote-os como um único arquivo .zip antes de fazer o upload para o <a href="https://speech.microsoft.com/customspeech" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"> </span>Custom Speech </a>. Abaixo está um conjunto de dados de exemplo com três arquivos de áudio e um arquivo de transcrição com rótulo humano:

> [!div class="mx-imgBorder"]
> ![Selecione o áudio do Portal de Fala](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dados de texto relacionados para treinamento

Nomes de produtos ou recursos exclusivos, devem incluir dados de texto relacionados para treinamento. Texto relacionado ajuda a garantir o reconhecimento correto. Dois tipos de dados de texto relacionados podem ser fornecidos para melhorar o reconhecimento:

| Tipo de dados | Como esses dados melhoram o reconhecimento |
|-----------|------------------------------------|
| Sentenças (enunciados) | Melhore a precisão ao reconhecer nomes de produtos ou vocabulário específico da indústria no contexto de uma frase. |
| Pronúncias | Melhore a pronúncia de termos incomuns, siglas ou outras palavras com pronúncias indefinidas. |

As sentenças podem ser fornecidas como um único arquivo de texto ou vários arquivos de texto. Para melhorar a precisão, use dados de texto mais próximos das declarações faladas esperadas. As pronúncias devem ser fornecidas como um único arquivo de texto. Tudo pode ser embalado como um único arquivo zip e carregado no <a href="https://speech.microsoft.com/customspeech" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"> </span>Custom Speech </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Diretrizes para criar um arquivo de sentenças

Para criar um modelo personalizado usando frases, você precisará fornecer uma lista de enunciados de exemplo. As declarações _não_ precisam ser completas ou gramaticalmente corretas, mas devem refletir com precisão a entrada falada que você espera na produção. Se você quiser que certos termos tenham aumento de peso, adicione várias frases que incluem esses termos específicos.

Como orientação geral, a adaptação do modelo é mais eficaz quando o texto de formação está o mais próximo possível do texto real esperado na produção. Jargões específicos de domínio e frases que você está mirando para melhorar, devem ser incluídos no texto de treinamento. Quando possível, tente ter uma frase ou palavra-chave controlada em uma linha separada. Para palavras-chave e frases que são importantes para você (por exemplo, nomes de produtos), você pode copiá-las algumas vezes. Mas tenha em mente, não copie muito - isso pode afetar a taxa de reconhecimento global.

Use esta tabela para garantir que seu arquivo de dados relacionado para declarações seja formatado corretamente:

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | BOM para UTF-8 |
| Nº de enunciados por linha | 1 |
| Tamanho máximo do arquivo | 200 MB |

Além disso, você vai querer explicar as seguintes restrições:

* Evite repetir caracteres mais de quatro vezes. Por exemplo: "aaaa" ou "uuuu".
* Não use caracteres especiais ou caracteres `U+00A1`UTF-8 acima .
* Uris serão rejeitados.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Diretrizes para criar um arquivo de pronúncia

Se houver termos incomuns sem pronúncias padrão que seus usuários encontrarão ou usarão, você pode fornecer um arquivo de pronúncia personalizado para melhorar o reconhecimento.

> [!IMPORTANT]
> Não é recomendável usar arquivos de pronúncia personalizados para alterar a pronúncia de palavras comuns.

Isso inclui exemplos de um pronunciamento falado e uma pronúncia personalizada para cada um:

| Forma reconhecida/exibida | Forma falada |
|--------------|--------------------------|
| 3CPO | três c p o |
| CNTK | c n t k |
| Ieee | i triplo e |

A forma falada é a seqüência fonética escrita. Pode ser composto de letras, palavras, sílabas ou uma combinação das três.

A pronúncia personalizada`en-US`está disponível`de-DE`em inglês ( ) e alemão ( ). Esta tabela mostra caracteres suportados por linguagem:

| Idioma | Local | Caracteres |
|----------|--------|------------|
| Inglês | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Alemão | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Use a tabela a seguir para garantir que seu arquivo de dados relacionado para pronúncias seja formatado corretamente. Os arquivos de pronúncia são pequenos, e devem ter apenas alguns kilobytes de tamanho.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM (ANSI também é suportado para inglês) |
| # de pronúncias por linha | 1 |
| Tamanho máximo do arquivo | 1 MB (1 KB para nível gratuito) |

## <a name="next-steps"></a>Próximas etapas

* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)
