---
title: Gerenciar o backup do compartilhamento de arquivos do Azure com a API de descanso
description: Saiba como usar a API REST para gerenciar e monitorar os compartilhamentos de arquivos do Azure que são apoiados pelo Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 9d29b226aff568c91de8e1f19ddc0c64f8169e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444726"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Gerenciar o backup do compartilhamento de arquivos do Azure com a API REST

Este artigo explica como executar tarefas para gerenciar e monitorar os compartilhamentos de arquivos do Azure que são apoiados pelo [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

## <a name="monitor-jobs"></a>Monitorar trabalhos

O serviço de backup do Azure aciona trabalhos executados em segundo plano. Isso inclui cenários como ativar backup, restaurar operações e desativar backup. Esses trabalhos podem ser rastreados usando seus IDs.

### <a name="fetch-job-information-from-operations"></a>Buscar informações sobre o trabalho das operações

Uma operação como o acionamento de backup sempre retornará um JobID na resposta.

Por exemplo, a resposta final de uma operação de [API REST de backup de gatilho](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) é a seguinte:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

O trabalho de backup do compartilhamento de arquivos do Azure é identificado pelo campo **jobId** e pode ser rastreado como mencionado [aqui](https://docs.microsoft.com/rest/api/backup/jobdetails/) usando uma solicitação GET.

### <a name="tracking-the-job"></a>Acompanhando o trabalho

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

O {jobName} é o "jobId" mencionado acima. A resposta é sempre "200 OK" com o campo **de status** indicando o status do trabalho. Uma vez que esteja "Concluído" ou "Concluídocomavisos", a seção **extendedInfo** revela mais detalhes sobre o trabalho.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Resposta

Nome  | Type  |  Descrição
--- | --- | ----
200 OK |  JobResource  | OK

#### <a name="response-example"></a>Exemplo de resposta

Uma vez que o *GET* URI é enviado, uma resposta de 200 é retornada.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Modificar a política

Para alterar a diretiva com a qual o compartilhamento de arquivos é protegido, você pode usar o mesmo formato que ativar a proteção. Basta fornecer o novo ID de política na política de solicitação e enviar a solicitação.

Por exemplo: Para alterar a política de proteção do *testshare* do *schedule1* para *o schedule2,* forneça o *id agendado2* no órgão de solicitação.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Interromper a proteção, mas manter os dados existentes

Você pode remover a proteção em um compartilhamento de arquivos protegido, mas reter os dados já em backup. Para isso, remova a política no órgão de solicitação que você usou para ativar o[backup](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) e enviar a solicitação. Uma vez que a associação com a diretiva é removida, os backups não são mais acionados e nenhum novo ponto de recuperação é criado.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Resposta de exemplo

Parar a proteção para um compartilhamento de arquivos é uma operação assíncrona. A operação cria outra operação que precisa ser rastreada. Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada, e 200 quando essa operação é concluída.

Cabeçalho de resposta quando a operação é aceita com sucesso:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Em seguida, rastreie a operação resultante usando o cabeçalho de localização ou cabeçalho Azure-AsyncOperation com um comando GET:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Corpo da resposta

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Interromper a proteção e excluir dados

Para remover a proteção em um compartilhamento de arquivos protegido e excluir os dados de backup também, execute uma operação de exclusão conforme detalhado [aqui](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Os parâmetros {containerName} e {protectedItemName} estão definidos [aqui](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname).

O exemplo a seguir desencadeia uma operação para interromper a proteção para o compartilhamento de arquivos *testshare* protegido com *o azurefilesvault*.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Respostas

A proteção de exclusão é uma operação assíncrona. A operação cria outra operação que precisa ser rastreada separadamente.
Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada e 204 (NoContent) quando essa operação é concluída.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [solucionar problemas enquanto configura o backup para compartilhamentos do Arquivo Azure](troubleshoot-azure-files.md).