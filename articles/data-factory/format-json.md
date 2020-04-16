---
title: Formato JSON na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato JSON na Fábrica de Dados Do Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: c18d767afd3721bd6f6250058b9fbe66990133e4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417735"
---
# <a name="json-format-in-azure-data-factory"></a>Formato JSON na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando quiser **analisar os arquivos JSON ou escrever os dados no formato JSON**. 

O formato JSON é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP.](connector-sftp.md)

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados JSON.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade do tipo do conjunto de dados deve ser definida como **Json**. | Sim      |
| local         | Configurações de localização dos arquivos(s). Cada conector baseado em arquivo tem seu próprio `location`tipo de localização e propriedades suportadas em . **Veja detalhes na seção propriedades connector article-> Dataset**. | Sim      |
| encodingName     | O tipo de codificação usado para ler/gravar arquivos de teste. <br>Os valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7" ", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM855", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM863", "IBM864", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", "IBM865", 0", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01144", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR". 8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13". , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Não       |
| compactação | Grupo de propriedades para configurar compactação de arquivos. Configure esta seção quando quiser fazer compressão/descompressão durante a execução da atividade. | Não |
| type | O codec de compressão usado para ler/gravar arquivos JSON. <br>Os valores permitidos são **bzip2,** **gzip,** **deflate**, **ZipDeflate,** **snappy,** ou **lz4**. para usar ao salvar o arquivo. O padrão não é compactado.<br>**Nota** atualmente A atividade de cópia não suporta "snappy" & "lz4", e o fluxo de dados de mapeamento não suporta "ZipDeflate".<br>**Nota** ao usar a atividade de cópia para descompactar arquivos ZipDeflate e gravar no armazenamento `<path specified in dataset>/<folder named as source zip file>/`de dados do sink baseado em arquivos, os arquivos serão extraídos para a pasta: . | Não.  |
| level | A relação de compressão. <br>Os valores permitidos são **ótimos** ou **mais rápidos**.<br>- **O mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não esteja compactado de forma ideal.<br>- **Ideal**: A operação de compressão deve ser compactada de forma ideal, mesmo que a operação deva mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Não       |

Abaixo está um exemplo do conjunto de dados JSON no Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte e pia JSON.

### <a name="json-as-source"></a>JSON como fonte

As seguintes propriedades são suportadas na seção *** \*de origem de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **JSONSource**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de leitura suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

### <a name="json-as-sink"></a>JSON como pia

As seguintes propriedades são suportadas na seção *** \*de dissipação de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **JSONSink**. | Sim      |
| Formatsettings | Um grupo de propriedades. Consulte a tabela **de configurações de gravação JSON** abaixo. | Não       |
| configurações de armazenamento | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de gravação suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

Configurações de **gravação JSON** suportadas em: `formatSettings`

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | O tipo de formatoConfigurações devem ser definidas como **JsonWriteSettings**. | Sim                                                   |
| filePattern |Indique o padrão de dados armazenados em cada arquivo JSON. Os valores permitidos são: **setOfObjects** e **arrayOfObjects**. O valor **padrão** é **setOfObjects**. Veja a seção [Padrões de arquivo JSON](#json-file-patterns) para obter detalhes sobre esses padrões. |Não |

### <a name="json-file-patterns"></a>Padrões de arquivo JSON

A atividade de cópia pode detectar e analisar automaticamente os seguintes padrões de arquivos JSON. 

- **Tipo I: setOfObjects**

    Cada arquivo contém um único objeto ou vários objetos concatenados/delimitados por linhas. 
    Quando essa opção é escolhida no dissipador de atividades de cópia, a atividade de cópia produz um único arquivo JSON com cada objeto por linha (delimitado de linha).

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

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Os tipos de arquivos JSON podem ser usados tanto como um sink quanto como uma fonte no mapeamento do fluxo de dados.

### <a name="creating-json-structures-in-a-derived-column"></a>Criando estruturas JSON em uma coluna derivada

Você pode adicionar uma coluna complexa ao seu fluxo de dados através do construtor de expressão de coluna derivada. Na transformação da coluna derivada, adicione uma nova coluna e abra o construtor de expressão clicando na caixa azul. Para tornar uma coluna complexa, você pode inserir a estrutura JSON manualmente ou usar o UX para adicionar subcolunas interativamente.

#### <a name="using-the-expression-builder-ux"></a>Usando o construtor de expressão UX

No painel lateral do esquema de saída, gire sobre uma coluna e clique no ícone de adição. Selecione **Adicionar subcoluna** para tornar a coluna um tipo complexo.

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar subcoluna")

Você pode adicionar colunas e subcolunas adicionais da mesma forma. Para cada campo não complexo, uma expressão pode ser adicionada no editor de expressão à direita.

![Coluna complexa](media/data-flow/complexcolumn.png "Coluna complexa")

#### <a name="entering-the-json-structure-manually"></a>Inserindo a estrutura JSON manualmente

Para adicionar manualmente uma estrutura JSON, adicione uma nova coluna e digite a expressão no editor. A expressão segue o seguinte formato geral:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Se esta expressão fosse inserida para uma coluna chamada "complexColumn", então ela seria escrita na pia como o seguinte JSON:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Script manual de amostra para definição hierárquica completa
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Opções de formato de origem

O uso de um conjunto de dados JSON como fonte no fluxo de dados permite definir cinco configurações adicionais. Essas configurações podem ser encontradas sob o acordeão **de configurações JSON** na guia **Opções de** origem.  

![Configurações json](media/data-flow/json-settings.png "Configurações de JSON")

#### <a name="default"></a>Padrão

Por padrão, os dados JSON são lidos no formato a seguir.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Documento único

Se **o documento único** for selecionado, o mapeamento dos fluxos de dados lerá um documento JSON de cada arquivo. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>Nomes de colunas não citadas

Se **os nomes das colunas não citados forem** selecionados, os fluxos de dados de mapeamento lerão colunas JSON que não estão cercadas por citações. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Tem comentários

Selecione **Tem comentários** se os dados JSON têm comentários de estilo C ou C++.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Único citado

Selecione **Único citado** se os campos e valores JSON usarem aspas simples em vez de aspas duplas.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Barra invertida escapou

Selecione **Single citado** se os cortes de fundo forem usados para escapar de caracteres nos dados JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
