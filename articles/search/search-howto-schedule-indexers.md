---
title: Execução do indexador de agendamento
titleSuffix: Azure Cognitive Search
description: Agende os indexadores de pesquisa cognitiva do Azure para indexar o conteúdo periodicamente ou em horários específicos.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112951"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Como agendar indexadores na Pesquisa Cognitiva do Azure

Um indexador normalmente é executado uma vez, imediatamente após sua criação. Você pode executá-lo novamente demanda usando o portal, a API REST ou o .NET SDK. Você também pode configurar um indexador para executar periodicamente em um cronograma.

Algumas situações em que o agendamento do indexador é útil:

* Os dados de origem serão alterados ao longo do tempo, e você deseja que os indexadores de pesquisa cognitiva do Azure processem automaticamente os dados alterados.
* O índice será preenchido a partir de várias fontes de dados e você deseja garantir que os indexadores sejam executados em momentos diferentes para reduzir conflitos.
* Os dados de origem são muito grandes e você deseja espalhar o processamento do indexador ao longo do tempo. Para obter mais informações sobre como indexar grandes volumes de dados, consulte [Como indexar grandes conjuntos de dados no Azure Cognitive Search](search-howto-large-index.md).

O agendador é uma característica incorporada do Azure Cognitive Search. Você não pode usar um agendador externo para controlar indexadores de pesquisa.

## <a name="define-schedule-properties"></a>Definir propriedades de agendamento

Um cronograma de indexador tem duas propriedades:
* **Intervalo**, que define a quantidade de tempo entre execuções de indexadores programados. O menor intervalo permitido é de 5 minutos, e o maior é de 24 horas.
* **Start Time (UTC)**, que indica a primeira vez em que o indexador deve ser executado.

Você pode especificar um cronograma ao primeiro criar o indexador ou atualizando as propriedades do indexador mais tarde. Os horários do indexador podem ser definidos usando o [portal,](#portal)a [API REST](#restApi)ou o [.NET SDK](#dotNetSdk).

Só é possível ocorrer uma execução de um indexador por vez. Se um indexador já estiver sendo executado quando sua próxima execução estiver programada, essa execução será adiada para a próxima hora agendada.

Vamos considerar um exemplo para tornar isso mais concreto. Suponha que configuremos um cronograma de indexador com um **intervalo** de hora em hora e um horário de **início** de 1º de junho de 2019 às 8:00:00 UTC. Aqui está o que pode acontecer quando uma corrida de indexador leva mais de uma hora:

* A primeira execução do indexador começa em 1º de junho de 2019 às 8:00 UTC. Vamos supor que essa execução demore 20 minutos (ou menos de uma hora).
* A segunda execução começa em 1º de junho de 2019 às 9:00 UTC. Suponha que essa execução leve 70 minutos - mais de uma hora - e não seja concluída até as 10:10 UTC.
* A terceira execução está programada para começar às 10:00 UTC, mas nesse momento a execução anterior ainda está em execução. Esta execução programada é então ignorada. A próxima execução do indexador não começará até as 11:00 UTC.

> [!NOTE]
> Se um indexador é definido para um determinado cronograma, mas falha repetidamente no mesmo documento repetidamente cada vez que ele é executado, o indexador começará a funcionar em um intervalo menos freqüente (até o máximo de pelo menos uma vez a cada 24 horas) até que ele faça progressos com sucesso Novamente.  Se você acredita que corrigiu qualquer problema que estava fazendo com que o indexador ficasse preso em um determinado ponto, você pode realizar uma corrida demanda do indexador, e se isso fizer progressos com sucesso, o indexador voltará ao seu intervalo de horário definido novamente.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Programação no portal

O assistente de dados de importação no portal permite definir o cronograma de um indexador no momento da criação. A configuração padrão de agendamento é **hourly**, o que significa que o indexador é executado uma vez depois de criado, e é executado novamente a cada hora depois.

Você pode alterar a configuração De agendamento para **Uma vez** se você não quiser que o indexador seja executado automaticamente ou para **o Diário** para funcionar uma vez por dia. Defina-o **como Personalizado** se quiser especificar um intervalo diferente ou um horário de início futuro específico.

Quando você define o cronograma como **Personalizado,** os campos aparecem para permitir que você especifique o **Intervalo** e o **Tempo de Início (UTC).** O menor intervalo de tempo permitido é de 5 minutos, e o maior é de 1440 minutos (24 horas).

   ![Definindo o cronograma do indexador no assistente de dados de importação](media/search-howto-schedule-indexers/schedule-import-data.png "Definindo o cronograma do indexador no assistente de dados de importação")

Depois que um indexador for criado, você pode alterar as configurações de cronograma usando o painel Editar do indexador. Os campos Agendar são os mesmos do assistente Dados de importação.

   ![Definindo o cronograma no painel 'Edição do indexador'](media/search-howto-schedule-indexers/schedule-edit.png "Definindo o cronograma no painel 'Edição do indexador'")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Agendar usando APIs REST

Você pode definir o cronograma de um indexador usando a API REST. Para isso, inclua a propriedade **de agendamento** ao criar ou atualizar o indexador. O exemplo abaixo mostra uma solicitação PUT para atualizar um indexador existente:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O parâmetro **interval** é necessário. O intervalo refere-se ao tempo entre o início de duas execuções consecutivas do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

O **startTime** opcional indica quando as execuções programadas devem começar. Se for omitido, a hora UTC atual será usada. Esse tempo pode ser no passado, nesse caso a primeira execução é agendada como se o indexador estivesse funcionando continuamente desde o **início**original .

Você também pode executar um indexador demanda a qualquer momento usando a chamada Run Indexer. Para obter mais informações sobre a execução de indexadores e definir agendas de indexadores, consulte [Run Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [Get Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer)e [Update Indexer](https://docs.microsoft.com/rest/api/searchservice/update-indexer) na Referência de API REST.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Agendar usando o .NET SDK

Você pode definir o cronograma de um indexador usando o Azure Cognitive Search .NET SDK. Para isso, inclua a propriedade **agendada** ao criar ou atualizar um Indexador.

O exemplo C# a seguir cria um indexador, usando uma fonte de dados e índice predefinidos, e define seu cronograma para ser executado uma vez por dia a partir de 30 minutos a partir de agora:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Se o parâmetro de **agendamento** for omitido, o indexador só funcionará imediatamente após sua criação.

O parâmetro **startTime** pode ser definido como um tempo no passado. Nesse caso, a primeira execução é agendada como se o indexador estivesse funcionando continuamente desde o início dado tempo de **partida**.

O cronograma é definido usando a classe [IndexandSchedule.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) O **construtor IndexingSchedule** requer um parâmetro **de intervalo** especificado usando um objeto **TimeSpan.** O menor valor de intervalo permitido é de 5 minutos, e o maior é de 24 horas. O segundo parâmetro **startTime,** especificado como um objeto **DateTimeOffset,** é opcional.

O .NET SDK permite controlar operações de indexador usando a classe [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) e sua propriedade [Indexers,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) que implementa métodos a partir da interface **IIndexersOperations.** 

Você pode executar um indexador demanda a qualquer momento usando um dos métodos [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)ou [RunWithHttpMessagesAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)

Para obter mais informações sobre como criar, atualizar e executar indexadores, consulte [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
