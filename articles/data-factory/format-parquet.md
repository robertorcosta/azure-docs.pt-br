---
title: Formato parquet no Azure Data Factory | Microsoft Docs
description: Este tópico descreve como lidar com o formato parquet no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 92950a79be43bc656d50e2ced106a2fdb47f53bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387717"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet no Azure Data Factory

Siga este artigo quando desejar **analisar os arquivos parquet ou gravar os dados no formato parquet**. 

O formato parquet tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Armazenamento em nuvem](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de parquet.

| Propriedade         | Descrição                                                  | obrigatórios |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **parquet**. | SIM      |
| location         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e as propriedades com suporte em `location`. **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | SIM      |
| compressionCodec | O codec de compactação a ser usado ao gravar em arquivos parquet. Ao ler de arquivos parquet, Data Factory determinará automaticamente o codec de compactação com base nos metadados do arquivo.<br>Os tipos com suporte são "**None**", "**gzip**", "**encaixado**" (padrão) e "**LZO**". Observação a atividade de cópia atualmente não dá suporte a LZO. | Não       |

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

As propriedades a seguir têm suporte na seção de atividade de cópia ***\*source @ no__t-2*** .

| Propriedade      | Descrição                                                  | obrigatórios |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **ParquetSource**. | SIM      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings`. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="parquet-as-sink"></a>Parquet como coletor

As propriedades a seguir têm suporte na seção de atividade de cópia ***\*sink @ no__t-2*** .

| Propriedade      | Descrição                                                  | obrigatórios |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **ParquetSink**. | SIM      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings`. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Aprenda detalhes da [transformação de origem](data-flow-source.md) e da [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.

## <a name="data-type-support"></a>Suporte ao tipo de dados

Atualmente, não há suporte para tipos de dados complexos do parquet (por exemplo, mapa, lista, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Usando Integration Runtime auto-hospedados

> [!IMPORTANT]
> Para cópias autorizadas pelo Integration Runtime (auto-hospedado), por exemplo, entre repositórios de dados locais e na nuvem, se você não estiver copiando arquivos Parquet **como são**, precisará instalar o **JRE (Java Runtime Environment) 8 de 64 bits ou o OpenJDK** no IR de seu computador. Confira o próximo parágrafo para obter mais detalhes.

Para cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo Parquet, o ADF localiza o tempo de execução do Java verificando o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, em primeiro lugar e, se não encontrado, realiza, em segundo lugar, a verificação na variável do sistema *`JAVA_HOME`* para o OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar o OpenJDK**: ele tem suporte desde a versão do IR 3.13. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.

> [!TIP]
> Se você copiar os dados para/do formato Parquet usando o IR auto-hospedado e ocorrências de erro informando que "Ocorreu um erro ao chamar o java, mensagem: **espaço de heap java.lang.OutOfMemoryError:Java**", poderá adicionar uma variável de ambiente `_JAVA_OPTIONS` na máquina que hospeda o IR auto-hospedado para ajustar o tamanho mín/máx do heap para JVM para capacitar a cópia e executar novamente o pipeline.

![Definir o tamanho do heap da JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: defina a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para uma JVM (Máquina Virtual Java), enquanto `Xmx` especifica o pool de alocação de memória máxima. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa um mínimo de 64MB e um máximo de 1G.

## <a name="next-steps"></a>Próximos passos

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
