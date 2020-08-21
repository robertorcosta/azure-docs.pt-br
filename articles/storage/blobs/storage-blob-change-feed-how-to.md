---
title: Processar feed de alterações no armazenamento de BLOBs do Azure (visualização) | Microsoft Docs
description: Saiba como processar logs do feed de alterações em um aplicativo cliente .NET
author: normesta
ms.author: normesta
ms.date: 06/18/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: dedf1174e00f5bb75822fb720a592af86121ec2d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691421"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Processar feed de alterações no armazenamento de BLOBs do Azure (visualização)

O feed de alterações fornece logs de transações de todas as alterações que ocorrem nos BLOBs e nos metadados de BLOB em sua conta de armazenamento. Este artigo mostra como ler registros do feed de alterações usando a biblioteca do processador do feed de alterações de BLOB.

Para saber mais sobre o feed de alterações, consulte [alterar feed no armazenamento de BLOBs do Azure (versão prévia)](storage-blob-change-feed.md).

> [!NOTE]
> O feed de alterações está em visualização pública e está disponível nas regiões **westcentralus** e **westus2** . Para saber mais sobre esse recurso juntamente com limitações e problemas conhecidos, consulte [suporte ao feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md). A biblioteca do processador do feed de alterações está sujeita a alterações entre agora e quando essa biblioteca se torna disponível de forma geral.

## <a name="get-the-blob-change-feed-processor-library"></a>Obter a biblioteca do processador do feed de alterações de BLOB

1. Abra uma janela de comando (por exemplo: Windows PowerShell).
2. No diretório do projeto, instale o pacote NuGet **Azure. Storage. BLOBs. changefeed** .

```console
dotnet add package Azure.Storage.Blobs.ChangeFeed --source https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json --version 12.0.0-dev.20200604.2
```
## <a name="read-records"></a>Ler registros

> [!NOTE]
> O feed de alterações é uma entidade imutável e somente leitura em sua conta de armazenamento. Qualquer número de aplicativos pode ler e processar o feed de alterações simultaneamente e de forma independente a sua própria conveniência. Os registros não são removidos do feed de alterações quando um aplicativo os lê. O estado de leitura ou de iteração de cada leitor de consumo é independente e mantido somente pelo seu aplicativo.

Este exemplo itera por todos os registros no feed de alterações, adiciona-os a uma lista e, em seguida, retorna essa lista ao chamador.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Este exemplo imprime no console alguns valores de cada registro na lista. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Retomar a leitura de registros de uma posição salva

Você pode optar por salvar sua posição de leitura no feed de alterações e, em seguida, retomar a iteração pelos registros em um momento futuro. Você pode salvar a posição de leitura obtendo o cursor do feed de alterações. O cursor é uma **cadeia de caracteres** e seu aplicativo pode salvar essa cadeia de caracteres de qualquer forma que faça sentido para o design do seu aplicativo (por exemplo: para um arquivo ou banco de dados).

Este exemplo itera por todos os registros no feed de alterações, adiciona-os a uma lista e salva o cursor. A lista e o cursor são retornados para o chamador. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Fluxo de processamento de registros

Você pode optar por processar registros do feed de alterações à medida que eles chegam. Consulte [especificações](storage-blob-change-feed.md#specifications). Recomendamos que você pesquise por alterações a cada hora.

Este exemplo sonda periodicamente as alterações.  Se houver registros de alteração, esse código processará esses registros e salvará o cursor do feed de alterações. Dessa forma, se o processo for interrompido e, em seguida, iniciado novamente, o aplicativo poderá usar o cursor para retomar os registros de processamento onde ele parou pela última vez. Este exemplo salva o cursor em um arquivo de configuração de aplicativo local, mas seu aplicativo pode salvá-lo em qualquer forma que faça mais sentido para seu cenário. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Lendo registros dentro de um intervalo de tempo

Você pode ler os registros que se enquadram em um intervalo de tempo específico. Este exemplo itera por meio de todos os registros no feed de alterações que estão entre 3:00 PM em março de 2 2017 e 2:00 AM em outubro de 7 2019, adiciona-os a uma lista e, em seguida, retorna essa lista ao chamador.

### <a name="selecting-segments-for-a-time-range"></a>Selecionando segmentos para um intervalo de tempo

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2017, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 10, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

A hora de início que você fornece é arredondada para a hora mais próxima e a hora de término é arredondada para a hora mais próxima. É possível que os usuários possam ver eventos que ocorreram antes da hora de início e após a hora de término. Também é possível que alguns eventos que ocorrem entre a hora de início e de término não apareçam. Isso ocorre porque os eventos podem ser registrados durante a hora anterior à hora de início ou durante a hora após a hora de término.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os logs do feed de alterações. Consulte o [feed de alterações no armazenamento de BLOBs do Azure (visualização)](storage-blob-change-feed.md)
