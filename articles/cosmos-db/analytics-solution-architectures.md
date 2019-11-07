---
title: Soluções que usam análises distribuídas globalmente no Azure Cosmos DB.
description: Saiba mais sobre as soluções que podem ser criadas usando a análise distribuída globalmente no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681712"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Soluções que usam análises distribuídas globalmente no Azure Cosmos DB

Este artigo descreve as soluções que podem ser criadas usando a análise distribuída globalmente no Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Varejo e bens de consumo

Você pode usar o suporte ao Spark no Azure Cosmos DB para fornecer recomendações e ofertas em tempo real. Você pode ajudar os clientes a descobrir os itens de que eles precisam com personalização e recomendações de produtos em tempo real.

* Você pode usar o suporte interno ao Machine Learning fornecido pelo tempo de execução do Apache Spark para gerar recomendações em tempo real em todos os catálogos de produtos.

* Você pode fazer a mineração com um clique de dados de fluxo, de compra e do cliente para fornecer recomendações direcionadas que promovem valor por todo o tempo de vida.

* Usando o recurso de distribuição global do Azure Cosmos DB, grandes volumes de dados do produto que são distribuídos entre regiões podem ser analisados em milissegundos.

* Você pode obter insights rapidamente para os usuários e dados distribuídos geograficamente. Você pode melhorar a taxa de conversão das promoções oferecendo o anúncio certo ao usuário correto, no momento exato.

* Você pode aproveitar a funcionalidade de streaming interna do Spark para enriquecer dados ao vivo, combinando-os com os dados estáticos do cliente. Dessa forma, você pode fornecer anúncios mais personalizados e direcionados em tempo real, dentro do contexto daquilo que os clientes estão fazendo.

A imagem a seguir mostra como o suporte ao Spark no Azure Cosmos DB é usado para otimizar o preço e as promoções:

![Suporte ao Spark no Azure Cosmos DB para otimizar o preço e as promoções](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


A imagem a seguir mostra como o suporte ao Spark no Azure Cosmos DB é usado no mecanismo de recomendação em tempo real:

![Suporte ao Spark no Azure Cosmos DB no mecanismo de recomendação em tempo real](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Manufatura e IoT

A plataforma de análise interna do Azure Cosmos DB permite que você habilite a análise em tempo real de dados de IoT de milhões de dispositivos em escala global. Você pode realizar inovações modernas como prever padrões meteorológicos, análise preditiva e otimizações de energia.

* Usando o Azure Cosmos DB, você pode fazer a mineração de dados como métricas de ativos em tempo real e fatores meteorológicos e, em seguida, aplicar análise de grade inteligente para otimizar o desempenho de dispositivos conectados no campo. A Análise de grade inteligente é a chave para controlar os custos operacionais, melhorar a confiabilidade da grade e fornecer serviços de energia personalizados aos consumidores.

A imagem a seguir mostra como o suporte ao Spark no Azure Cosmos DB é usado para ler métricas de dispositivos IoT e aplicar a análise de grade inteligente:

![Suporte ao Spark no Azure Cosmos DB para ler métricas de dispositivos IoT](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Manutenção preditiva

* A manutenção de ativos, como compactadores usados em pequenas plataformas de perfuração, para uso em plataformas de águas profundas é um esforço complexo. Esses ativos estão localizados em todo o mundo e geram petabytes de dados. Usando Azure Cosmos DB, você pode criar um pipeline de dados preditivos de ponta a ponta que usa o streaming do Spark para processar grandes quantidades de dados de telemetria de sensor, peças de ativos armazenados e mapeamentos de sensor.

* Você pode criar e implantar modelos de machine learning para prever falhas de ativos antes que elas ocorram e emitir ordens de trabalho de manutenção em caráter preventivo.

A imagem a seguir mostra como o suporte ao Spark no Azure Cosmos DB é usado para criar um sistema de manutenção preditiva:

![Suporte ao Spark no Azure Cosmos DB para criar sistema de manutenção preditiva](./media/analytics-solution-architectures/predictive-maintenance-system.png)

A imagem a seguir mostra como o suporte ao Spark no Azure Cosmos DB é usado para criar um sistema de diagnóstico de veículo em tempo real:

![Suporte ao Spark no Azure Cosmos DB para criar sistema de diagnóstico de veículo em tempo real](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Jogos

* Com o suporte interno ao Spark, o Azure Cosmos DB permite que você crie, dimensione e implante com facilidade modelos de análise avançada e modelos de machine learning em questão de minutos, a fim de criar a melhor experiência de jogos possível.

* Você pode analisar o player, a compra e os dados comportamentais para criar ofertas personalizadas relevantes a fim de obter elevadas taxas de conversão.

* Usando o aprendizado de máquina do Spark, você pode analisar e obter insights sobre dados de telemetria de jogos. Você pode diagnosticar e evitar lentidão no tempo de carregamento e problemas no jogo.

A imagem a seguir mostra como o suporte ao Spark no Azure Cosmos DB é usado na análise de jogos:

![Suporte ao Spark no Azure Cosmos DB para análise de jogos](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os benefícios do Azure Cosmos DB, consulte o artigo de [visão geral](introduction.md).
* [Introdução à API do Azure Cosmos DB para MongoDB](mongodb-introduction.md)
* [Introdução à API do Cassandra no Azure Cosmos DB](cassandra-introduction.md)
* [Introdução à API do Gremlin no Azure Cosmos DB](graph-introduction.md)
* [Introdução à API de Tabela no Azure Cosmos DB](table-introduction.md)
