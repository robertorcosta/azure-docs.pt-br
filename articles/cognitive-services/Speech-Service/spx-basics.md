---
title: Noções básicas da CLI de Fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a ferramenta de comando da CLI de Fala para trabalhar com o Serviço de Fala sem código e com configuração mínima.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e859ac13c72ed07d3f57da6e61fd6d9f827f0fca
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88854893"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Conheça os fundamentos da CLI de Fala

Neste artigo, você aprende os padrões de uso básicos da CLI de Fala, uma ferramenta de linha de comando para usar o serviço de Fala sem escrever código. Você pode testar rapidamente os principais recursos do serviço de Fala, sem criar ambientes de desenvolvimento nem escrever qualquer código, para ver se os casos de uso podem ser adequadamente satisfeitos. Além disso, a CLI de Fala está pronta para produção e pode ser usada para automatizar fluxos de trabalho simples no serviço de Fala, usando scripts de shell ou `.bat`.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Uso básico

Esta seção mostra alguns comandos de SPX básicos que geralmente são úteis para teste e experimentação na primeira vez. Comece examinando a ajuda interna da ferramenta executando o comando a seguir.

```shell
spx
```

Observe os tópicos de ajuda do tipo **confira:** listados à direita dos parâmetros do comando. Você pode inserir esses comandos para obter ajuda detalhada sobre os subcomandos.

Você pode pesquisar os tópicos de ajuda por palavra-chave. Por exemplo, digite o seguinte comando para ver uma lista de exemplos de uso da CLI de Fala:

```shell
spx help find --topics "examples"
```

Insira o seguinte comando para ver opções do comando Recognize:

```shell
spx help recognize
```

Agora, use o serviço de Fala para executar um reconhecimento de fala usando o microfone padrão, executando o comando a seguir.

```shell
spx recognize --microphone
```

Depois de inserir o comando, o SPX começará a escutar áudio no dispositivo de entrada ativo atual e será interrompido depois que você pressionar `ENTER`. A fala gravada é reconhecida e convertida em texto na saída do console. A síntese de conversão de texto em fala também é fácil de fazer usando a CLI de Fala. 

A execução do comando a seguir usará o texto inserido como entrada e produzirá a fala sintetizada no dispositivo de saída ativo atual.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Além do reconhecimento e síntese de fala, você também pode fazer a tradução de fala com a CLI de Fala. Assim como o comando de reconhecimento de fala acima, execute o comando a seguir para capturar áudio do seu microfone padrão e execute a tradução para texto no idioma de destino.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

Neste comando, você especifica os idiomas de origem (idioma **do** qual traduzir) e destino (idioma **para** o qual traduzir). O uso do argumento `--microphone` escutará o áudio no dispositivo de entrada ativo atual e será interrompido depois que você pressionar `ENTER`. A saída é uma tradução de texto para o idioma de destino, gravada em um arquivo de texto.

> [!NOTE]
> Consulte o [artigo idioma e localidade](language-support.md) para obter uma lista de todos os idiomas compatíveis, com os respectivos códigos de localidade.

## <a name="batch-operations"></a>Operações em lote

Os comandos da seção anterior são ótimos para ver rapidamente como o serviço de Fala funciona. No entanto, ao avaliar se seus casos de uso podem ou não ser atendidos, você provavelmente precisará executar operações em lote em uma variedade de entradas que você já tem, para ver como o serviço lida com uma variedade de cenários. Esta seção mostra como:

* Executar o reconhecimento de fala em lote em um diretório de arquivos de áudio
* Iterar em um arquivo `.tsv` e executar a síntese de conversão de texto em fala em lote

## <a name="batch-speech-recognition"></a>Reconhecimento de fala em lote

Se você tiver um diretório de arquivos de áudio, será fácil fazer com que a CLI de Fala execute rapidamente o reconhecimento de fala em lote. Basta executar o comando a seguir, apontando para o diretório, com o comando `--files`. Neste exemplo, você acrescenta `\*.wav` ao diretório para reconhecer todos os arquivos `.wav` presentes no diretório. Além disso, especifique o argumento `--threads` para executar o reconhecimento em 10 threads paralelos.

> [!NOTE]
> O argumento `--threads` também pode ser usado na próxima seção para comandos `spx synthesize`, e os threads disponíveis dependerão da CPU e do respectivo percentual de carga atual.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

A saída da fala reconhecida é gravada no `speech_output.tsv` usando o argumento `--output file`. A seguir, um exemplo da estrutura do arquivo de saída.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>Síntese de conversão de texto em fala em lote

A maneira mais fácil de executar a conversão de texto em fala em lotes é criar um arquivo `.tsv` (valores separados por tabulação) e usar o comando `--foreach` na CLI de Fala. Considere o seguinte arquivo `text_synthesis.tsv`:

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Em seguida, execute um comando para apontar para `text_synthesis.tsv`, executar a síntese em cada campo `text` e gravar o resultado no caminho `audio.output` correspondente como um arquivo `.wav`. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Esse comando é o equivalente à execução de `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **para cada** registro no arquivo `.tsv`. Algumas coisas a serem observadas:

* Os cabeçalhos de coluna, `audio.output` e `text`, correspondem aos argumentos de linha de comando `--audio output` e `--text`, respectivamente. Argumentos de linha de comando de várias partes, como `--audio output`, devem ser formatados no arquivo sem espaços, sem traço à esquerda e com pontos separando cadeias de caracteres, por exemplo, `audio.output`. Qualquer outro argumento de linha de comando existente pode ser adicionado ao arquivo como colunas adicionais usando esse padrão.
* Quando o arquivo é formatado dessa forma, não há necessidade de passar nenhum argumento adicional para `--foreach`.
* Separe cada valor no `.tsv` com uma **tabulação**.

No entanto, se você tiver um arquivo `.tsv` como o exemplo a seguir, com cabeçalhos de coluna que **não correspondem** a argumentos de linha de comando:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

Você pode substituir esses nomes de campo pelos argumentos corretos usando a sintaxe a seguir na chamada `--foreach`. Esta é a mesma chamada descrita acima.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Próximas etapas

* Conclua os guias de início rápido de [reconhecimento de fala](./quickstarts/speech-to-text-from-microphone.md) ou [síntese de fala](./quickstarts/text-to-speech.md) usando o SDK.
