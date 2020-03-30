---
title: Melhores práticas para o Serviço de Rota do Azure Maps | Mapas do Microsoft Azure
description: Aprenda a rodar de forma eficiente usando o Serviço de Rota do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335411"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Melhores práticas para o serviço Azure Maps Route

As APIs de direção e matriz de rota no Serviço de [Rota](https://docs.microsoft.com/rest/api/maps/route) do Azure Maps podem ser usadas para calcular os tempos estimados de chegada (ETAs) para cada rota solicitada. As APIs de rota consideram fatores como informações de tráfego em tempo real e dados históricos de tráfego, como as velocidades típicas das estradas no dia solicitado da semana e a hora do dia. As APIs retornam as rotas mais curtas ou mais rápidas disponíveis para vários destinos por vez em seqüência ou em ordem otimizada, com base no tempo ou distância. Os usuários também podem solicitar rotas especializadas e detalhes para caminhantes, ciclistas e veículos comerciais, como caminhões. Neste artigo, compartilharemos as melhores práticas para chamar o Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route), e você aprenderá como:

* Escolha entre as APIs de direção de rota e a API de roteamento de matriz
* Solicite tempos de viagem históricos e previstos, com base em dados de tráfego em tempo real e histórico
* Solicitar detalhes da rota, como tempo e distância, para toda a rota e cada trecho da rota
* Solicitação de rota para um veículo comercial, como um caminhão
* Solicite informações de tráfego ao longo de uma rota, como congestionamentos e informações de pedágio
* Solicite uma rota que consiste em uma ou mais paradas (waypoints)
* Otimize uma rota de uma ou mais paradas para obter a melhor ordem para visitar cada parada (waypoint)
* Otimize rotas alternativas usando pontos de apoio. Por exemplo, oferecer rotas alternativas que passem por uma estação de carregamento de veículos elétricos.
* Use o [Serviço de Rota](https://docs.microsoft.com/rest/api/maps/route) com o Web SDK do Azure Maps

## <a name="prerequisites"></a>Pré-requisitos

Para fazer chamadas para as APIs do Azure Maps, você precisa de uma conta do Azure Maps e uma chave. Para obter mais informações, consulte [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e obter uma chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account). A chave principal também é conhecida como a chave de assinatura principal, ou chave de assinatura.

Para obter informações sobre autenticação no Azure Maps, consulte [Gerenciar autenticação no Azure Maps](./how-to-manage-authentication.md). E para obter mais informações sobre a cobertura do Serviço de Rota, consulte a [Cobertura de Roteamento](routing-coverage.md).

Este artigo usa o [aplicativo Carteiro](https://www.postman.com/downloads/) para criar chamadas REST, mas você pode escolher qualquer ambiente de desenvolvimento de API.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Escolha entre direções de rota e roteamento de matriz

As APIs de direção de rota retornam instruções, incluindo o tempo de viagem e as coordenadas para um caminho de rota. A API Route Matrix permite calcular o tempo de viagem e as distâncias para um conjunto de rotas definidas por origem e locais de destino. Para cada origem dada, a API Matrix calcula o custo (tempo de viagem e distância) do roteamento dessa origem para cada destino dado. Todas essas APIs permitem especificar parâmetros como o horário de partida desejado, os horários de chegada e o tipo de veículo, como carro ou caminhão. Todos eles usam dados de tráfego em tempo real ou preditivos de acordo para retornar as rotas mais ideais.

Considere chamar APIs de direções de rota se o seu cenário for para:

* Solicite a rota de condução mais curta ou mais rápida entre dois ou mais locais conhecidos, para obter horários de chegada precisos para seus veículos de entrega.
* Solicite orientação detalhada da rota, incluindo geometria da rota, para visualizar rotas no mapa
* Dada uma lista de locais do cliente, calcule a rota mais curta possível para visitar cada local do cliente e retornar à origem. Este cenário é comumente conhecido como o problema do vendedor viajante. Você pode passar até 150 waypoints (stops) em uma solicitação.
* Envie lotes de consultas para a API de lote de direções de rota usando apenas uma única chamada de API.

Considere chamar a API de roteamento de Matriz se o seu cenário for para:

* Calcule o tempo de viagem ou distância entre um conjunto de origens e destinos. Por exemplo, você tem 12 motoristas e você precisa encontrar o motorista mais próximo disponível para pegar a entrega de comida do restaurante.
* Classifique rotas potenciais por sua distância ou tempo de viagem real. A API Matrix retorna apenas tempos de viagem e distâncias para cada combinação de origem e destino.
* Dados de cluster com base no tempo de viagem ou distâncias. Por exemplo, sua empresa tem 50 funcionários, encontre todos os funcionários que vivem dentro de 20 minutos drive time a partir de seu escritório.

Aqui está uma comparação para mostrar algumas capacidades das APIs de Direção de Rota e Matriz:

| Azure Maps API | Número máximo de consultas na solicitação | Evite áreas | Roteamento de caminhões e veículos elétricos | waypoints e otimização de vendedores itinerantes | Pontos de apoio |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Obter Trajeto de Rota | 1 | | X | X | |
| Instruções da rota do post | 1 | X | X | X | X |
| Lote de direções da rota do post | 700 | | X | X | |
| Matrix de rota POST | 700 | | X | | |

Para saber mais sobre os recursos de roteamento de veículos elétricos, consulte nosso tutorial sobre como [rodar veículos elétricos usando notebooks Azure com Python](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Solicite dados históricos e em tempo real

Por padrão, o serviço Route assume que o modo de viagem é um carro e a hora de partida é agora. Ele retorna a rota com base em condições de tráfego em tempo real, a menos que uma solicitação de cálculo de rota especifique o contrário. Restrições de tráfego dependentes de tempo fixo, como 'Curvas à esquerda não são permitidas entre 16:00 e 18:00) são capturadas e serão consideradas pelo motor de roteamento. Fechamentos de estradas, como obras rodoviárias, serão considerados a menos que você solicite especificamente uma rota que ignore o tráfego ao vivo atual. Para ignorar o tráfego `traffic` `false` atual, defina-o em sua solicitação de API.

O valor de cálculo de rota **travelTimeInSeconds** inclui o atraso devido ao tráfego. É gerado por aproveitar os dados atuais e históricos do tempo de viagem, quando a hora da partida é definida para agora. Se o horário de partida for definido no futuro, o retorno das APIs prevê tempos de viagem com base em dados históricos.

Se você incluir o **parâmetro computeTravelTimeFor=all** em sua solicitação, então o elemento resumo na resposta terá os seguintes campos adicionais, incluindo condições históricas de tráfego:

| Elemento | Descrição|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Tempo estimado de viagem calculado como se não houvesse atrasos na via por causa das condições de tráfego, por exemplo, por causa do congestionamento |
| históricoTrafficTravelTimeInSeconds | Tempo estimado de viagem calculado usando dados históricos de tráfego dependentes do tempo |
| liveTrafficIncidentsTravelTimeInSeconds | Tempo estimado de viagem calculado usando dados de velocidade em tempo real |

As próximas seções demonstram como fazer chamadas para as APIs de rota usando os parâmetros discutidos.

### <a name="sample-query"></a>Exemplo de consulta

No primeiro exemplo abaixo, o horário de partida é definido para o futuro, no momento da escrita.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

A resposta contém um elemento sumário, como o abaixo. Como o horário de partida é definido para o futuro, o valor **trafficDelayInSeconds** é zero. O valor **travelTimeInSeconds** é calculado usando dados históricos de tráfego dependentes do tempo. Então, neste caso, o valor **travelTimeInSeconds** é igual ao valor **históricoTrafficTravelTimeInSeconds.**

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Exemplo de consulta

No segundo exemplo abaixo, temos um pedido de roteamento em tempo real, onde a hora da partida é agora. Não está explicitamente especificado na URL porque é o valor padrão.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

A resposta contém um resumo como mostrado abaixo. Devido aos congestionamentos, o valor **de trafficDelaysInSeconds** é maior que zero. Também é maior do que **o históricoTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Solicitar detalhes da rota e da perna

Por padrão, o serviço Route retornará uma matriz de coordenadas. A resposta conterá as coordenadas que compõem `points`o caminho em uma lista nomeada . A resposta da rota também inclui a distância do início da rota e o tempo estimado decorrido. Esses valores podem ser usados para calcular a velocidade média de toda a rota.

A imagem a `points` seguir mostra o elemento.

<center>

![lista de pontos](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

Expanda `point` o elemento para ver a lista de coordenadas do caminho:

<center>

![lista de pontos](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

As APIs de direções de rota suportam diferentes formatos de instruções que podem ser usadas especificando o parâmetro **instructionsType.** Para formatar instruções para facilitar o processamento do computador, use **instruçõesType=coded**. Use **instruçõesType=tagged** para exibir instruções como texto para o usuário. Além disso, as instruções podem ser formatadas como texto onde alguns elementos das instruções são marcados, e a instrução é apresentada com formatação especial. Para obter mais informações, consulte a [lista de tipos de instrução suportados](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype).

Quando as instruções são solicitadas, `guidance`a resposta retorna um novo elemento chamado . O `guidance` elemento contém duas informações: direções turn-by-turn e instruções resumidas.

<center>

![Tipo de instruções](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

O `instructions` elemento mantém as direções turn-by-turn para a viagem, e as `instructionGroups` instruções resumidas. Cada resumo de instruções abrange um segmento da viagem que pode cobrir várias estradas. As APIs podem retornar detalhes para seções de uma rota. como, como, a faixa de coordenadas de um engarrafamento ou a velocidade atual do tráfego.

<center>

![Vire por turno instruções](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![Instruções resumidas](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>Solicite uma rota para um veículo comercial

As APIs de roteamento de mapas do Azure suportam roteamento de veículos comerciais, cobrindo o roteamento de caminhões comerciais. As APIs consideram limites especificados. Como, a altura e o peso do veículo, e se o veículo está carregando carga perigosa. Por exemplo, se um veículo está carregando inflamável, o motor de roteamento evita certos túneis que estão perto de áreas residenciais.

### <a name="sample-query"></a>Exemplo de consulta

A solicitação de amostra abaixo consulta uma rota para um caminhão comercial. O caminhão está carregando material de resíduoperigoso classe 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A API rota retorna direções que acomodam as dimensões do caminhão e os resíduos perigosos. Você pode ler as instruções `guidance` de rota expandindo o elemento.

<center>

![Caminhão com hazwaste classe 1](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>Exemplo de consulta

Alterar a Classe Hazmat dos EUA, a partir da consulta acima, resultará em uma rota diferente para acomodar essa mudança.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A resposta abaixo é para um caminhão que transporta um material perigoso classe 9, que é menos perigoso do que um material perigoso classe 1. Quando você `guidance` expande o elemento para ler as direções, você notará que as direções não são as mesmas. Há mais instruções de rota para o caminhão que transporta material perigoso classe 1.

<center>

![Caminhão com hazwaste classe 9](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>Solicite informações de tráfego ao longo de uma rota

Com as APIs de direção de rota do Azure Maps, os desenvolvedores podem solicitar detalhes para cada tipo de seção, incluindo o `sectionType` parâmetro na solicitação. Por exemplo, você pode solicitar as informações de velocidade para cada segmento de engarrafamento. Consulte a [lista de valores da chave sectionType](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) para saber mais sobre os vários detalhes que você pode solicitar.

### <a name="sample-query"></a>Exemplo de consulta

A consulta a `sectionType` seguir `traffic`define o : Ele solicita as seções que contêm informações de tráfego de Seattle para San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

A resposta contém as seções adequadas para o tráfego ao longo das coordenadas dadas.

<center>

![seções de tráfego](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

Esta opção pode ser usada para colorir as seções ao renderizar o mapa, como na imagem abaixo: 

<center>

![seções de tráfego](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Calcular e otimizar uma rota multi-stop

Atualmente, o Azure Maps oferece duas formas de otimização de rotas:

* Otimizações com base no tipo de rota solicitado, sem alterar a ordem dos waypoints. Você pode encontrar os [tipos de rota suportados aqui](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* Otimização de vendedor itinerante, que muda a ordem dos waypoints para obter a melhor ordem para visitar cada parada

Para roteamento multi-stop, até 150 waypoints podem ser especificados em uma única solicitação de rota. Os locais de coordenadas de partida e término podem ser os mesmos, como seria o caso de uma ida e volta. Mas você precisa fornecer pelo menos um ponto de passagem adicional para fazer o cálculo da rota. Os pontos de passagem podem ser adicionados à consulta entre as coordenadas de origem e destino.

Se você quiser otimizar a melhor ordem para visitar os pontos de passagem dado, então você precisa especificar **computeBestOrder=true**. Esse cenário também é conhecido como o problema de otimização do vendedor itinerante.

### <a name="sample-query"></a>Exemplo de consulta

A consulta a seguir solicita o caminho `computeBestOrder` para seis `false`pontos de passagem, com o parâmetro definido como . É também o valor padrão `computeBestOrder` para o parâmetro.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A resposta descreve o comprimento do caminho de 140.851 metros, e que levaria 9.991 segundos para percorrer esse caminho.

<center>

![Resposta não otimizada](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

A imagem abaixo ilustra o caminho resultante desta consulta. Este caminho é um caminho possível. Não é o caminho ideal baseado no tempo ou distância.

<center>

![Imagem não otimizada](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

Esta ordem de waypoint é: 0, 1, 2, 3, 4, 5 e 6.

### <a name="sample-query"></a>Exemplo de consulta

A consulta a seguir solicita o caminho para os mesmos seis pontos de passagem, como na amostra acima. Desta vez, `computeBestOrder` o parâmetro `true` definido para (a otimização do vendedor viajante).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A resposta descreve o comprimento do caminho de 91.814 metros, e que levaria 7.797 segundos para percorrer esse caminho. A distância de viagem e o tempo de viagem são ambos mais baixos aqui porque a API devolveu a rota otimizada.

<center>

![Resposta não otimizada](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

A imagem abaixo ilustra o caminho resultante desta consulta.

<center>

![Imagem não otimizada](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

A rota ideal tem a seguinte ordem de waypoint: 0, 5, 1, 2, 4, 3 e 6.

>[!TIP]
> As informações otimizadas da ordem waypoint do serviço de roteamento fornecem um conjunto de índices. Estes excluem a origem e os índices de destino. Você precisa incrementar esses valores por 1 para explicar a origem. Em seguida, adicione seu destino ao final para obter a lista completa de waypoint encomendada.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Calcular e viés rotas alternativas usando pontos de apoio

Você pode ter situações em que deseja reconstruir uma rota para calcular rotas zero ou mais alternativas para uma rota de referência. Por exemplo, você pode querer mostrar aos clientes rotas alternativas que passam pela sua loja de varejo. Neste caso, você precisa influenciar um local usando pontos de apoio. Aqui estão os passos para influenciar um local:

1. Calcule uma rota como está e obtenha o caminho a partir da resposta da rota
2. Use o caminho de rota para encontrar os locais desejados ao longo ou perto do caminho da rota. Por exemplo, você pode usar a [API do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) Maps Point of Interest ou consultar seus próprios dados em seu banco de dados.  
3. Encomendar os locais com base na distância do início da rota
4. Adicione esses locais como pontos de suporte em uma nova solicitação de rota para a [API Post Route Directions](https://docs.microsoft.com/rest/api/maps/route/postroutedirections). Para saber mais sobre os pontos de suporte, consulte a documentação da [API Post Route Directions](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints). 

Ao chamar a [API de Direções de Rota de Post,](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)você pode definir o tempo mínimo de desvio ou as restrições de distância, juntamente com os pontos de suporte. Use esses parâmetros se quiser oferecer rotas alternativas, mas também deseja limitar o tempo de viagem. Quando essas restrições forem utilizadas, as rotas alternativas seguirão a rota de referência a partir do ponto de origem para o tempo ou distância dado. Em outras palavras, as outras rotas divergem da rota de referência pelas restrições dadas.

A imagem abaixo é um exemplo de renderização de rotas alternativas com limites de desvio especificados para a hora e a distância.

<center>

![Rotas alternativas](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>Use o serviço de roteamento em um aplicativo web

O Web SDK do Azure Maps fornece um [módulo de serviço](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest). Este módulo é uma biblioteca auxiliar que facilita o uso das APIs Azure Maps REST em aplicativos Web ou Node.js, usando JavaScript ou TypeScript. O módulo Serviço pode ser usado para renderizar as rotas retornadas no mapa. O módulo determina automaticamente qual API usar com solicitações GET e POST.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte:

> [!div class="nextstepaction"]
> [Serviço de rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Como usar o módulo Serviço](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Mostrar rota no mapa](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Pacote NPM do Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )
