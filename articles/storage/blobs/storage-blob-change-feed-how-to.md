---
title: Feed de alteração de processo no Azure Blob Storage (Preview) | Microsoft Docs
description: Saiba como processar logs de feed de alterações em um aplicativo cliente .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111860"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Alimentação de alteração de processo no Azure Blob Storage (Preview)

O feed de alterações fornece logs de transações de todas as alterações que ocorrem nas blobs e nos metadados blob em sua conta de armazenamento. Este artigo mostra como ler registros de feed de alterações usando a biblioteca do processador de feed de alteração de bolha.

Para saber mais sobre o feed de alterações, consulte [Alterar feed no Azure Blob Storage (Preview)](storage-blob-change-feed.md).

> [!NOTE]
> O feed de mudança está em pré-visualização pública, e está disponível nas **regiões oeste** e **oeste2.** Para saber mais sobre esse recurso, juntamente com problemas e limitações conhecidos, consulte [O suporte ao feed alterar no Azure Blob Storage](storage-blob-change-feed.md). A biblioteca do processador de feed de alterações está sujeita a alterações entre agora e quando esta biblioteca estiver geralmente disponível.

## <a name="get-the-blob-change-feed-processor-library"></a>Obtenha a biblioteca do processador de ração de alteração blob

1. No Visual Studio, `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` adicione a URL às fontes do pacote NuGet. 

   Para saber como, consulte [fontes de pacotes](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. No NuGet Package Manager, encontre o pacote **Microsoft.Azure.Storage.Changefeed** e instale-o no seu projeto. 

   Para saber como, consulte [Encontrar e instalar um pacote](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Conecte-se à conta de armazenamento

Analise a seqüência de conexões chamando o método [CloudStorageAccount.TryParse.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) 

Em seguida, crie um objeto que represente o Blob Storage em sua conta de armazenamento, chamando o método [CloudStorageAccount.CreateCloudBlobClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Inicialize a alimentação de mudança

Adicione as instruções a seguir usando a parte superior do seu arquivo de código. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Em seguida, crie uma instância da classe **ChangeFeed** chamando o método **GetContainerReference.** Passe em nome do recipiente de alimentação de mudança.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Leitura de registros

> [!NOTE]
> O feed de alterações é uma entidade imutável e somente leitura em sua conta de armazenamento. Qualquer número de aplicativos pode ler e processar o feed de alterações simultaneamente e independentemente à sua própria conveniência. Os registros não são removidos do feed de alterações quando um aplicativo os lê. O estado de leitura ou iteração de cada leitor consumidor é independente e mantido apenas pela sua aplicação.

A maneira mais simples de ler registros é criar uma instância da classe **ChangeFeedReader.** 

Este exemplo itera através de todos os registros no feed de alterações e, em seguida, imprime para o console alguns valores de cada registro. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Retomando registros de leitura de uma posição salva

Você pode optar por salvar sua posição de leitura no feed de alterações e retomar a iteração dos registros em um momento futuro. Você pode salvar o estado da sua iteração do feed de alterações a qualquer momento usando o método **ChangeFeedReader.SerializeState().** O estado é uma **string** e seu aplicativo pode salvar esse estado com base no design do seu aplicativo (Por exemplo: em um banco de dados ou em um arquivo).

```csharp
    string currentReadState = processor.SerializeState();
```

Você pode continuar iterando através de registros do último estado criando o **ChangeFeedReader** usando o método **CreateChangeFeedReaderFromPointerAsync.**

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Processamento de fluxo de registros

Você pode optar por processar os registros de feed de alteração à medida que eles chegam. Ver [especificações](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Leitura de registros dentro de um intervalo de tempo

O feed de alterações é organizado em segmentos de hora em hora com base no tempo do evento de mudança. Ver [especificações](storage-blob-change-feed.md#specifications). Você pode ler registros de segmentos de feed de alteração que se enquadram em um intervalo de tempo específico.

Este exemplo obtém os tempos de partida de todos os segmentos. Em seguida, ele itera através dessa lista até que o tempo de início esteja além do tempo do último segmento consumível ou além do tempo final da faixa desejada. 

### <a name="selecting-segments-for-a-time-range"></a>Selecionando segmentos para um intervalo de tempo

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Leitura de registros em um segmento

Você pode ler registros de segmentos individuais ou faixas de segmentos.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Ler registros a partir de um tempo

Você pode ler os registros da alimentação de mudança de um segmento inicial até o final. Semelhante aos registros de leitura dentro de um intervalo de tempo, você pode listar os segmentos e escolher um segmento para começar a iterar.

Este exemplo obtém o [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) do primeiro segmento a ser processado.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

Este exemplo processa os registros de feed de alteração a partir do [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) de um segmento inicial.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Um segmento do pode ter logs de feed de alteração em um ou mais *chunkFilePath*. No caso de vários *chunkFilePath,* o sistema dividiu internamente os registros em vários fragmentos para gerenciar a publicação de throughput. É garantido que cada partição do segmento conterá alterações para bolhas mutuamente exclusivas e pode ser processada de forma independente sem violar o pedido. Você pode usar a classe **ChangeFeedSegmentShardReader** para iterar através de registros no nível de fragmento, se isso for mais eficiente para o seu cenário.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os registros de feed de alterações. Consulte [o feed de alterações no Azure Blob Storage (Visualização)](storage-blob-change-feed.md)
