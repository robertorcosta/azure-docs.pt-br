---
title: Formatos de dados suportados pelo Azure Data Explorer para ingestão.
description: Conheça os vários formatos de dados e compactações suportados pelo Azure Data Explorer para ingestão.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235294"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Formatos de dados suportados pelo Azure Data Explorer para ingestão

A ingestão de dados é o processo pelo qual os dados são adicionados a uma tabela e são disponibilizados para consulta no Azure Data Explorer. Para todos os métodos de ingestão, além de ingerir-a-consulta, os dados devem estar em um dos formatos suportados. A tabela a seguir lista e descreve os formatos que o Azure Data Explorer suporta para ingestão de dados.

|Formatar   |Extensão   |Descrição|
|---------|------------|-----------|
|Avro     |`.avro`     |Um [arquivo de contêiner Avro](https://avro.apache.org/docs/current/). Os códigos a seguir são compatíveis com: `null`, `deflate` (`snappy` não é compatível no momento).|
|CSV      |`.csv`      |Um arquivo de texto com valores separados por vírgulas (`,`). Consulte [RFC 4180: _Formato comum e tipo mime para arquivos separados por comuma (CSV)._](https://www.ietf.org/rfc/rfc4180.txt)|
|JSON     |`.json`     |Um arquivo de texto com objetos JSON delimitados por `\n` ou `\r\n`. Confira [JSONL (Linhas JSON)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|Um arquivo de texto com uma matriz JSON de recipientes de propriedades (cada um representando um registro) ou qualquer número de recipientes de propriedades delimitados por espaço em branco, `\n` ou `\r\n`. Cada recipiente de propriedades pode ser distribuído em várias linhas. Este formato é `JSON`preferido em vez de , a menos que os dados sejam sacos não-propriedade.|
|ORC      |`.orc`      |Um [arquivo Orc](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parquet  |`.parquet`  |Um [arquivo Parquet](https://en.wikipedia.org/wiki/Apache_Parquet).|
|Psv      |`.psv`      |Um arquivo de texto com valores separados por pipe (<code>&#124;</code>).|
|RAW      |`.raw`      |Um arquivo de texto cujo conteúdo inteiro é um valor de cadeia de caracteres único.|
|SCsv     |`.scsv`     |Um arquivo de texto com valores separados por ponto e vírgula (`;`).|
|SOHsv    |`.sohsv`    |Um arquivo de texto com valores separados por SOH. (SOH é o ponto de código ASCII 1; esse formato é usado pelo Hive no HDInsight.)|
|Tsv      |`.tsv`      |Um arquivo de texto com valores separados por tabulações (`\t`).|
|TSVE     |`.tsv`      |Um arquivo de texto com valores separados por tabulações (`\t`). Um caractere de barra invertida (`\`) é usado para escapar.|
|TXT      |`.txt`      |Um arquivo de texto com linhas delimitadas por `\n`. Linhas vazias são ignoradas.|

## <a name="supported-data-compression-formats"></a>Formatos de compactação de dados suportados

Blobs e arquivos podem ser compactados através de qualquer um dos seguintes algoritmos de compressão:

|Compactação|Extensão|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Indique a compactação anexando a extensão ao nome da bolha ou arquivo.

Por exemplo: 
* `MyData.csv.zip`indica uma bolha ou um arquivo formatado como CSV, compactado com ZIP (arquivo ou um único arquivo)
* `MyData.csv.gz`indica uma bolha ou um arquivo formatado como CSV, compactado com GZip

Blob ou nomes de arquivo que não incluem as extensões de formato, mas apenas compressão (por exemplo, ) também são suportados. Nesse caso, o formato de arquivo precisa ser especificado como uma propriedade de ingestão porque não pode ser inferido.

> [!NOTE]
> Alguns formatos de compactação controlam a extensão do arquivo original como parte do fluxo compactado. Essa extensão é geralmente ignorada para determinar o formato do arquivo. Se o formato do arquivo não puder ser determinado com base no nome do arquivo ou blob (compactado), ele deverá ser especificado por meio da propriedade de ingestão `format`.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a ingestão de dados](/azure/data-explorer/ingest-data-overview)
* Saiba mais sobre [as propriedades de ingestão de dados do Azure Data Explorer](ingestion-properties.md)
