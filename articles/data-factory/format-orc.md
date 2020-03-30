---
title: Formato ORC na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato ORC na Fábrica de Dados Do Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597483"
---
# <a name="orc-format-in-azure-data-factory"></a>Formato ORC na Fábrica de Dados Azure

Siga este artigo quando quiser **analisar os arquivos ORC ou escrever os dados no formato ORC**. 

O formato ORC é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP.](connector-sftp.md)

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados ORC.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade do tipo do conjunto de dados deve ser definida **como Orc**. | Sim      |
| local         | Configurações de localização dos arquivos(s). Cada conector baseado em arquivo tem seu próprio `location`tipo de localização e propriedades suportadas em . **Veja detalhes na seção propriedades connector article-> Dataset**. | Sim      |

Abaixo está um exemplo do conjunto de dados ORC no Azure Blob Storage:

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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte e pia orc.

### <a name="orc-as-source"></a>ORC como fonte

As seguintes propriedades são suportadas na seção *** \*de origem de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **OrcSource**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de leitura suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

### <a name="orc-as-sink"></a>ORC como pia

As seguintes propriedades são suportadas na seção *** \*de dissipação de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **OrcSink**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de gravação suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

## <a name="using-self-hosted-integration-runtime"></a>Usando o runtime de integração auto-hospedado

> [!IMPORTANT]
> Para copiar com poder de integração auto-hospedada Runtime, por exemplo, entre lojas de dados no local e na nuvem, se você não estiver copiando arquivos ORC **como está,** você precisa instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou o OpenJDK** e **o Microsoft Visual C++ 2010 Redistributable Package** em sua máquina de IR. Confira o parágrafo a seguir com mais detalhes.

Para a execução de cópia no IR auto-hospedado com serialização/desserialização de arquivos ORC, o ADF localiza o tempo de execução java verificando primeiro o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, se não encontrado, verificando em segundo lugar a variável *`JAVA_HOME`* do sistema para OpenJDK.

- **Para usar JRE**: O IR de 64 bits requer JRE de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: É suportado desde a versão 3.13 do IR. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.
- **Para instalar o Visual C++ 2010 Redistributable Package**: Visual C++ 2010 Redistributable Package não está instalado com instalações de IR auto-hospedadas. É possível encontrá-lo [aqui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se você copiar dados para/do formato ORC usando o Executetime de integração auto-hospedado e o erro de acerto dizendo "Ocorreu um erro `_JAVA_OPTIONS` ao invocar java, mensagem: **java.lang.OutOfMemoryError:Java heap space",** você pode adicionar uma variável de ambiente na máquina que hospeda o IR auto-hospedado para ajustar o tamanho do heap min/max para JVM para empoderar tal cópia e, em seguida, executar novamente o pipeline.

![Definir o tamanho do heap da JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: defina a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para uma JVM (Máquina Virtual Java), enquanto `Xmx` especifica o pool de alocação de memória máxima. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa min 64 MB e max 1G.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
