---
title: Como escolher a oferta de taxa de transferência certa no Azure Cosmos DB
description: Saiba mais sobre como escolher entre a taxa de transferência padrão (manual) e a taxa de transferência provisionada de dimensionamento automático para sua carga de trabalho.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: fbe17d75ad809c54939624b1409e281b2f62a037
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605208"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Como escolher entre a taxa de transferência padrão (manual) e a de dimensionamento automático provisionada 

O Azure Cosmos DB dá suporte a dois tipos ou ofertas de taxa de transferência provisionada: padrão (manual) e de dimensionamento automático. Os dois tipos de taxa de transferência são adequados para cargas de trabalho de missão crítica que exigem alto desempenho e escala, além disso, são apoiados pelos mesmos SLAs do Azure Cosmos DB no que diz respeito a taxa de transferência, disponibilidade, latência e consistência.

Este artigo descreve como escolher entre a taxa de transferência padrão (manual) e a de dimensionamento automático provisionada para sua carga de trabalho. 

## <a name="overview-of-provisioned-throughput-types"></a>Visão geral dos tipos de taxa de transferência provisionados
Antes de se aprofundar sobre a diferença entre a taxa padrão (manual) e a de dimensionamento automático, é importante entender primeiro como a taxa de transferência provisionada funciona no Azure Cosmos DB. 

Ao usar a taxa de transferência provisionada, você define a taxa de transferência, medida em unidades de solicitação por segundo (RU/s), que são necessárias para sua carga de trabalho. O serviço provisiona a capacidade necessária para dar suporte aos requisitos de taxa de transferência. As operações de banco de dados no serviço, tal como leituras, gravações e consultas, consomem alguma quantidade de unidades de solicitação (RUs). Saiba mais sobre [unidades de solicitação](request-units.md).

A tabela a seguir mostra uma comparação de alto nível entre o padrão (manual) e o dimensionamento automático.

|Descrição|Padrão (manual)|Autoscale|
|-------------|------|-------|
|Mais indicado para|Cargas de trabalho com tráfego constante ou previsível|Cargas de trabalho com tráfego variável ou imprevisível. Consulte os [casos de uso do dimensionamento automático](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Como ele funciona|Você provisiona uma quantidade definida de RU/s `T`, que é estática ao longo do tempo, a menos que você altere essa quantidade manualmente. A cada segundo, você pode usar uma taxa de transferência de até `T` RU/s. <br/><br/>Por exemplo, se você definir o padrão (manual) em 400 RU/s, a taxa de transferência permanecerá em 400 RU/s.|Você define o valor maior ou o máximo de RU/s `Tmax` que o sistema não deve exceder. O sistema dimensiona automaticamente a taxa de transferência `T` de modo que `0.1* Tmax <= T <= Tmax`. <br/><br/>Por exemplo, se você definir o dimensionamento automático a 4.000 RU/s, o sistema será dimensionado entre 400 e 4.000 RU/s.|
|Quando usar isso|Você quer gerenciar manualmente sua capacidade de taxa de transferência (RU/s) e dimensionar por conta própria.<br/><br/>Você tem uma utilização alta e consistente de RU/s provisionadas. De todas as horas em um mês, se você definir um valor de RU/s provisionadas `T` e usar 66% da quantidade total de horas ou mais, estima-se que você economizará com RU/s provisionadas padrão (manual).<br/><br/>Isso é baseado em uma comparação entre a configuração `T` em modo padrão (manual) e a mesma quantidade `Tmax` em dimensionamento automático. |Você deseja que o Azure Cosmos DB gerencie sua capacidade de taxa de transferência (RU/s) e escala com base no uso.<br/><br/>O seu uso de RU/s é variável ou difícil de prever. Do total de horas em um mês, se você definir o valor máximo de RU/s de dimensionamento automático `Tmax` e usar a quantidade total `Tmax` em 66% das horas ou menos, estima-se que você economizará com o dimensionamento automático.<br/><br/>Isso se baseia em uma comparação entre configurar o dimensionamento automático `Tmax` e o mesmo valor `T` na taxa de transferência padrão (manual).|
|Modelo de cobrança|A cobrança das RU/s provisionadas é feita por hora, independentemente de quantas RUs foram consumidas.<br/><br/>Exemplo: <li>Provisionar 400 RU/s</li><li>Hora 1: nenhuma solicitação</li><li>Hora 2: 400 RU/s de solicitações</li><br/><br/>Para as horas 1 e 2, você será cobrado 400 RU/s por ambas as horas nas tarifas [padrão (manual)](https://azure.microsoft.com/pricing/details/cosmos-db/).|A cobrança é feita por hora, para o valor mais alto de RU/s que o sistema escalou durante essa hora. <br/><br/>Exemplo: <li>Provisionar dimensionamento automático máximo de 4.000 RU/s (escalas entre 400 e 4.000 RU/s)</li><li>Hora 1: o sistema escalou verticalmente para o valor mais alto de 3.500 RU/s</li><li>Hora 2: o sistema foi reduzido para o mínimo de 400 RU/s (sempre 10% do `Tmax`), devido à ausência de uso</li><br/><br/>Você será cobrado por 3.500 RU/s na hora 1 e 400 RU/s na hora 2 nas [taxas de transferência provisionadas pelo dimensionamento automático](https://azure.microsoft.com/pricing/details/cosmos-db/). A taxa de dimensionamento automático por RU/s é 1,5 * a taxa padrão (manual).
|O que acontece se você exceder as RU/s provisionadas|As RU/s permanecem estáticas no que está provisionado. Todas as solicitações que consumirem além das RUs provisionadas em um segundo serão limitadas por taxa, com uma resposta que recomenda um tempo de espera antes de tentar novamente. Você pode aumentar ou diminuir as RU/s manualmente, se necessário.| O sistema dimensionará as RU/s até o valor máximo de dimensionamento automático. Todas as solicitações que consumirem para além do valor máximo de dimensionamento automático de RU/s em um segundo serão limitadas por taxa, com uma resposta que recomenda um tempo de espera antes de tentar novamente.|

## <a name="understand-your-traffic-patterns"></a>Entenda seus padrões de tráfego

### <a name="new-applications"></a>Novos aplicativos ###

Se você estiver criando um novo aplicativo e ainda não souber seu padrão de tráfego, poderá iniciar com as RU/s do ponto de entrada (ou com o mínimo de RU/s) para evitar excesso de provisionamento no início. Ou, se você tiver um aplicativo pequeno que não precise de alta escala, poderá provisionar apenas o mínimo de RU/s do ponto de entrada para otimizar o custo. Para aplicativos pequenos com um tráfego de baixo esperado, você também pode considerar o modo de capacidade sem [servidor](throughput-serverless.md) .

Se você planeja usar o padrão (manual) ou o dimensionamento automático, veja o que você deve considerar:

Se você provisionar RU/s padrão (manual) no ponto de entrada de 400 RU/s, não será possível consumir acima de 400 RU/s, a menos que você altere manualmente a taxa de transferência. Você será cobrado, por hora, 400 RU/s na taxa de taxa de transferência provisionada padrão (manual).

Se você provisionar a taxa de transferência de dimensionamento automático no ponto de entrada com no máximo 4.000 RU/s, o recurso será dimensionado entre 400 e 4.000 RU/s. Como o valor de cobrança da taxa de transferência de dimensionamento automático por RU/s equivale a 1,5x o valor da taxa padrão (manual), por horas em que o sistema foi reduzido para o mínimo de 400 RU/s, sua fatura será mais alta do que se você provisionar 400 RU/s manualmente. No entanto, com o dimensionamento automático, em qualquer momento, se o tráfego do aplicativo atingir o pico, você poderá consumir até 4.000 RU/s sem a necessidade de qualquer ação por parte do usuário. Em geral, você deve avaliar o benefício de poder consumir até o máximo de RU/s em qualquer momento com a taxa de 1,5x de dimensionamento automático.

Use a [calculadora de capacidade](estimate-ru-with-capacity-planner.md) do Azure Cosmos DB para estimar os requisitos de taxa de transferência. 

### <a name="existing-applications"></a>Aplicativos existentes ###

Se você tiver um aplicativo existente usando a taxa de transferência padrão (manual) provisionada, poderá usar as [Métricas do Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md) para determinar se o seu padrão de tráfego é adequado para o dimensionamento automático. 

Primeiro, localize a [métrica de consumo da unidade de solicitação normalizada](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) do seu banco de dados ou contêiner. A utilização normalizada é uma medida do quanto você está usando atualmente sua taxa de transferência padrão (manual) provisionada. Quanto mais próximo de 100% estiver o número, mais você estará usando as RU/s provisionadas. [Saiba mais](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) sobre a métrica.

Em seguida, determine como a utilização normalizada varia ao longo do tempo. Se você observar que sua utilização normalizada é variável ou imprevisível, considere habilitar o dimensionamento automático em seu banco de dados ou contêiner. Por outro lado, se for constante e previsível, considere mantê-la na taxa de transferência padrão (manual) provisionada. 

> [!TIP]
> Com a taxa de transferência padrão (manual), você pode usar a métrica de utilização normalizada para estimar as RU/s reais que você pode usar se alternar para o dimensionamento automático. Multiplique a utilização normalizada em um momento determinado pelas RU/s padrão (manual) configuradas atualmente. Por exemplo, se você tiver provisionado 5.000 RU/s e a utilização normalizada for de 90%, o uso de RU/s será de 0,9 * 5.000 = 4.500 RU/s. Se você observar que o seu padrão de tráfego é variável, mas está acima ou abaixo do provisionado, poderá habilitar o dimensionamento automático e, em seguida, alterar a configuração de RU/s de dimensionamento automático de forma adequada.

## <a name="measure-and-monitor-your-usage"></a>Medição e monitoramento de uso
Ao longo do tempo, depois de escolher o tipo de taxa de transferência, monitore seu aplicativo e faça ajustes conforme necessário. 

Ao usar o dimensionamento automático, use o Azure Monitor para ver o máximo de RU/s de dimensionamento automático provisionado (**Taxa de transferência máxima de dimensionamento automático**) e a RU/s em que o sistema está atualmente dimensionado (**Taxa de transferência provisionada**). Abaixo está um exemplo de carga de trabalho variável ou imprevisível usando o dimensionamento automático. Observação quando não há tráfego, o sistema dimensiona as RU/s para o mínimo de 10% do máximo de RU/s, que neste caso é de 5.000 RU/s e 50.000 RU/s, respectivamente. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Exemplo de carga de trabalho usando o dimensionamento automático":::

> [!NOTE]
> Quando você usa a taxa de transferência padrão (manual) provisionada, a métrica de **taxa de transferência provisionada** refere-se ao que você definiu como um usuário. Quando você usa a taxa de transferência de dimensionamento automático, essa métrica se refere às RU/s nas quais o sistema está atualmente dimensionado.

## <a name="next-steps"></a>Próximas etapas
* Use a [calculadora de RU](https://cosmos.azure.com/capacitycalculator/) para estimar a taxa de transferência para novas cargas de trabalho.
* Use o [Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) para monitorar suas cargas de trabalho existentes.
* Saiba como [provisionar a taxa de transferência de dimensionamento automático em um banco de dados ou contêiner do Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Examine as [Perguntas frequentes sobre dimensionamento automático](autoscale-faq.md).