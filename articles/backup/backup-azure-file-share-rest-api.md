---
title: Faça backup das partes de arquivos do Azure com a API REST
description: Saiba como usar a API REST para fazer backup das ações de arquivos do Azure no Cofre de Serviços de Recuperação
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248093"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Compartilhamento de arquivos do Backup Azure usando o Backup do Azure via API de descanso

Este artigo descreve como fazer backup de um compartilhamento de arquivos do Azure usando o Backup do Azure via API REST.

Este artigo pressupõe que você já criou um cofre de serviços de recuperação e uma política para configurar backup para o compartilhamento de arquivos. Se você não tiver, consulte o [cofre de criação](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault) e crie tutoriais de API REST de [política](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) para criar novos cofres e políticas.

Para este artigo, usaremos os seguintes recursos:

- **RecoveryServicesVault**: *azurefilesvault*

- **Política:** *cronograma1*

- **Grupo de recursos**: *azurefiles*

- **Conta de armazenamento**: *testvault2*

- **Compartilhamento de arquivos**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Configure backup para um compartilhamento de arquivos Azure desprotegido usando a API REST

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Descubra contas de armazenamento com compartilhamentos de arquivos Azure desprotegidos

O cofre precisa descobrir todas as contas de armazenamento do Azure na assinatura com compartilhamentos de arquivos que podem ser copiados para o Cofre de Serviços de Recuperação. Isso é disparado usando a [operação de atualização](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). É uma *operação* POST assíncrona que garante que o cofre receba a lista mais recente de todos os compartilhamentos de arquivos Azure desprotegidos na assinatura atual e 'caches' deles. Uma vez que o compartilhamento de arquivos seja 'armazenado em cache', os serviços de recuperação podem acessar o compartilhamento de arquivos e protegê-lo.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

O POST `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}`tem `{fabricName}` , , e parâmetros. Em nosso exemplo, o valor para os diferentes parâmetros seria o seguinte:

- `{fabricName}`é *Azure*

- `{vaultName}`é *azurefilesvault*

- `{vaultresourceGroupName}`é *azurefiles*

- $filter=backupGerenciamentoType eq 'AzureStorage'

Uma vez que todos os parâmetros necessários são dados no URI, não há necessidade de um corpo de solicitação separado.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>Respostas

A operação “atualizar” é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada, e 200 (OK) quando essa operação é concluída.

##### <a name="example-responses"></a>Respostas de exemplo

Após a solicitação *POST* ser enviada, uma resposta 202 (Aceito) será retornada.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Rastreie a operação resultante usando o cabeçalho de “Localização” com um comando *GET* simples

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Uma vez que todas as contas do Azure Storage sejam descobertas, o comando GET retorna uma resposta de 200 (Sem Conteúdo). O cofre agora é capaz de descobrir qualquer conta de armazenamento com compartilhamentos de arquivos que podem ser copiados dentro da assinatura.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>Obtenha lista de contas de armazenamento que podem ser protegidas com o cofre dos Serviços de Recuperação

Para confirmar se o cache está feito, liste todas as contas de armazenamento protetoras a assinatura. Em seguida, localize a conta de armazenamento desejada na resposta. Isso é feito usando a operação [GET ProtectableContainers.](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list)

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

O URI *GET* tem todos os parâmetros necessários. Nenhum corpo da solicitação adicional é necessário.

Exemplo de corpo de resposta:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Uma vez que podemos localizar a conta de armazenamento *testvault2* no corpo de resposta com o nome amigável, a operação de atualização realizada acima foi bem sucedida. O cofre de serviços de recuperação agora pode descobrir com sucesso contas de armazenamento com compartilhamentos de arquivos desprotegidos na mesma assinatura.

### <a name="register-storage-account-with-recovery-services-vault"></a>Registre a conta de armazenamento no cofre dos Serviços de Recuperação

Esta etapa só é necessária se você não registrar a conta de armazenamento no cofre anteriormente. Você pode registrar o cofre através da [operação ProtectionContainers-Register](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Defina as variáveis para o URI da seguinte forma:

- {resourceGroupName} - *azurefiles*
- {fabricName} - *Azure*
- {vaultName} - *azurefilesvault*
- {containerName} - Este é o atributo de nome no corpo de resposta da operação GET ProtectableContainers.
   Em nosso exemplo, é *StorageContainer; Armazenamento; AzureFiles;testvault2*

>[!NOTE]
> Sempre pegue o atributo de nome da resposta e preencha-o nesta solicitação. Não codifique ou crie o formato de nome de contêiner. Se você criá-lo ou codificar-lo, a chamada aPI falhará se o formato de nome de contêiner for alterado no futuro.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

O órgão de solicitação de criação é o seguinte:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Para obter a lista completa de definições do órgão de solicitação e outros detalhes, consulte [ProtectionContainers-Register](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Trata-se de uma operação assíncrona e retorna duas respostas: "202 Aceito" quando a operação é aceita e "200 OK" quando a operação estiver concluída.  Para rastrear o status da operação, use o cabeçalho de localização para obter o status mais recente da operação.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Exemplo de corpo de resposta quando a operação é concluída:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Você pode verificar se o registro foi bem sucedido a partir do valor do parâmetro de status de *registro* no órgão de resposta. No nosso caso, ele mostra o status como registrado para *testvault2,* de modo que a operação de registro foi bem sucedida.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Indaga todos os compartilhamentos de arquivos desprotegidos em uma conta de armazenamento

Você pode perguntar sobre itens protegidos em uma conta de armazenamento usando a operação [Protection Containers-Inquire.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire) É uma operação assíncrona e os resultados devem ser rastreados usando o cabeçalho de localização.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Defina as variáveis para o URI acima da seguinte forma:

- {vaultName} - *azurefilesvault*
- {fabricName} - *Azure*
- {containerName}- Consulte o atributo nome no corpo de resposta da operação GET ProtectableContainers. Em nosso exemplo, é *StorageContainer; Armazenamento; AzureFiles;testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Uma vez que a solicitação é bem sucedida, ela retorna o código de status "OK"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Selecione o compartilhamento de arquivos que deseja fazer backup

Você pode listar todos os itens protegidos a assinatura e localizar o compartilhamento de arquivos desejado para fazer backup usando a operação [GET backupprotectableIts.](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Construa o URI da seguinte forma:

- {vaultName} - *azurefilesvault*
- {$filter} - *backupManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Exemplo de resposta:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

A resposta contém a lista de todos os compartilhamentos de arquivos desprotegidos e contém todas as informações exigidas pelo Azure Recovery Service para configurar o backup.

### <a name="enable-backup-for-the-file-share"></a>Habilite o backup para o compartilhamento de arquivos

Depois que o compartilhamento de arquivos relevante for "identificado" com o nome amigável, selecione a diretiva a proteger. Para saber mais sobre as políticas existentes no cofre, consulte a [API de lista de políticas](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Em seguida, selecione a [política relevante](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) referindo-se ao nome da política. Para criar políticas, veja o [tutorial de criação de políticas](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy).

A habilitação da proteção é uma operação *PUT* assíncrona que cria um "item protegido".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Defina as variáveis **de nome** de contêiner e de **nome protegido** usando o atributo ID no corpo de resposta da operação GET backupprotectableitits.

Em nosso exemplo, o ID do compartilhamento de arquivos que queremos proteger é:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} - *contêiner de armazenamento;armazenamento;azurefiles;testvault2*
- {protectedItemName} - *azurefileshare;testshare*

Ou você pode consultar o atributo **de nome** do recipiente de proteção e respostas de itens protegidos.

>[!NOTE]
>Sempre pegue o atributo de nome da resposta e preencha-o nesta solicitação. NÃO código rígido ou criar o formato de nome do contêiner ou o formato de nome do item protegido. Se você criá-lo ou codificar-lo, a chamada aPI falhará se o formato de nome do contêiner ou o formato do nome do item protegido foralterado no futuro.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Criar um corpo de solicitação:

O corpo da solicitação a seguir define as propriedades necessárias para criar um item protegido.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

O **sourceResourceId** é o **contêiner paiFabricID** em resposta aos itens protegidos por backup GET.

Exemplo de Resposta

A criação de um item protegido é uma operação assíncrona, que cria outra operação que precisa ser rastreada. Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada e 200 (OK) quando essa operação é concluída.

Depois de enviar a solicitação *PUT* para criação ou atualização de itens protegidos, a resposta inicial será 202 (Aceita) com um cabeçalho de localização.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Em seguida, rastreie a operação resultante usando o cabeçalho de localização ou cabeçalho Azure-AsyncOperation com um comando *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Depois que a operação for concluída, ele retornará 200 (OK) com o conteúdo do item protegido no corpo da resposta.

Corpo de resposta da amostra:

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

Isso confirma que a proteção está ativada para o compartilhamento de arquivos e o primeiro backup será acionado de acordo com o cronograma de políticas.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Acione um backup demanda para compartilhamento de arquivos

Uma vez que um compartilhamento de arquivos Do Zure é configurado para backup, os backups são executados de acordo com o cronograma de políticas. Você pode esperar pelo primeiro backup agendado ou disparar um backup sob demanda a qualquer momento.

Disparar um backup sob demanda é uma operação POST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} e {protectedItemName} são como construídos acima enquanto habilitam o backup. Para nosso exemplo, isso é traduzido como:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Criar o corpo da solicitação

Para disparar um backup sob demanda, a seguir estão os componentes do corpo da solicitação.

| Nome       | Type                       | Descrição                       |
| ---------- | -------------------------- | --------------------------------- |
| Propriedades | AzurefilesharebackupReques | BackupSolicitarrecursosPropriedades de recursos |

Para obter uma lista de definições de corpo da solicitação e outros detalhes, veja o [documento sobre como disparar backups de itens protegidos da API REST](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

Exemplo de solicitar corpo

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>Respostas

Disparar um backup sob demanda é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada e 200 (OK) quando essa operação é concluída.

### <a name="example-responses"></a>Respostas de exemplo

Depois de enviar a solicitação *POST* para um backup sob demanda, a resposta inicial é 202 (Aceito) com um cabeçalho de localização ou cabeçalho assíncrono do Azure.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Em seguida, rastreie a operação resultante usando o cabeçalho de localização ou cabeçalho Azure-AsyncOperation com um comando *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Depois que a operação for concluída, ela retornará 200 (OK) com a ID da tarefa de backup resultante no corpo da resposta.

#### <a name="sample-response-body"></a>Corpo da resposta de exemplo

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Como a tarefa de backup é uma operação longa, ela precisa ser rastreada conforme explicado [no documento sobre monitoramento de trabalhos usando a API REST](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [restaurar os compartilhamentos de arquivos do Azure usando a API rest](restore-azure-file-share-rest-api.md).
