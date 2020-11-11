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
ms.openlocfilehash: bead348e64fcee4cc5b790f975c9da5200ee796b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422392"
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

### <a name="configuration-files-in-the-datastore"></a>Arquivos de configuração no armazenamento de dados

O comportamento da CLI de Fala pode depender das configurações nos arquivos de configuração, que podem ser vistos nas chamadas da CLI de Fala por meio de um símbolo @.
A CLI de Fala salva uma nova configuração em um novo subdiretório `./spx/data` criado no diretório de trabalho atual.
Ao buscar um valor de configuração, a CLI de Fala procura esse valor no diretório de trabalho atual, no armazenamento de dados em `./spx/data` e em outros armazenamentos de dados, incluindo um armazenamento de dados final somente leitura no binário `spx`.
Anteriormente, você usou o armazenamento de dados para salvar seus valores `@key` e `@region`, portanto, você não precisou especificá-los com cada chamada de linha de comando.
Você também pode usar arquivos de configuração para armazenar as próprias definições de configuração ou até mesmo usá-las para passar URLs ou outro conteúdo dinâmico gerado em runtime.

Esta seção mostra o uso de um arquivo de configuração no armazenamento de dados local para armazenar e buscar configurações de comando usando `spx config` e armazenar a saída da CLI de Fala usando a opção `--output`.

O exemplo a seguir limpa o arquivo de configuração `@my.defaults`, adiciona pares chave-valor para **chave** e **região** no arquivo e usa a configuração em uma chamada para `spx recognize`.

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Você também pode gravar o conteúdo dinâmico em um arquivo de configuração. Por exemplo, o comando a seguir cria um modelo de fala personalizada e armazena a URL do novo modelo em um arquivo de configuração. O próximo comando aguarda até que o modelo na URL esteja pronto para ser usado antes de retornar.

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

O exemplo a seguir grava duas URLs no arquivo de configuração `@my.datasets.txt`.
Nesse cenário, `--output` pode incluir uma palavra-chave **adicionar** opcional para criar um arquivo de configuração ou acrescentar à existente.


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Para obter mais detalhes sobre arquivos do armazenamento de dados, incluindo o uso de arquivos de configuração padrão (`@spx.default`, `@default.config` e `@*.default.config` para configurações padrão específicas de comando), digite este comando:

```shell
spx help advanced setup
```

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

## <a name="synthesize-speech-to-a-file"></a>Sintetizar fala em um arquivo

Execute o comando a seguir para alterar a saída do seu alto-falante para um arquivo `.wav`.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

A CLI de Fala produzirá idioma natural em inglês no arquivo de áudio `greetings.wav`.
No Windows, você pode reproduzir o arquivo de áudio digitando `start greetings.wav`.


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

* Conclua os guias de início rápido de [reconhecimento de fala](get-started-speech-to-text.md?pivots=programmer-tool-spx) ou [sintetização de voz](get-started-text-to-speech.md?pivots=programmer-tool-spx) usando a CLI de Fala.
