---
title: Formato parquet no Azure Data Factory
description: Este tópico descreve como lidar com o formato parquet no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597619"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet no Azure Data Factory

Siga este artigo quando desejar **analisar os arquivos parquet ou gravar os dados no formato parquet**. 

O formato parquet tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de parquet.

| Propriedade         | DESCRIÇÃO                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **parquet**. | Sim      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location`. **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |
| compressionCodec | O codec de compactação a ser usado ao gravar em arquivos parquet. Ao ler de arquivos parquet, as fábricas de dados determinam automaticamente o codec de compactação com base nos metadados do arquivo.<br>Os tipos com suporte são "**None**", "**gzip**", "**encaixado**" (padrão) e "**LZO**". Observação a atividade de cópia atualmente não dá suporte a LZO quando arquivos parquet de leitura/gravação. | Não       |

> [!NOTE]
> Não há suporte para o espaço em branco no nome da coluna para arquivos parquet.

Veja abaixo um exemplo de conjunto de parquet no armazenamento de BLOBs do Azure:

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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do parquet.

### <a name="parquet-as-source"></a>Parquet como fonte

As propriedades a seguir têm suporte na seção ***\*de origem*** da atividade de cópia\*.

| Propriedade      | DESCRIÇÃO                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **ParquetSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings`. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="parquet-as-sink"></a>Parquet como coletor

As propriedades a seguir têm suporte na seção de ***\*do coletor*** de atividade de cópia\*.

| Propriedade      | DESCRIÇÃO                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **ParquetSink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings`. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Aprenda detalhes da [transformação de origem](data-flow-source.md) e da [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.

## <a name="data-type-support"></a>Suporte do tipo de dados

Atualmente, não há suporte para tipos de dados complexos do parquet (por exemplo, mapa, lista, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Usando Integration Runtime auto-hospedados

> [!IMPORTANT]
> Para a cópia capacitada por Integration Runtime auto-hospedados, por exemplo, entre armazenamentos de dados locais e na nuvem, se você não estiver copiando arquivos parquet no estado em que se **encontra**, precisará instalar o **pacote redistribuível do C++**  **JRE 8 de 64 bits (Java Runtime Environment) ou do OpenJDK** e do Microsoft Visual 2010 no computador ir. Verifique o parágrafo a seguir com mais detalhes.

Para cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo Parquet, o ADF localiza o runtime do Java verificando o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, em primeiro lugar e, se não encontrado, realiza, em segundo lugar, a verificação na variável do sistema *`JAVA_HOME`* para o OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.
- **Para instalar o C++ pacote redistribuível do Visual 2010**: o pacote redistribuível do Visual C++ 2010 não está instalado com instalações de ir de hospedagem interna. É possível encontrá-lo [aqui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se você copiar os dados para/do formato Parquet usando o IR auto-hospedado e ocorrências de erro informando que "Ocorreu um erro ao chamar o java, mensagem: **espaço de heap java.lang.OutOfMemoryError:Java**", poderá adicionar uma variável de ambiente `_JAVA_OPTIONS` na máquina que hospeda o IR auto-hospedado para ajustar o tamanho mín/máx do heap para JVM para capacitar a cópia e executar novamente o pipeline.

![Definir o tamanho do heap da JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: defina a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para uma JVM (Máquina Virtual Java), enquanto `Xmx` especifica o pool de alocação de memória máxima. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa mín. 64 MB e máximo 1G.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
