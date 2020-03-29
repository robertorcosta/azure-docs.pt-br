---
title: Como preparar dados para o serviço de voz personalizada - Fala
titleSuffix: Azure Cognitive Services
description: Crie uma voz personalizada para sua marca com o serviço Speech. Você fornece gravações de estúdio e os scripts associados, o serviço gera um modelo de voz único sintonizado com a voz gravada. Use essa voz para sintetizar a fala em seus produtos, ferramentas e aplicações.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805970"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Prepare dados para criar uma voz personalizada

Quando você estiver pronto para criar uma voz personalizada de texto para voz para o seu aplicativo, o primeiro passo é reunir gravações de áudio e scripts associados para começar a treinar o modelo de voz. O serviço Speech usa esses dados para criar uma voz única sintonizada para corresponder à voz nas gravações. Depois de treinar a voz, você pode começar a sintetizar a fala em suas aplicações.

Você pode começar com uma pequena quantidade de dados para criar uma prova de conceito. No entanto, quanto mais dados você fornecer, mais natural sua voz personalizada soará. Antes de treinar seu próprio modelo de voz texto-a-fala, você precisará de gravações de áudio e das transcrições de texto associadas. Nesta página, vamos revisar os tipos de dados, como eles são usados e como gerenciar cada um.

## <a name="data-types"></a>Tipos de dados

Um conjunto de dados de treinamento de voz inclui gravações de áudio e um arquivo de texto com as transcrições associadas. Cada arquivo de áudio deve conter uma única expressão (uma única frase ou uma única volta para um sistema de diálogo) e ter menos de 15 segundos de duração.

Em alguns casos, você pode não ter o conjunto de dados certo pronto e vai querer testar o treinamento de voz personalizado com arquivos de áudio disponíveis, curtos ou longos, com ou sem transcrições. Fornecemos ferramentas (beta) para ajudá-lo a segmentar seu áudio em enunciados e preparar transcrições usando a [API de transcrição em lote](batch-transcription.md).

Esta tabela lista os tipos de dados e como cada um é usado para criar um modelo de voz personalizado de texto para voz.

| Tipo de dados | Descrição | Quando usar | Serviço adicional necessário | Quantidade para treinar um modelo | Locale(s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Expressões individuais + transcrição correspondente** | Uma coleção (.zip) de arquivos de áudio (.wav) como enunciados individuais. Cada arquivo de áudio deve ter 15 segundos ou menos de comprimento, emparelhado com uma transcrição formatada (.txt). | Gravações profissionais com transcrições correspondentes | Pronto para o treinamento. | Não há exigência sustamo para en-US e zh-CN. Mais de 2.000 expressões distintas para outros locais. | [Todos os locais de voz personalizados](language-support.md#customization) |
| **Áudio longo + transcrição (beta)** | Uma coleção (.zip) de arquivos de áudio longos e não segmentados (mais de 20 segundos), emparelhado com uma transcrição (.txt) que contém todas as palavras faladas. | Você tem arquivos de áudio e transcrições correspondentes, mas eles não são segmentados em expressões. | Segmentação (utilização de transcrição em lote).<br>Transformação do formato de áudio quando necessário. | Sem exigência sustal  | [Todos os locais de voz personalizados](language-support.md#customization) |
| **Somente áudio (beta)** | Uma coleção (.zip) de arquivos de áudio sem uma transcrição. | Você só tem arquivos de áudio disponíveis, sem transcrições. | Segmentação + geração de transcrição (utilizando transcrição em lote).<br>Transformação do formato de áudio quando necessário.| Sem exigência sustal | [Todos os locais de voz personalizados](language-support.md#customization) |

Os arquivos devem ser agrupados por tipo em um conjunto de dados e carregados como um arquivo zip. Cada conjunto de dados só pode conter um único tipo de dados.

> [!NOTE]
> O número máximo de conjuntos de dados permitidos para serem importados por assinatura é de 10 arquivos .zip para usuários de assinatura gratuita (F0) e 500 para usuários de assinatura padrão (S0).

## <a name="individual-utterances--matching-transcript"></a>Expressões individuais + transcrição correspondente

Você pode preparar gravações de expressões individuais e a transcrição correspondente de duas maneiras. Escreva um script e o leia por um talento de voz ou use áudio disponível publicamente e transcreva-o em texto. No último caso, edite os erros de fluência dos arquivos de áudio, como "um" e outros sons de preenchimento, gagueira, palavras murmuradas ou pronunciamentos incorretos.

Para produzir uma boa fonte de voz, crie as gravações em uma sala tranquila com um microfone de alta qualidade. Volume consistente, taxa de fala, discurso e maneirismos expressivos da fala são essenciais.

> [!TIP]
> Para criar uma voz para uso de produção, é recomendável utilizar um estúdio de gravação profissional e um talento de voz. Para mais informações, consulte [Como gravar amostras de voz para uma voz personalizada](record-custom-voice-samples.md).

### <a name="audio-files"></a>Arquivos de áudio

Cada arquivo de áudio deve conter uma única expressão (uma única frase ou uma única volta de um sistema de diálogo), com menos de 15 segundos de duração. Todos os arquivos devem estar na mesma língua falada. Vozes personalizadas de texto para fala em vários idiomas não são suportadas, com exceção do bilíndrico chinês-inglês. Cada arquivo de áudio deve ter um nome de arquivo numérico exclusivo com a extensão filename .wav.

Siga essas orientações ao preparar o áudio.

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | RIFF (.wav), agrupado em um arquivo .zip |
| Taxa de amostragem | Pelo menos 16.000 Hz |
| Formato de exemplo | PCM, 16 bits |
| Nome do arquivo | Numérico, com extensão .wav. Não são permitidos nomes de arquivos duplicados. |
| Comprimento de áudio | Menor que 15 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 2.048 MB |

> [!NOTE]
> .wav arquivos com uma taxa de amostragem inferior a 16.000 Hz serão rejeitados. Se um arquivo .zip contiver arquivos .wav com diferentes taxas de amostragem, somente aqueles iguais ou superiores a 16.000 Hz serão importados. Atualmente, o portal importa arquivos .zip de até 200 MB. No entanto, vários arquivos podem ser enviados.

### <a name="transcripts"></a>Transcrições

O arquivo de transcrição é um arquivo de texto simples. Use essas diretrizes para preparar suas transcrições.

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | Texto sem formatação (.txt) |
| Formato de codificação | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE. Para codificações zh-CN, ANSI/ASCII e UTF-8 não são suportadas. |
| Nº de enunciados por linha | **Um** - Cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguido da transcrição correspondente. O nome do arquivo e transcrição devem ser separados por uma tabulação (\t). |
| Tamanho máximo do arquivo | 2.048 MB |

Abaixo está um exemplo de como as transcrições são organizadas por enunciado em um arquivo .txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
É importante que as transcrições sejam 100% precisas transcrições do áudio correspondente. Erros nas transcrições introduzirão perda de qualidade durante o treinamento.

> [!TIP]
> Ao construir vozes texto-para-voz de produção, selecione expressões (ou scripts de gravação) que levem em conta tanto a cobertura fonética quanto a eficiência. Tendo problemas para a obtenção dos resultados você deseja? [Entre em contato com a](mailto:speechsupport@microsoft.com) equipe de Voz Personalizada para saber mais sobre como nos consultar.

## <a name="long-audio--transcript-beta"></a>Áudio longo + transcrição (beta)

Em alguns casos, você pode não ter áudio segmentado disponível. Fornecemos um serviço (beta) através do portal de voz personalizado para ajudá-lo a segmentar arquivos de áudio longos e criar transcrições. Tenha em mente que este serviço será cobrado em relação ao seu uso de assinatura de fala para texto.

> [!NOTE]
> O serviço de segmentação de áudio longo aproveitará o recurso de transcrição em lote do fala-para-texto, que só suporta usuários de assinatura padrão (S0). Durante o processamento da segmentação, seus arquivos de áudio e as transcrições também serão enviados para o serviço De fala personalizada para refinar o modelo de reconhecimento para que a precisão possa ser melhorada para seus dados. Nenhum dado será retido durante este processo. Após a segmentação, apenas as expressões segmentadas e suas transcrições de mapeamento serão armazenadas para o seu download e treinamento.

### <a name="audio-files"></a>Arquivos de áudio

Siga essas orientações ao preparar áudio para segmentação.

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | RIFF (.wav) com uma taxa de amostragem de pelo menos 16 khz-16-bit em PCM ou .mp3 com uma taxa de bits de pelo menos 256 KBps, agrupado em um arquivo .zip |
| Nome do arquivo | Caracteres ASCII e Unicode suportados. Não são permitidos nomes duplicados. |
| Comprimento de áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 2.048 MB |

Todos os arquivos de áudio devem ser agrupados em um arquivo zip. Tudo bem colocar arquivos .wav e .mp3 em um zíper de áudio. Por exemplo, você pode carregar um arquivo zip contendo um arquivo de áudio chamado 'kingstory.wav', 45 segundos de comprimento e outro áudio chamado 'queenstory.mp3', 200 segundos de duração. Todos os arquivos .mp3 serão transformados no formato .wav após o processamento.

### <a name="transcripts"></a>Transcrições

As transcrições devem ser preparadas para as especificações listadas nesta tabela. Cada arquivo de áudio deve ser combinado com uma transcrição.

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | Texto simples (.txt), agrupado em um .zip |
| Nome do arquivo | Use o mesmo nome do arquivo de áudio correspondente |
| Formato de codificação | UTF-8-BOM apenas |
| Nº de enunciados por linha | Sem limite |
| Tamanho máximo do arquivo | 2.048 MB |

Todos os arquivos de transcrições neste tipo de dados devem ser agrupados em um arquivo zip. Por exemplo, você carregou um arquivo zip contendo um arquivo de áudio chamado 'kingstory.wav', 45 segundos de duração, e outro chamado 'queenstory.mp3', com 200 segundos de duração. Você precisará carregar outro arquivo zip contendo duas transcrições, uma chamada 'kingstory.txt', a outra 'queenstory.txt'. Dentro de cada arquivo de texto simples, você fornecerá a transcrição correta completa para o áudio correspondente.

Depois que seu conjunto de dados for carregado com sucesso, ajudaremos você a segmentar o arquivo de áudio em enunciados com base na transcrição fornecida. Você pode verificar as declarações segmentadas e as transcrições correspondentes baixando o conjunto de dados. IDs exclusivos serão atribuídos automaticamente às expressões segmentadas. É importante que você tenha certeza que as transcrições que você fornece são 100% precisas. Erros nas transcrições podem reduzir a precisão durante a segmentação de áudio e introduzir ainda mais perda de qualidade na fase de treinamento que vem depois.

## <a name="audio-only-beta"></a>Somente áudio (beta)

Se você não tiver transcrições para suas gravações de áudio, use a opção **Somente áudio** para carregar seus dados. Nosso sistema pode ajudá-lo a segmentar e transcrever seus arquivos de áudio. Tenha em mente que este serviço contará para o uso da assinatura de fala para texto.

Siga essas orientações ao preparar o áudio.

> [!NOTE]
> O serviço de segmentação de áudio longo aproveitará o recurso de transcrição em lote do fala-para-texto, que só suporta usuários de assinatura padrão (S0).

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | RIFF (.wav) com uma taxa de amostragem de pelo menos 16 khz-16-bit em PCM ou .mp3 com uma taxa de bits de pelo menos 256 KBps, agrupado em um arquivo .zip |
| Nome do arquivo | Caracteres ASCII e Unicode suportados. Nenhum nome duplicado é permitido. |
| Comprimento de áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 2.048 MB |

Todos os arquivos de áudio devem ser agrupados em um arquivo zip. Uma vez que seu conjunto de dados seja carregado com sucesso, nós o ajudaremos a segmentar o arquivo de áudio em enunciados com base em nosso serviço de transcrição em lote de fala. IDs exclusivos serão atribuídos automaticamente às expressões segmentadas. As transcrições correspondentes serão geradas através do reconhecimento de fala. Todos os arquivos .mp3 serão transformados no formato .wav após o processamento. Você pode verificar as declarações segmentadas e as transcrições correspondentes baixando o conjunto de dados.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma Voz Personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Grave suas amostras de voz](record-custom-voice-samples.md)
