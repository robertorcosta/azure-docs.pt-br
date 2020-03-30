---
title: Obtenha atividade de metadados na fábrica de dados do Azure
description: Saiba como usar a atividade Get Metadata em um pipeline de Fábrica de Dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: jingwang
ms.openlocfilehash: a0c07aaf27825254f776a03b9b9ca2cbeddca02d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250269"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Obtenha atividade de metadados na fábrica de dados do Azure

Você pode usar a atividade Get Metadata para recuperar os metadados de quaisquer dados na Fábrica de Dados do Azure. Você pode usar essa atividade nos seguintes cenários:

- Valide os metadados de qualquer dado.
- Acione um pipeline quando os dados estão prontos/disponíveis.

A seguinte funcionalidade está disponível no fluxo de controle:

- Você pode usar a saída da atividade Get Metadata em expressões condicionais para realizar a validação.
- Você pode acionar um pipeline quando uma condição é satisfeita via Do Until looping.

## <a name="capabilities"></a>Funcionalidades

A atividade Get Metadata toma um conjunto de dados como entrada e retorna informações de metadados como saída. Atualmente, os seguintes conectores e metadados recuperáveis correspondentes são suportados. O tamanho máximo dos metadados retornados é de 2 MB.

>[!NOTE]
>Se você executar a atividade Get Metadata em um tempo de execução de integração auto-hospedado, os recursos mais recentes serão suportados na versão 3.6 ou posterior.

### <a name="supported-connectors"></a>Conectores com suporte

**Armazenamento de arquivos**

| Conector/metadados | itemName<br>(arquivo/pasta) | itemType<br>(arquivo/pasta) | tamanho<br>(arquivo) | criado<br>(arquivo/pasta) | lastModified<br>(arquivo/pasta) |childItems<br>(pasta) |contentMD5<br>(arquivo) | estrutura<br/>(arquivo) | ColumnCount<br>(arquivo) | exists<br>(arquivo/pasta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Arquivos do Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Sistema de arquivos](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Ftp](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Para Amazon S3 e `lastModified` Google Cloud Storage, aplica-se ao balde e `exists` à chave, mas não à pasta virtual, e aplica-se ao balde e à chave, mas não ao prefixo ou pasta virtual.
- Para armazenamento Azure `lastModified` Blob, aplica-se ao recipiente e à bolha, mas não à pasta virtual.
- `lastModified`o filtro atualmente se aplica a itens de filtragem de crianças, mas não à pasta/arquivo especificado em si.
- O filtro curinga em pastas/arquivos não é suportado para a atividade Get Metadata.

**Banco de dados relacional**

| Conector/metadados | estrutura | ColumnCount | exists |
|:--- |:--- |:--- |:--- |
| [Banco de dados SQL do Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Instância gerenciada do Banco de Dados SQL do Azure](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opções de metadados

Você pode especificar os seguintes tipos de metadados na lista de campo de atividade Get Metadata para recuperar as informações correspondentes:

| Tipo de Metadados | Descrição |
|:--- |:--- |
| itemName | Nome do arquivo ou pasta. |
| itemType | Tipo do arquivo ou pasta. O valor `File` devolvido `Folder`é ou . |
| tamanho | Tamanho do arquivo, em bytes. Aplicável apenas aos arquivos. |
| criado | O datetime da última modificação do arquivo ou da pasta. |
| lastModified | O datetime da última modificação do arquivo ou da pasta. |
| childItems | Lista de subpastas e arquivos na pasta dada. Aplicável apenas a pastas. Valor devolvido é uma lista do nome e tipo de cada item filho. |
| contentMD5 | MD5 do arquivo. Aplicável apenas aos arquivos. |
| estrutura | Estrutura de dados do arquivo ou tabela de banco de dados relacional. Valor retornado é uma lista de nomes de colunas e tipos de coluna. |
| ColumnCount | Número de colunas no arquivo ou tabela relacional. |
| exists| Se existe um arquivo, pasta ou tabela. Observe que `exists` se for especificado na lista de campo Obter metadados, a atividade não falhará mesmo se o arquivo, pasta ou tabela não existirem. Em `exists: false` vez disso, é devolvido na saída. |

>[!TIP]
>Quando você quiser validar que existe um arquivo, pasta ou tabela, especifique `exists` na lista de campos de atividade Get Metadata. Em seguida, `exists: true/false` você pode verificar o resultado na saída de atividade. Se `exists` não for especificado na lista de campos, a atividade Obter metadados falhará se o objeto não for encontrado.

>[!NOTE]
>Quando você receber metadados de `modifiedDatetimeStart` armazenamentos de arquivos e configurar ou , `modifiedDatetimeEnd`a `childItems` saída in incluirá apenas arquivos no caminho dado que têm um último tempo modificado dentro do intervalo especificado. Não incluirá itens em subpastas.

## <a name="syntax"></a>Sintaxe

**Obtenha atividade de metadados**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Conjunto de dados**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo

Atualmente, a atividade Get Metadata pode retornar os seguintes tipos de informações de metadados:

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
fieldList | Os tipos de informações de metadados necessárias. Para obter detalhes sobre metadados suportados, consulte a seção [de opções de metadados](#metadata-options) deste artigo. | Sim 
dataset | O conjunto de dados de referência cujos metadados devem ser recuperados pela atividade Get Metadata. Consulte a seção [Recursos](#capabilities) para obter informações sobre conectores suportados. Consulte os tópicos específicos do conector para obter detalhes da sintaxe do conjunto de dados. | Sim
Formatsettings | Aplique ao usar o conjunto de dados do tipo de formato. | Não
configurações de armazenamento | Aplique ao usar o conjunto de dados do tipo de formato. | Não

## <a name="sample-output"></a>Saída de exemplo

Os resultados do Get Metadata são mostrados na saída de atividade. A seguir estão duas amostras mostrando extensas opções de metadados. Para utilizar os resultados em uma atividade `@{activity('MyGetMetadataActivity').output.itemName}`subseqüente, use este padrão: .

### <a name="get-a-files-metadata"></a>Obter metadados de um arquivo

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Obter metadados de uma pasta

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
Conheça outras atividades de fluxo de controle suportadas pela Data Factory:

- [Executar atividade de pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
