---
title: Como escolher entre a taxa de transferência provisionada e sem servidor no Azure Cosmos DB
description: Saiba mais sobre como escolher entre a taxa de transferência provisionada e sem servidor para sua carga de trabalho.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 0adb346a693beaa905438cfdc1249c1646c28811
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608705"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Como escolher entre taxa de transferência provisionada e sem servidor

Azure Cosmos DB está disponível em dois modos de capacidade diferentes: [taxa de transferência provisionada](set-throughput.md) e sem [servidor](serverless.md). Você pode executar exatamente as mesmas operações de banco de dados em ambos os modos, mas a maneira como você é cobrado por essas operações é radicalmente diferente. O vídeo a seguir explica as principais diferenças entre esses modos e como eles se encaixam em diferentes tipos de cargas de trabalho:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

> [!NOTE]
> No momento, há suporte para servidor somente na API do Azure Cosmos DB Core (SQL).

## <a name="detailed-comparison"></a>Comparação detalhada

| Critérios | Taxa de transferência provisionada | Sem servidor |
| --- | --- | --- |
| Status | Disponível para o público geral | Em versão prévia |
| Mais indicado para | Cargas de trabalho de missão crítica que exigem desempenho previsível | Cargas de trabalho não críticas de pequeno a médio porte com tráfego leve |
| Como ele funciona | Para cada um de seus contêineres, você provisiona uma quantidade de produtividade expressa em [unidades de solicitação](request-units.md) por segundo. A cada segundo, essa quantidade de unidades de solicitação está disponível para suas operações de banco de dados. A taxa de transferência provisionada pode ser atualizada manualmente ou ajustada automaticamente com o [dimensionamento automático](provision-throughput-autoscale.md). | Você executa as operações de banco de dados em seus contêineres sem precisar provisionar nenhuma capacidade. |
| Limitações por conta | Número máximo de regiões do Azure: ilimitado | Número máximo de regiões do Azure: 1 |
| Limitações por contêiner | Taxa de transferência máxima: ilimitada<br>Armazenamento máximo: ilimitado | Taxa de transferência máxima: 5.000 RU/s<br>Armazenamento máximo: 50 GB |
| Desempenho | 99,99% a 99,999% de disponibilidade coberta pelo SLA<br>Latência de 10 ms de < para leituras de ponto e gravações cobertas pelo SLA<br>99,99% de produtividade garantida coberta pelo SLA | 99,9% a 99,99% de disponibilidade coberta pelo SLA<br>< latência de 10 ms para leituras de ponto e < 30 ms para gravações cobertas pelo SLO<br>95% de intermitência cobertas pelo SLO |
| Modelo de cobrança | A cobrança das RU/s provisionadas é feita por hora, independentemente de quantas RUs foram consumidas. | A cobrança é feita por hora para a quantidade de RUs consumida pelas operações de banco de dados. |

> [!IMPORTANT]
> Algumas das limitações sem servidor podem ser facilitou ou removidas quando o servidor não estiver disponível de forma geral e **seus comentários** nos ajudarão a decidir! Alcance e conte-nos mais sobre sua experiência sem servidor: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="burstability-and-expected-consumption"></a>Intermitência e consumo esperado

Em algumas situações, pode ficar claro se a taxa de transferência provisionada ou sem servidor deve ser escolhida para uma determinada carga de trabalho. Para ajudar com essa decisão, você pode estimar:

- O requisito de **intermitência** da carga de trabalho, é qual é a quantidade máxima de RUs que você pode precisar consumir em um segundo
- O **consumo geral esperado**, que é o número total de RUs que você pode consumir durante um mês (você pode estimar isso com a ajuda da tabela mostrada [aqui](plan-manage-costs.md#estimating-serverless-costs))

Se sua carga de trabalho exigir a intermitência acima de 5.000 RU por segundo, a taxa de transferência provisionada deverá ser escolhida porque os contêineres sem servidor não podem ultrapassar esse limite. Caso contrário, você pode comparar o custo de ambos os modos com base no consumo esperado.

**Exemplo 1**: espera-se que uma carga de trabalho seja estourada para um máximo de 10.000 ru/s e consuma um total de 20 milhões RUs em um mês.

- Somente o modo de taxa de transferência provisionado pode fornecer uma taxa de transferência de 10.000 RU/s

**Exemplo 2**: espera-se que uma carga de trabalho seja estourada para um máximo de 500 ru/s e consuma um total de 20 milhões RUs em um mês.

- No modo de taxa de transferência provisionado, você provisionia um contêiner com 500 RU/s por um custo mensal de: $0.08 * 5 * 730 = **$29.20**
- No modo sem servidor, você pagaria pelo RUs consumido: $0.25 * 20 = **$5**

**Exemplo 3**: espera-se que uma carga de trabalho seja estourada para um máximo de 500 ru/s e consuma um total de 250 milhões RUs em um mês.

- No modo de taxa de transferência provisionado, você provisionia um contêiner com 500 RU/s por um custo mensal de: $0.08 * 5 * 730 = **$29.20**
- No modo sem servidor, você pagaria pelo RUs consumido: $0.25 * 250 = **$62.50**

(esses exemplos não são contábeis para o custo de armazenamento, que é o mesmo entre os dois modos)

> [!NOTE]
> Os custos mostrados no exemplo anterior são apenas para fins de demonstração. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as informações de preços mais recentes.

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre como [provisionar a taxa de transferência no Azure Cosmos DB](set-throughput.md)
- Leia mais sobre o [Azure Cosmos DB sem servidor](serverless.md)
- Familiarize-se com o conceito de [unidades de solicitação](request-units.md)
