---
title: Formatos de arquivo com suporte no Azure Data Factory| Microsoft Docs
description: Este tópico descreve os formatos de arquivo e os códigos de compactação com suporte nos conectores baseados em arquivo no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 00d8fb69abb6ce74a36ff017f3f356cb86114d99
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930924"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Formatos de arquivo e codecs de compactação com suporte no Azure Data Factory

*Este artigo se aplica aos seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Armazenamento](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).*

Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. Se você quiser **analisar ou gerar arquivos com um formato específico**, o Azure Data Factory dará suporte aos seguintes tipos de formato de arquivo:

* [Formato de texto](#text-format)
* [Formato JSON](#json-format)
* [Formato Parquet](#parquet-format)
* [Formato ORC](#orc-format)
* [Formato Avro](#avro-format)
* [Formato binário](#binary-format)

> [!TIP]
> Saiba como a atividade de cópia mapeia os dados de origem para o coletor do [mapeamento de esquema na atividade de cópia](copy-activity-schema-and-type-mapping.md).

## <a name="text-format"></a>Formato de texto

>[!NOTE]
>Data Factory introduziu um novo conjunto de informações de formato de texto delimitado, consulte o artigo [formato de texto delimitado](format-delimited-text.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se você quiser ler um arquivo de texto ou gravar em um arquivo de texto, defina a propriedade `type` na seção `format` do conjunto de dados para **TextFormat**. Você também pode especificar as seguintes propriedades **opcionais** na seção `format`. Veja a seção [Exemplo de TextFormat](#textformat-example) sobre a configuração.

| Propriedade | Descrição | Valores permitidos | obrigatórios |
| --- | --- | --- | --- |
| columnDelimiter |O caractere usado para separar as colunas em um arquivo. Você pode considerar o uso de um caractere não imprimível raro que não exista em seus dados. Por exemplo, especifique "\u0001", que representa o SOH (início do título). |Somente um caractere é permitido. O valor **padrão** é **vírgula (‘,’)** . <br/><br/>Para usar um caractere Unicode, consulte [Caracteres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obter o código correspondente. |Não |
| rowDelimiter |O caractere usado para separar as linhas em um arquivo. |Somente um caractere é permitido. O valor **padrão** é um dos seguintes valores na leitura: **["\r\n", "\r" e "\n"]** e **"\r\n"** na gravação. |Não |
| escapeChar |O caractere especial usado como escape do delimitador de coluna no conteúdo do arquivo de entrada. <br/><br/>Não é possível especificar ambos escapeChar e quoteChar para uma tabela. |Somente um caractere é permitido. Nenhum valor padrão. <br/><br/>Por exemplo, se tiver a vírgula (,) como o delimitador de coluna, mas quiser ter o caractere de vírgula no texto (exemplo: "Hello, world"), você poderá definir '$' como o caractere de escape e usar a cadeia de caracteres "Hello$, world" na fonte. |Não |
| quoteChar |O caractere usado para citar um valor de cadeia de caracteres. Os delimitadores de linha e coluna dentro dos caracteres de aspas seriam tratados como parte do valor de cadeia de caracteres. Essa propriedade é aplicável a ambos os conjuntos de dados de entrada e de saída.<br/><br/>Não é possível especificar ambos escapeChar e quoteChar para uma tabela. |Somente um caractere é permitido. Nenhum valor padrão. <br/><br/>Por exemplo, se tiver a vírgula (,) como o delimitador de coluna, mas quiser ter o caractere de vírgula no texto (exemplo: <Hello, world>), você poderá definir " (aspas duplas) como o caractere de citação e usar a cadeia de caracteres "Hello, world" na origem. |Não |
| nullValue |Um ou mais caracteres usados para representar um valor nulo. |Um ou mais caracteres. Os valores **padrão** são **"\N" e "NULL"** na leitura e **"\N"** na gravação. |Não |
| encodingName |Especifique o nome de codificação. |Um nomes de codificação válido. Consulte [Propriedade Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por exemplo: windows-1250 ou shift_jis. O valor **padrão** é **UTF-8**. |Não |
| firstRowAsHeader |Especifica se a primeira linha será considerada como cabeçalho. Para um conjunto de dados de entrada, o Data Factory lê a primeira linha como cabeçalho. Para um conjunto de dados de saída, o Data Factory lê a primeira linha como cabeçalho. <br/><br/>Veja [Cenários para usar o `firstRowAsHeader` e o `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. |True<br/><b>False (padrão)</b> |Não |
| skipLineCount |Indica o número de linhas **não vazias** a serem ignoradas ao ler dados de arquivos de entrada. Se skipLineCount e firstRowAsHeader forem especificados, as linhas serão ignoradas pela primeira vez e, em seguida, as informações de cabeçalho serão lidas do arquivo de entrada. <br/><br/>Veja [Cenários para usar o `firstRowAsHeader` e o `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. |Número inteiro |Não |
| treatEmptyAsNull |Especifica se uma cadeia de caracteres nula ou vazia será ou não tratada como um valor nulo ao ler dados de um arquivo de entrada. |**True (padrão)**<br/>False |Não |

### <a name="textformat-example"></a>Exemplo de TextFormat

Na definição de JSON a seguir para um conjunto de dados, algumas das propriedades opcionais são especificadas.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Para usar um `escapeChar` em vez de `quoteChar`, substitua a linha com `quoteChar` por este escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Cenários de uso de firstRowAsHeader e skipLineCount

* Você está copiando de uma fonte que não é de arquivo para um arquivo de texto e deseja adicionar uma linha de cabeçalho que contém os metadados de esquema (por exemplo: esquema SQL). Especifique `firstRowAsHeader` como true no conjunto de dados de saída para esse cenário.
* Você está copiando de um arquivo de texto contendo uma linha de cabeçalho para um coletor que não é em arquivo e gostaria de remover essa linha. Especifique `firstRowAsHeader` como true no conjunto de dados de entrada.
* Você está copiando de um arquivo de texto e deseja ignorar algumas linhas no início que não são informações de dados nem de cabeçalho. Especifique `skipLineCount` para indicar o número de linhas a serem ignoradas. Se o restante do arquivo contiver uma linha de cabeçalho, você também poderá especificar `firstRowAsHeader`. Se `skipLineCount` e `firstRowAsHeader` forem especificados, as linhas serão ignoradas pela primeira vez e, em seguida, as informações de cabeçalho serão lidas do arquivo de entrada

## <a name="json-format"></a>Formato JSON

>[!NOTE]
>Data Factory introduziu o novo conjunto de informações de formato JSON, consulte o artigo [JSON](format-json.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Para **importar/exportar um arquivo JSON no estado em que se encontra de/para o Azure Cosmos DB**, consulte a seção Importar/exportar documentos JSON do artigo [Move data to/from Azure Cosmos DB](connector-azure-cosmos-db.md) (Mover dados de/para o Azure Cosmos DB).

Se você quiser analisar os arquivos de JSON ou gravar os dados no formato JSON, defina a propriedade `type` na seção `format` como **JsonFormat**. Você também pode especificar as seguintes propriedades **opcionais** na seção `format`. Veja a seção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar.

| Propriedade | Descrição | obrigatórios |
| --- | --- | --- |
| filePattern |Indique o padrão de dados armazenados em cada arquivo JSON. Os valores permitidos são: **setOfObjects** e **arrayOfObjects**. O valor **padrão** é **setOfObjects**. Veja a seção [Padrões de arquivo JSON](#json-file-patterns) para obter detalhes sobre esses padrões. |Não |
| jsonNodeReference | Se você quiser fazer uma iteração e extrair dados de objetos dentro de um campo de matriz com o mesmo padrão, especifique o caminho JSON da matriz. Esta propriedade só terá suporte na cópia de dados **de** arquivos JSON. | Não |
| jsonPathDefinition | Especifique a expressão de caminho JSON para cada mapeamento de coluna com um nome de coluna personalizado (iniciar com letra minúscula). Esta propriedade só terá suporte na cópia de dados **de** arquivos JSON, e você pode extrair dados de objeto ou de matriz. <br/><br/> Para os campos sob o objeto root, comece com root $; para os campos dentro da matriz escolhidos pela propriedade `jsonNodeReference`, comece do elemento de matriz. Veja a seção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar. | Não |
| encodingName |Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, consulte: Propriedade [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) . Por exemplo: windows-1250 ou shift_jis. O valor **padrão** é **UTF-8**. |Não |
| nestingSeparator |Caractere que é usado para separar os níveis de aninhamento. O valor padrão é '.' (ponto). |Não |

>[!NOTE]
>Para o caso de aplicação cruzada de dados na matriz em várias linhas (caso 1-> exemplo 2 em [exemplos de JsonFormat](#jsonformat-example)), você pode optar por expandir apenas uma matriz usando a propriedade `jsonNodeReference`.

### <a name="json-file-patterns"></a>Padrões de arquivo JSON

A atividade de cópia pode analisar os padrões de arquivos JSON a seguir:

- **Tipo I: setOfObjects**

    Cada arquivo contém um único objeto ou vários objetos concatenados/delimitados por linhas. Quando essa opção é escolhida em um conjunto de dados de saída, a atividade de cópia produz um único arquivo JSON com cada objeto por linha (delimitados por linha).

    * **Exemplo de JSON de objeto único**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exemplo de JSON delimitado por linha**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exemplo de JSON concatenado**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Tipo II: arrayOfObjects**

    Cada arquivo contém uma matriz de objetos.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>Exemplo de JsonFormat

**Caso 1: copiar dados de arquivos JSON**

**Exemplo 1: extrair dados de objeto e de matriz**

Neste exemplo, você espera que um objeto JSON de raiz seja mapeado para um único registro no resultado tabular. Se você tiver um arquivo JSON com o seguinte conteúdo:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

e quiser copiá-lo para uma tabela SQL do Azure no formato a seguir, ao extrair dados dos objetos e da matriz:

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | Computador | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 13/01/2017 11:24:37 |

O conjunto de dados de entrada com o tipo **JsonFormat** é definido da seguinte maneira: (definição parcial com apenas as partes relevantes). Mais especificamente:

- A seção `structure` define os nomes de coluna personalizada e o tipo de dados correspondente ao converter em dados tabulares. Esta seção é **opcional**, a menos que você tenha de fazer o mapeamento de colunas. Para obter mais informações, consulte [Mapear colunas de conjunto de dados de origem para colunas de conjunto de dados de destino](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Para copiar dados de matriz, você pode usar `array[x].property` para extrair o valor da determinada propriedade do objeto `xth` ou usar `array[*].property` para localizar o valor de qualquer objeto que contenha essa propriedade.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Exemplo 2: cruzar aplicar vários objetos com o mesmo padrão da matriz**

Neste exemplo, você espera transformar um objeto JSON de raiz em vários registros no resultado tabular. Se você tiver um arquivo JSON com o seguinte conteúdo:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

e você deseja copiá-lo para uma tabela do Azure SQL no formato a seguir, ao nivelar os dados de dentro da matriz e fazer uma união cruzada com as informações comuns de root:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


O conjunto de dados de entrada com o tipo **JsonFormat** é definido da seguinte maneira: (definição parcial com apenas as partes relevantes). Mais especificamente:

- A seção `structure` define os nomes de coluna personalizada e o tipo de dados correspondente ao converter em dados tabulares. Esta seção é **opcional**, a menos que você tenha de fazer o mapeamento de colunas. Para obter mais informações, consulte [Mapear colunas de conjunto de dados de origem para colunas de conjunto de dados de destino](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` indica iterar e extrair dados dos objetos com o mesmo padrão da **matriz** `orderlines`.
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Neste exemplo, `ordernumber`, `orderdate` e `city` estão sob o objeto raiz com o caminho do JSON começando com `$.`, enquanto `order_pd` e `order_price` estão definidos com um caminho derivado do elemento da matriz sem `$.`.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Observe os seguintes pontos:**

* Se `structure` e `jsonPathDefinition` não forem definidos no conjunto de dados do Data Factory, a atividade de cópia detectará o esquema do primeiro objeto e mesclará todo o objeto.
* Se a entrada JSON tiver uma matriz por padrão, a atividade de cópia converterá o valor da matriz inteira em uma cadeia de caracteres. Você pode optar por extrair dados dele usando o `jsonNodeReference` e/ou o `jsonPathDefinition` ou ignorá-lo ao não especificá-lo no `jsonPathDefinition`.
* Se houver nomes duplicados no mesmo nível, a atividade de cópia selecionará o último entre eles.
* Os nomes de propriedade diferenciam maiúsculas de minúsculas. Duas propriedades com o mesmo nome, mas com maiúsculas e minúsculas diferentes são tratadas como duas propriedades separadas.

**Caso 2: gravação de dados no arquivo JSON**

Se você tiver a tabela a seguir no Banco de Dados SQL:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | Davi |
| 2 | 20170120 | 3500 | Pedro |
| 3 | 20170121 | 4000 | Jason |

e, para cada registro, você espera gravar em um objeto JSON neste formato:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

O conjunto de dados de saída com o tipo **JsonFormat** é definido da seguinte maneira: (definição parcial com apenas as partes relevantes). Mais especificamente, a seção `structure` define os nomes de propriedade personalizada no arquivo de destino, `nestingSeparator` (o padrão é ".") que são usados para identificar a camada de aninhamento do nome. Esta seção é **opcional**, a menos que você queira alterar o nome da propriedade em comparação ao nome da coluna de origem ou aninhar algumas das propriedades.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format"></a>Formato Parquet

>[!NOTE]
>Data Factory introduziu o novo conjunto de informações de formato parquet, consulte o artigo [formato parquet](format-parquet.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se você quiser analisar os arquivos Parquet ou gravar os dados no formato Parquet, defina a propriedade `format` `type` como **ParquetFormat**. Não será necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Observe os seguintes pontos:

* Não há suporte para tipos de dados complexos (MAP, LIST).
* Não há suporte para o espaço em branco no nome da coluna.
* O arquivo Parquet tem as seguintes opções relacionadas à compactação: NONE, SNAPPY, GZIP e LZO. O Data Factory dá suporte à leitura de dados do arquivo Parquet em qualquer um desses formatos compactados, exceto LZO: ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao gravar um arquivo Parquet, o Data Factory escolhe SNAPPY, que é o padrão para o formato Parquet. Não há nenhuma opção para substituir esse comportamento neste momento.

> [!IMPORTANT]
> Para cópias autorizadas pelo Integration Runtime (auto-hospedado), por exemplo, entre repositórios de dados locais e na nuvem, se você não estiver copiando arquivos Parquet **como são**, precisará instalar o **JRE (Java Runtime Environment) 8 de 64 bits ou o OpenJDK** no IR de seu computador. Confira o próximo parágrafo para obter mais detalhes.

Para cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo Parquet, o ADF localiza o tempo de execução do Java verificando o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, em primeiro lugar e, se não encontrado, realiza, em segundo lugar, a verificação na variável do sistema *`JAVA_HOME`* para o OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar o OpenJDK**: ele tem suporte desde a versão do IR 3.13. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.

>[!TIP]
>Se você copiar os dados para/do formato Parquet usando o IR auto-hospedado e ocorrências de erro informando que "Ocorreu um erro ao chamar o java, mensagem: **espaço de heap java.lang.OutOfMemoryError:Java**", poderá adicionar uma variável de ambiente `_JAVA_OPTIONS` na máquina que hospeda o IR auto-hospedado para ajustar o tamanho mín/máx do heap para JVM para capacitar a cópia e executar novamente o pipeline.

![Definir o tamanho do heap da JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: defina a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para uma JVM (Máquina Virtual Java), enquanto `Xmx` especifica o pool de alocação de memória máxima. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa um mínimo de 64MB e um máximo de 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Mapeamento de tipo de dados para arquivos Parquet

| Tipo de dados provisório do Data Factory | Tipo Primitivo Parquet | Tipo Original Parquet (Desserializar) | Tipo Original Parquet (Serializar) |
|:--- |:--- |:--- |:--- |
| Booliano | Booliano | N/D | N/D |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/Binário | UInt64 | Decimal |
| Individual | Float | N/D | N/D |
| DOUBLE | DOUBLE | N/D | N/D |
| Decimal | Binário | Decimal | Decimal |
| string | Binário | Utf8 | Utf8 |
| DateTime | Int96 | N/D | N/D |
| timespan | Int96 | N/D | N/D |
| DateTimeOffset | Int96 | N/D | N/D |
| ByteArray | Binário | N/D | N/D |
| GUID | Binário | Utf8 | Utf8 |
| Char | Binário | Utf8 | Utf8 |
| CharArray | Sem suporte | N/D | N/D |

## <a name="orc-format"></a>Formato ORC

>[!NOTE]
>Data Factory introduziu o novo conjunto de informações de formato ORC, consulte o artigo [formato Orc](format-orc.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se você quiser analisar os arquivos ORC ou gravar os dados no formato ORC, defina a propriedade `format` `type` como **OrcFormat**. Não será necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

```json
"format":
{
    "type": "OrcFormat"
}
```

Observe os seguintes pontos:

* Não há suporte para tipos de dados complexos (STRUCT, MAP, LIST e UNION).
* Não há suporte para o espaço em branco no nome da coluna.
* O arquivo ORC tem três [opções de compactação](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB e SNAPPY. O Data Factory dá suporte à leitura de dados de arquivo ORC em qualquer um dos formatos compactados acima. Ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao gravar um arquivo ORC, o Data Factory escolhe ZLIB, que é o padrão para ORC. Não há nenhuma opção para substituir esse comportamento neste momento.

> [!IMPORTANT]
> Para cópias autorizadas pelo Integration Runtime (auto-hospedado), por exemplo, entre repositórios de dados locais e na nuvem, se você não estiver copiando arquivos ORC **como são**, precisará instalar o **JRE (Java Runtime Environment) 8 de 64 bits ou o OpenJDK** no IR de seu computador. Confira o próximo parágrafo para obter mais detalhes.

Para cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo ORC, o ADF localiza o tempo de execução do Java verificando o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, em primeiro lugar e, se não encontrado, realiza, em segundo lugar, a verificação na variável do sistema *`JAVA_HOME`* para o OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar o OpenJDK**: ele tem suporte desde a versão do IR 3.13. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.

### <a name="data-type-mapping-for-orc-files"></a>Mapeamento de tipo de dados para arquivos ORC

| Tipo de dados provisório do Data Factory | Tipos ORC |
|:--- |:--- |
| Booliano | Booliano |
| SByte | Byte |
| Byte | Curto |
| Int16 | Curto |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Longo |
| Int64 | Longo |
| UInt64 | string |
| Individual | Float |
| DOUBLE | DOUBLE |
| Decimal | Decimal |
| string | string |
| DateTime | Timestamp |
| DateTimeOffset | Timestamp |
| timespan | Timestamp |
| ByteArray | Binário |
| GUID | string |
| Char | Char(1) |

## <a name="avro-format"></a>Formato AVRO

>[!NOTE]
>Data Factory introduziu o novo conjunto de informações de formato Avro, consulte o artigo [formato avri](format-avro.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se você quiser analisar os arquivos Avro ou gravar os dados no formato Avro, defina a propriedade `format` `type` como **AvroFormat**. Não será necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

```json
"format":
{
    "type": "AvroFormat",
}
```

Para usar o formato Avro em uma tabela de Hive, confira [Tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Observe os seguintes pontos:

* Não há suporte para [tipos de dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) (registros, enumerações, matrizes, mapas, uniões e fixo).

## <a name="binary-format"></a>Formato binário

Consulte o artigo [formato binário](format-binary.md) em detalhes.

## <a name="compression-support"></a>Suporte à compactação

O Azure Data Factory dá suporte para compactar/descompactar dados durante a cópia. Quando você especifica a propriedade `compression` em um conjunto de dados de entrada, a atividade de cópia lê os dados compactados da origem e descompacta-os e, quando você especifica a propriedade em um conjunto de dados de saída, a atividade de cópia compacta e grava os dados no coletor. Aqui estão alguns exemplos de cenários:

* Ler dados compactados em GZIP de um blob do Azure, descompactá-los e gravar os dados resultantes em um banco de dados SQL do Azure. Você define o conjunto de dados de Blob do Azure de entrada com a propriedade `compression` `type` como GZIP.
* Ler dados de um arquivo de texto sem formatação do Sistema de arquivos local, compactá-los usando o formato GZip e gravar os dados compactados em um blob do Azure. Você define um conjunto de dados de Blob do Azure de saída com a propriedade `compression` `type` como GZip.
* Leia o arquivo .zip do servidor FTP, descompacte-o para obter os arquivos e inclua-os no Azure Data Lake Store. Você define um conjunto de dados FTP de entrada com a propriedade `compression` `type` como ZipDeflate.
* Ler dados compactados em GZIP de um blob do Azure, descompactá-los, compactá-los usando BZIP2 e gravar os dados de resultado em um blob do Azure. Você define o conjunto de dados de Blob do Azure de entrada com `compression` `type` definido como GZIP e o conjunto de dados de saída com `compression` `type` definido como BZIP2.

Para especificar a compactação de um conjunto de dados, use a propriedade **compactação** no conjunto de dados JSON, como no exemplo a seguir:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

A seção **compactação** tem duas propriedades:

* **Tipo:** o codec de compactação, que pode ser **GZIP**, **Deflate**, **BZIP2** ou **ZipDeflate**.
* **Nível:** a taxa de compactação, que pode ser **Ideal** ou **Mais rápida**.

  * **Mais rápida:** a operação de compactação deve ser concluída o mais rápido possível, mesmo se o arquivo resultante não for compactado da maneira ideal.
  * **Ideal**: a operação de compactação deve ser concluída da maneira ideal, mesmo se a operação demorar mais tempo para ser concluída.

    Para saber mais, veja o tópico [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) .

> [!NOTE]
> Não há suporte para configurações de compactação de dados no **AvroFormat**, **OrcFormat** ou **ParquetFormat**. Ao ler arquivos nesses formatos, o Data Factory detecta e usa o codec de compactação nos metadados. Ao gravar em arquivos em um desses formatos, o Data Factory escolhe o codec de compactação padrão para esse formato. Por exemplo, ZLIB para OrcFormat e SNAPPY para ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Tipos de arquivo e formatos de compactação sem suporte

Você pode usar os recursos de extensibilidade do Azure Data Factory para transformar arquivos que não têm suporte.
Duas opções incluem Azure Functions e tarefas personalizadas usando o lote do Azure.

Você pode ver um exemplo que usa uma função do Azure para [extrair o conteúdo de um arquivo tar](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Para obter mais informações, consulte [Azure Functions atividade](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Você também pode criar essa funcionalidade usando uma atividade dotnet personalizada. Mais informações estão disponíveis [aqui](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Próximos passos

Consulte os artigos a seguir para armazenamentos de dados baseados em arquivo com suporte pelo Azure Data Factory:

- [Azure Blob Storage connector](connector-azure-blob-storage.md) (Conector do Armazenamento de Blobs do Azure)
- [Azure Data Lake Store connector](connector-azure-data-lake-store.md) (Conector do Azure Data Lake Store)
- [Amazon S3 connector](connector-amazon-simple-storage-service.md) (Conector do Amazon S3)
- [File System connector](connector-file-system.md) (Conector de sistema de arquivos)
- [FTP connector](connector-ftp.md) (Conector de FTP)
- [SFTP connector](connector-sftp.md) (Conector de SFTP)
- [HDFS connector](connector-hdfs.md) (Conector de HDFS)
- [HTTP connector](connector-http.md) (Conector de HTTP)
