---
title: Exportar e importar registros do Azure Notification Hubs a granel | Microsoft Docs
description: Saiba como usar o suporte em massa do Notification Hubs para realizar um grande número de operações em um hub de notificação ou para exportar todos os registros.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 8eb03a42f38c0cc7fe82eda6a81d1c8c1213ec74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212389"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Exportação e importação de registros do Azure Notification Hubs em massa
Existem cenários em que é necessário criar ou modificar grandes números de registros em um hub de notificação. Alguns desses cenários são atualizações de marcas após computações em lote ou a migração de uma implementação de push existente para usar Hubs de Notificação.

Este artigo explica como realizar um grande número de operações em um hub de notificação, ou exportar todos os registros, em massa.

## <a name="high-level-flow"></a>Fluxo de alto nível
O suporte a operações em lote destina-se a trabalhos de longa duração que envolvem milhões de registros. Para obter essa escala, o suporte em lote usa o Azure Storage para armazenar detalhes do trabalho e a saída. Para operações de atualização em massa, o usuário precisa criar um arquivo em um contêiner de blobs, cujo conteúdo é a lista de operações de atualização de registros. Ao iniciar o trabalho, o usuário fornece uma URL para o blob de entrada, junto com uma URL para um diretório de saída (também contido em um contêiner de blobs). Depois que o trabalho for iniciado, o usuário pode verificar o status consultando um local de URL fornecido no início do trabalho. Um trabalho específico só pode realizar operações de um tipo específico (cria, atualiza ou exclui). As operações de exportação são executadas de maneira semelhante.

## <a name="import"></a>Importar

### <a name="set-up"></a>Configuração
Esta seção pressupõe que você tem as seguintes entidades:

- Um hub de notificação provisionado.
- Um contêiner de blobs do Armazenamento do Azure.
- Referências ao [pacote NuGet de armazenamento azure](https://www.nuget.org/packages/windowsazure.storage/) e [ao pacote NuGet do Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Criar um arquivo de entrada e armazená-lo em um blob
Um arquivo de entrada contém uma lista de registros serializados em XML, sendo um por linha. Usando o SDK do Azure, o exemplo de código a seguir mostra como serializar os registros e carregá-los no contêiner de blobs.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> O código anterior serializa os registros na memória e, em seguida, carrega o fluxo inteiro em um blob. Se você carregou um arquivo de mais do que apenas alguns megabytes, consulte a orientação do Azure blob sobre como executar essas etapas; por exemplo, [bolhas de bloco](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Criar tokens de URL
Uma vez que o arquivo de entrada seja carregado, gere as URLs para fornecer ao seu centro de notificação tanto para o arquivo de entrada quanto para o diretório de saída. Você pode usar dois recipientes blob diferentes para entrada e saída.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Enviar o trabalho
Com os dois URLs de entrada e saída, agora você pode iniciar o trabalho em lote.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Além dos URLs de entrada e saída, este exemplo cria um `NotificationHubJob` objeto que contém um `JobType` objeto, que pode ser um dos seguintes tipos:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Uma vez que a chamada é concluída, o trabalho é continuado pelo centro de notificação, e você pode verificar seu status com a chamada para [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Quando o trabalho for concluído, você poderá verificar os resultados examinando os seguintes arquivos em seu diretório de saída:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Esses arquivos contêm a lista das operações de seu lote que foram bem-sucedidas e falharam. O formato `.cvs`do arquivo é , no qual cada linha tem o número de linha do arquivo de entrada original, e a saída da operação (geralmente a descrição de registro criada ou atualizada).

### <a name="full-sample-code"></a>Código de amostra completo
O código de amostra a seguir importa registros em um centro de notificação.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Exportação
A exportação do registro é semelhante à importação, com as seguintes diferenças:

- Você precisa somente da URL de saída.
- Você cria um NotificationHubJob do tipo ExportRegistrations.

### <a name="sample-code-snippet"></a>Trecho do código de amostra
Aqui está um trecho de código de exemplo para exportação de registros em Java:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as inscrições, consulte os seguintes artigos:

- [Gestão de registro](notification-hubs-push-notification-registration-management.md)
- [Marcas de registros](notification-hubs-tags-segment-push-message.md)
- [Registros de modelo](notification-hubs-templates-cross-platform-push-messages.md)
