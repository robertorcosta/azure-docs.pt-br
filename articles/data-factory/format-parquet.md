---
title: Formato Parquet na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato Parquet na Fábrica de Dados Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597619"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato Parquet na Fábrica de Dados Azure

Siga este artigo quando quiser **analisar os arquivos do Parquet ou escrever os dados no formato Parquet**. 

O formato Parquet é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Parquet.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade do tipo do conjunto de dados deve ser definida como **Parquet**. | Sim      |
| local         | Configurações de localização dos arquivos(s). Cada conector baseado em arquivo tem seu próprio `location`tipo de localização e propriedades suportadas em . **Veja detalhes na seção propriedades connector article-> Dataset**. | Sim      |
| compressionCodec | O codec de compressão para usar ao escrever em arquivos Parquet. Ao ler os arquivos do Parquet, as Fábricas de Dados determinam automaticamente o codec de compressão com base nos metadados do arquivo.<br>Os tipos suportados são "**nenhum**", "**gzip**", "**snappy**" (padrão), e "**lzo**". Nota atualmente A atividade copiar não suporta LZO quando ler/gravar arquivos do Parquet. | Não       |

> [!NOTE]
> O espaço em branco no nome da coluna não é suportado para arquivos parquet.

Abaixo está um exemplo do conjunto de dados Parquet no Azure Blob Storage:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte e pia do Parquet.

### <a name="parquet-as-source"></a>Parquet como fonte

As seguintes propriedades são suportadas na seção *** \*de origem de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida **como ParquetSource**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de leitura suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

### <a name="parquet-as-sink"></a>Parquet como pia

As seguintes propriedades são suportadas na seção *** \*de dissipação de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **ParquetSink**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de gravação suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Aprenda detalhes sobre [a transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) no mapeamento do fluxo de dados.

## <a name="data-type-support"></a>Suporte do tipo de dados

Os tipos de dados complexos do Parquet atualmente não são suportados (por exemplo, MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Usando o runtime de integração auto-hospedado

> [!IMPORTANT]
> Para copiar o runtime de integração auto-hospedado, por exemplo, entre lojas de dados no local e na nuvem, se você não estiver copiando arquivos do Parquet **como está,** você precisa instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou o OpenJDK** e **o Pacote Redistributable Do Microsoft Visual C++ 2010** na sua máquina de IR. Confira o parágrafo a seguir com mais detalhes.

Para a execução de cópia sigo no IR auto-hospedado com serialização/desserialização de *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* arquivos Parquet, o ADF localiza *`JAVA_HOME`* o tempo de execução java verificando primeiro o registro para JRE, se não encontrado, verificando em segundo lugar a variável do sistema para OpenJDK.

- **Para usar JRE**: O IR de 64 bits requer JRE de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: É suportado desde a versão 3.13 do IR. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.
- **Para instalar o Visual C++ 2010 Redistributable Package**: Visual C++ 2010 Redistributable Package não está instalado com instalações de IR auto-hospedadas. É possível encontrá-lo [aqui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se você copiar os dados para/do formato Parquet usando o IR auto-hospedado e ocorrências de erro informando que "Ocorreu um erro ao chamar o java, mensagem: **espaço de heap java.lang.OutOfMemoryError:Java**", poderá adicionar uma variável de ambiente `_JAVA_OPTIONS` na máquina que hospeda o IR auto-hospedado para ajustar o tamanho mín/máx do heap para JVM para capacitar a cópia e executar novamente o pipeline.

![Definir o tamanho do heap da JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: defina a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para uma JVM (Máquina Virtual Java), enquanto `Xmx` especifica o pool de alocação de memória máxima. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa min 64 MB e max 1G.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
