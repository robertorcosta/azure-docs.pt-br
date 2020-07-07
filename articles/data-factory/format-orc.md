---
title: Formato ORC no Azure Data Factory
description: Este tópico descreve como lidar com o formato ORC no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 9b68d3724c6390fc5d30745924451e27ef9855b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81417718"
---
# <a name="orc-format-in-azure-data-factory"></a>Formato ORC no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando desejar **analisar os arquivos orc ou gravar os dados no formato Orc**. 

O formato ORC tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de ORC.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **Orc**. | Sim      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` . **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |

Veja abaixo um exemplo de conjunto de ORC no armazenamento de BLOBs do Azure:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Observe os seguintes pontos:

* Não há suporte para tipos de dados complexos (STRUCT, MAP, LIST e UNION).
* Não há suporte para o espaço em branco no nome da coluna.
* O arquivo ORC tem três [opções de compactação](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB e SNAPPY. O Data Factory dá suporte à leitura de dados de arquivo ORC em qualquer um dos formatos compactados acima. Ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao gravar um arquivo ORC, o Data Factory escolhe ZLIB, que é o padrão para ORC. Não há nenhuma opção para substituir esse comportamento neste momento.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do ORC.

### <a name="orc-as-source"></a>ORC como fonte

As propriedades a seguir têm suporte na seção *** \* origem \* *** da atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **OrcSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="orc-as-sink"></a>ORC como coletor

As propriedades a seguir têm suporte na seção *** \* coletor \* *** de atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **OrcSink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

## <a name="using-self-hosted-integration-runtime"></a>Usando Integration Runtime auto-hospedados

> [!IMPORTANT]
> Para a cópia habilitada pelo Integration Runtime auto-hospedado, por exemplo, entre armazenamentos de dados locais e na nuvem, se você não estiver copiando arquivos ORC no estado em que se **encontra**, precisará instalar o **pacote redistribuível** do **JRE 8 de 64 bits (Java Runtime Environment) ou OpenJDK** e Microsoft Visual C++ 2010 no seu computador ir. Verifique o parágrafo a seguir com mais detalhes.

Para a cópia em execução no IR auto-hospedado com a serialização/desserialização de arquivo ORC, o ADF localiza o tempo de execução do Java verificando primeiro o registro do *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* jre, se não for encontrado, verificando a variável do sistema em segundo lugar *`JAVA_HOME`* para OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.
- **Para instalar Visual C++ pacote redistribuível 2010**: o pacote redistribuível Visual C++ 2010 não está instalado com instalações de ir de hospedagem interna. É possível encontrá-lo [aqui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se você copiar dados de/para o formato ORC usando o autohospedado Integration Runtime e erro de ocorrência que diz "ocorreu um erro ao invocar Java, mensagem: **Java. lang. OutOfMemoryError: espaço de heap Java**", você pode adicionar uma variável `_JAVA_OPTIONS` de ambiente no computador que hospeda o ir do modo auto-hospedado para ajustar o tamanho de heap mínimo/máximo para a JVM para capacitar tal cópia e

![Definir o tamanho do heap da JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: defina a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para uma JVM (Máquina Virtual Java), enquanto `Xmx` especifica o pool de alocação de memória máxima. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa mín. 64 MB e máximo 1G.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
