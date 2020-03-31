---
title: Agregação geofencing e geoespacial com o Azure Stream Analytics
description: Este artigo descreve como usar o Azure Stream Analytics para agregação geofencing e geoespacial.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 5a3aa3786469c3df37b53cb82bdd396871689297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443635"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Cenários de agregação geofencing e geoespacial com o Azure Stream Analytics

Com funções geoespaciais incorporadas, você pode usar o Azure Stream Analytics para criar aplicativos para cenários como gerenciamento de frota, compartilhamento de carona, carros conectados e rastreamento de ativos.

## <a name="geofencing"></a>Delimitação geográfica

O Azure Stream Analytics suporta computação de geofencing de baixa latência em tempo real na nuvem e no tempo de execução do IoT Edge.

### <a name="geofencing-scenario"></a>Cenário geofencing

Uma empresa de fabricação precisa rastrear ativos em seus edifícios. Eles equiparam todos os dispositivos com um GPS e querem receber notificações se um dispositivo sair de uma determinada área.

Os dados de referência utilizados neste exemplo têm as informações de geocerca para os edifícios e os dispositivos que são permitidos em cada um dos edifícios. Lembre-se que os dados de referência podem ser estáticos ou mudar lentamente. Dados de referência estática são usados para este cenário. Um fluxo de dados emite continuamente o ID do dispositivo e sua posição atual.

### <a name="define-geofences-in-reference-data"></a>Defina geocercas em dados de referência

Uma geocerca pode ser definida usando um objeto GeoJSON. Para trabalhos com compatibilidade versão 1.2 ou superior, as geocercas também podem `NVARCHAR(MAX)`ser definidas usando texto bem conhecido (WKT) como . WKT é um padrão de Consórcio Geoespacial Aberto (OGC) que é usado para representar dados espaciais em um formato textual.

As funções geoespaciais incorporadas podem usar geocercas definidas para descobrir se um elemento está dentro ou fora de um polígono específico de geocerca.

A tabela a seguir é um exemplo de dados de referência de geofence que podem ser armazenados no armazenamento blob do Azure ou em uma tabela SQL do Azure. Cada site é representado por um polígono geoespacial, e cada dispositivo está associado a uma id do site permitida.

|Siteid|SiteName|Geocerca|Dispositivo permitido|
|------|--------|--------|---------------|
|1|"Redmond Building 41"|"POLYGON((-122.1337357922017 47.63782998329432,-122.13373042778369 47.63763479357305,-122.133467571300234 2022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806,-122.1336124105880 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Redmond Building 40"|"POLYGON((-122.1336154507967 47.6366745947009,-122.133610863767 47.636483015064535,-122.13349206918201 47.636447 9400347675,-122.13349743360004 47.6363637292773,-122.13372810357532 47.6363372927773.122.13333346993335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Redmond Building 22"|"POLYGON((-122.13611660248233 47.63758544698554,-122.13635263668764 47.6374083293018,-122.1362238084293333333 3603619712,-122.1362238984293 47.63717699101473,-122.1358161950726644 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|"C"|

### <a name="generate-alerts-with-geofence"></a>Gerar alertas com geocerca

Os dispositivos podem emitir seu ID `DeviceStreamInput`e localização a cada minuto através de um fluxo chamado . A tabela a seguir é um fluxo de entrada.

|DeviceID|Geoposition|
|--------|-----------|
|"A"|"PONTO(-122.1329234159497 47.636318374032726)"|
|"B"|"PONTO(-122.13338475554553 47.63743531308874)"|
|"C"|"PONTO(-122.13354001095752 47.63627622505007)"|

Você pode escrever uma consulta que une o fluxo do dispositivo com os dados de referência de geofence e gera um alerta toda vez que um dispositivo está fora de um edifício permitido.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

A imagem a seguir representa as geocercas. Você pode ver onde os dispositivos estão de acordo com a entrada de dados do fluxo.

![Construção de geocercas](./media/geospatial-scenarios/building-geofences.png)

O dispositivo "C" está localizado dentro do ID do edifício 2, o que não é permitido de acordo com os dados de referência. Este dispositivo deve estar localizado dentro do prédio ID 3. Executar este trabalho vai gerar um alerta para esta violação específica.

### <a name="site-with-multiple-allowed-devices"></a>Site com vários dispositivos permitidos

Se um site permitir vários dispositivos, um conjunto de `AllowedDeviceID` IDs de dispositivo pode ser `WHERE` definido e uma função definida pelo usuário pode ser usada na cláusula para verificar se o ID do dispositivo de fluxo corresponde a algum ID do dispositivo nessa lista. Para obter mais informações, consulte o tutorial [Javascript UDF](stream-analytics-javascript-user-defined-functions.md) para trabalhos na nuvem e o tutorial [C# UDF](stream-analytics-edge-csharp-udf.md) para trabalhos de borda.

## <a name="geospatial-aggregation"></a>Agregação geoespacial

O Azure Stream Analytics suporta baixa agregação geoespacial em tempo real de latência na nuvem e no tempo de execução do IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Cenário de agregação geoespacial

Uma empresa de táxi quer construir um aplicativo em tempo real para orientar seus taxistas que procuram carona em direção às áreas das cidades que atualmente experimentam maior demanda.

A empresa armazena regiões lógicas da cidade como dados de referência. Cada região é definida por um RegionID, RegionName e Geofence.

### <a name="define-the-geofences"></a>Defina as geocercas

A tabela a seguir é um exemplo de dados de referência de geofence que podem ser armazenados no armazenamento blob do Azure ou em uma tabela SQL do Azure. Cada região é representada por um polígono geoespacial, que é usado para se correlacionar com as solicitações provenientes de dados de streaming.

Estes polígonos são apenas para referência e não representam separações físicas ou lógicas reais da cidade.

|RegionID|RegionName|Geocerca|
|--------|----------|--------|
|1|"SoHo"|"POLYGON((-74.00279525078275 40.7283625216264,-74.0054774597 9765 40.72192915863244,-74.00125029839018 40.71893680218 994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.7283625216264) )"|
|2|"Chinatown"|"POLYGON((-73.9971236714876 40.71281582671333,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452 3559088633,-73.98976368961189 40.7154823078944,-73.99551444573982 40.713337446783735,-73.99480624449 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"POLYGON((-74.01091641815208 40.72583120006787,-74.01338405044578 40.7143658362705,-74.0137059155275777 17702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018.-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Dados agregados ao longo de uma janela de tempo

A tabela a seguir contém dados de streaming de "passeios".

|UserID|Localização de Deslocamento|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"PONTO(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.7137884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.71643989862424)"|"POINT(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"POINT(-74.00741090068288 40.7161562675086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

A consulta a seguir se junta ao fluxo do dispositivo com os dados de referência de geofence e calcula o número de solicitações por região em uma janela de tempo de 15 minutos a cada minuto.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Esta consulta produz uma contagem de solicitações a cada minuto para os últimos 15 minutos por cada região dentro da cidade. Essas informações podem ser exibidas facilmente pelo painel Power BI, ou podem ser transmitidas a todos os drivers como mensagens de texto SMS através da integração com serviços como funções do Azure.

A imagem abaixo ilustra a saída da consulta para o painel Power BI. 

![Saída de resultado no painel Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Próximas etapas

* [Introdução às funções geoespaciais do Stream Analytics](stream-analytics-geospatial-functions.md)
* [Funções Geoespaciais (Análise de Fluxo Azure)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
