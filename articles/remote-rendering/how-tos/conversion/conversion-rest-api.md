---
title: A API de conversão de ativos REST
description: Descreve como converter um ativo através da API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887037"
---
# <a name="use-the-model-conversion-rest-api"></a>Usar a API REST de conversão de modelo

O serviço [de conversão do modelo](model-conversion.md) é controlado através de uma [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Este artigo descreve os detalhes da API do serviço de conversão.

## <a name="regions"></a>Regiões

Consulte a [lista de regiões disponíveis](../../reference/regions.md) para as URLs base para enviar as solicitações.

## <a name="common-headers"></a>Cabeçalhos comuns

### <a name="common-request-headers"></a>Cabeçalhos de solicitação comuns

Estes cabeçalhos devem ser especificados para todas as solicitações:

- O cabeçalho **de autorização** deve ter o valor de "Portador [*TOKEN*]", onde [*TOKEN*] é um [token de acesso de serviço](../tokens.md).

### <a name="common-response-headers"></a>Cabeçalhos de resposta comuns

Todas as respostas contêm estes cabeçalhos:

- O cabeçalho **MS-CV** contém uma seqüência única que pode ser usada para rastrear a chamada dentro do serviço.

## <a name="endpoints"></a>Pontos de extremidade

O serviço de conversão fornece três pontos finais da API REST para:

- iniciar a conversão do modelo usando uma conta de armazenamento vinculada à sua conta de renderização remota do Azure. 
- iniciar a conversão do modelo usando assinaturas de acesso compartilhadas fornecidas *(SAS)*.
- consultar o status de conversão

### <a name="start-conversion-using-a-linked-storage-account"></a>Iniciar conversão usando uma conta de armazenamento vinculada
Sua conta de renderização remota do Azure precisa ter acesso à conta de armazenamento fornecida seguindo as etapas de como [vincular contas de armazenamento](../create-an-account.md#link-storage-accounts).

| Ponto de extremidade | Método |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversões/create | POST |

Retorna o ID da conversão em curso, embrulhado em um documento JSON. O nome de campo é "conversionId".

#### <a name="request-body"></a>Corpo da solicitação


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Iniciar conversão usando assinaturas de acesso compartilhadas fornecidas
Se sua conta ARR não estiver vinculada à sua conta de armazenamento, esta interface REST permite que você forneça acesso usando *assinaturas de acesso compartilhado (SAS)*.

| Ponto de extremidade | Método |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversões/createWithSharedAccessSignature | POST |

Retorna o ID da conversão em curso, embrulhado em um documento JSON. O nome de campo é "conversionId".

#### <a name="request-body"></a>Corpo da solicitação

O órgão de solicitação é o mesmo que na chamada create REST acima, mas a entrada e a saída contêm *tokens SAS (SAS).* Esses tokens fornecem acesso à conta de armazenamento para ler a entrada e escrever o resultado da conversão.

> [!NOTE]
> Esses tokens Uri SAS são as strings de consulta e não o URI completo. 


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Status de conversão de pesquisa
O status de uma conversão contínua iniciada com uma das chamadas REST acima pode ser consultado usando a seguinte interface:


| Ponto de extremidade | Método |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversões/conversõesId**conversionId** | GET |

Retorna um documento JSON com um campo "status" que pode ter os seguintes valores:

- "Correndo"
- "Success"
- "Fracasso"

Se o status for "Falha", haverá um campo adicional de "erro" com um subcampo de "mensagem" contendo informações de erro. Logs adicionais serão carregados no recipiente de saída.

## <a name="next-steps"></a>Próximas etapas

- [Usar o armazenamento de Blobs do Azure para conversão de modelo](blob-storage.md)
- [Conversão de modelo](model-conversion.md)
