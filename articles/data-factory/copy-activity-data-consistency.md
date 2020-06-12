---
title: Verificação de consistência de dados na atividade de cópia
description: Saiba mais sobre como habilitar a verificação de consistência de dados na atividade de cópia no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a386c7d44cf5ba7eda895006cda7ce1fa9b798ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663704"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Verificação de consistência de dados na atividade de cópia (versão prévia)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando você move dados do repositório de origem para o de destino, a atividade de cópia do Azure Data Factory fornece uma opção para realizar a verificação de consistência de dados adicional a fim de garantir que os dados não sejam apenas copiados com êxito da origem para o repositório de destino, mas também verificados como consistentes entre o repositório de origem e destino. Depois que dados inconsistentes tiverem sido encontrados durante a movimentação de dados, você poderá abortar a atividade de cópia ou continuar a copiar o restante habilitando a configuração de tolerância a falhas para ignorar dados inconsistentes. Você pode obter os nomes de objeto ignorados habilitando a configuração de log de sessão na atividade de cópia. 

> [!IMPORTANT]
> Este recurso está em versão preliminar no momento com as seguintes limitações em que estamos trabalhando ativamente:
>- A verificação de consistência de dados está disponível somente em arquivos binários que copiam entre repositórios baseados em arquivo com o comportamento 'PreserveHierarchy' na atividade de cópia. Para copiar dados tabulares, a verificação de consistência de dados ainda não está disponível na atividade de cópia.
>- Quando você habilita a configuração de log de sessão na atividade de cópia para registrar em log os arquivos inconsistentes que estão sendo ignorados, a integridade do arquivo de log não pode ser 100% garantida se a atividade de cópia tiver falhado.
>- O log de sessão contém apenas arquivos inconsistentes, onde os arquivos copiados com êxito não são registrados até o momento.

## <a name="supported-data-stores"></a>Armazenamento de dados com suporte

### <a name="source-data-stores"></a>Armazenamentos de dados de origem

-   [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Armazenamento de Arquivos do Azure](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Sistema de Arquivos](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Armazenamento de dados de destino

-   [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Armazenamento de Arquivos do Azure](connector-azure-file-storage.md)
-   [Sistema de Arquivos](connector-file-system.md)


## <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição de JSON para habilitar a verificação de consistência de dados na atividade de cópia: 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | -------- 
validateDataConsistency | Se você definir true para essa propriedade, a atividade de cópia verificará o tamanho do arquivo, lastModifiedDate e soma de verificação MD5 para cada objeto copiado da origem para o repositório de destino para garantir a consistência dos dados entre o repositório de origem e de destino. Lembre-se de que o desempenho da cópia será afetado pela habilitação dessa opção.  | True<br/>False (padrão) | Não
dataInconsistency | Um dos pares chave-valor dentro do conjunto de propriedades skipErrorFile para determinar se você deseja ignorar os dados inconsistentes.<br/> -True: você deseja copiar o restante ignorando arquivos inconsistentes.<br/> - Falso: você deseja anular a atividade de cópia quando dados inconsistentes forem encontrados.<br/>Lembre-se de que essa propriedade só é válida quando você define validateDataConsistency como True.  | True<br/>False (padrão) | Não
logStorageSettings | Um grupo de propriedades que pode ser especificado para habilitar o log de sessão para objetos ignorados. | | Não
linkedServiceName | O serviço vinculado do [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os arquivos de log da sessão. | O nome de um serviço vinculado `AzureBlobStorage` ou `AzureBlobFS`, que se refere à instância de armazenamento que você usa para armazenar os arquivos de log. | Não
caminho | O caminho dos arquivos de log. | Especifique o caminho que você quer armazenar os arquivos de log. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não

>[!NOTE]
>- Não há suporte para consistência de dados no cenário de cópia de preparo. 
>- Ao copiar arquivos binários de qualquer armazenamento para o armazenamento de Blobs do Azure ou Azure Data Lake Storage Gen2, a atividade de cópia faz a verificação do tamanho do arquivo e a soma de verificação MD5 para garantir a consistência dos dados entre os armazenamentos de origem e destino. 
>- Ao copiar arquivos binários de qualquer armazenamento para armazenamentos além de Blobs do Azure ou Azure Data Lake Storage Gen2, a atividade de cópia faz a verificação do tamanho do arquivo para garantir a consistência dos dados entre o armazenamento de origem e o de destino.


## <a name="monitoring"></a>Monitoramento

### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Depois que a atividade de cópia for executada completamente, você poderá ver o resultado da verificação de consistência de dados da saída de cada execução da atividade de cópia:

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
Você pode ver os detalhes da verificação de consistência de dados da "propriedade dataConsistencyVerification".

Valor de **VerificationResult**: 
-   **Verified**:  Os dados copiados foram verificados para serem consistentes entre o repositório de origem e destino. 
-   **NotVerified**: Seus dados copiados não foram verificados como consistentes porque você não habilitou o validateDataConsistency na atividade de cópia. 
-   **Unsupported**: Seus dados copiados não foram verificados como consistentes porque a verificação de consistência de dados não tem suporte para esse par de cópia em particular. 

Valor de **InconsistentData**: 
-   **Encontrado**: A atividade de cópia do ADF encontrou dados inconsistentes. 
-   **Ignorada**: A atividade de cópia do ADF encontrou e ignorou dados inconsistentes. 
-   **Nenhum**: A atividade de cópia do ADF não encontrou dados inconsistentes. Pode ser porque seus dados foram verificados para serem consistentes entre o repositório de origem e de destino ou porque você desabilitou o validateDataConsistency na atividade de cópia. 

### <a name="session-log-from-copy-activity"></a>Log de sessão da atividade de cópia

Se você configurar para registrar em log os arquivos inconsistentes, poderá encontrar o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`.  Os arquivos de log serão os arquivos csv. 

O esquema de um arquivo de log é o seguinte:

Coluna | Descrição 
-------- | -----------  
Timestamp | O carimbo de data/hora quando o Azure Data Factory ignora os arquivos inconsistentes.
Nível | O nível de log deste item. Ele estará no nível de "Aviso" para o item que mostra o arquivo ignorado.
OperationName | Comportamento operacional da atividade de cópia do Azure Data Factory em cada arquivo. Será "FileSkip" para especificar o arquivo a ser ignorado.
OperationItem | O nome de arquivo a ser ignorado.
Mensagem | Mais informações para ilustrar por que os arquivos estão sendo ignorados.

O exemplo de um arquivo de log é o seguinte: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
No arquivo de log acima, você pode ver que sample1.csv foi ignorado porque ele não pôde ser verificado para ser consistente entre o repositório de origem e de destino. Você pode obter mais detalhes sobre por que o sample1.csv se torna inconsistente: ele estava sendo alterado por outros aplicativos quando a atividade de cópia do ADF estava sendo copiada ao mesmo tempo. 



## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md)


