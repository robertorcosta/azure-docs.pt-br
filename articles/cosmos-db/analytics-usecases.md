---
title: Casos de uso para análises incorporadas com o Azure Cosmos DB.
description: Aprenda a usar análises incorporadas com o Azure Cosmos DB em diferentes casos de uso.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72757070"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Casos de uso para análises incorporadas com o Azure Cosmos DB

Os seguintes casos de uso podem ser obtidos usando as análises incorporadas com o Apache Spark no Azure Cosmos DB:

## <a name="htap-scenarios"></a>Cenários htap

Análises incorporadas no Azure Cosmos DB podem ser usadas para:

* Detecte fraudedurante o processamento de transações.
* Forneça recomendações aos compradores enquanto eles navegam em uma loja de Comércio Eletrônico.
* Alerte os operadores para a iminente falha de uma peça crítica de equipamento de fabricação.
* Crie insights rápidos e acionáveis incorporando análises em tempo real diretamente sobre dados operacionais.

O Azure Cosmos DB suporta cenários de Processamento Transacional/Analítico Híbrido (HTAP) usando o Apache Spark nativo embutido. O Azure Cosmos DB remove a separação operacional e analítica que vem com os sistemas tradicionais.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Lago de dados distribuído globalmente sem exigir qualquer ETL

Com o Apache Spark embutido nativamente, o Azure Cosmos DB fornece uma maneira rápida, simples e escalável de construir um lago de dados distribuído globalmente que fornece uma única imagem do sistema. Os dados multimodelodistribuídos globalmente eliminam a necessidade de investir em gasodutos e escalas eescalas eescalas caras demanda, revolucionando a forma como as equipes de dados analisam seus conjuntos de dados.

## <a name="time-series-analytics-over-historic-data"></a>Análise de séries tempois sobre dados históricos

Em alguns casos, você pode precisar responder perguntas com base em dados como em um ponto específico no tempo sobre eventos concluídos no passado. Por exemplo, para obter a contagem de status de atividade de CRM em uma determinada data. Se você publicasse o relatório há uma semana, a contagem dos status seria de acordo com os status de cada atividade naquele momento. A execução do mesmo relatório hoje lhe dará a contagem das atividades cujos status são como são hoje, que podem ter mudado desde a semana passada, à medida que passam pelo seu ciclo de vida de aberto a fechar. Então, você precisa informar sobre o instantâneo em cada estágio do ciclo de vida do caso.

Em cenários tradicionais de data warehouse, o conceito de snapshot não é possível porque os data warehouses não foram projetados para incorporá-lo e os dados apenas fornecem uma visão atual do que está acontecendo. Com o Azure Cosmos DB, os usuários têm a possibilidade de implementar o conceito de viagem no tempo, podendo consultar e executar análises sobre os dados retrospectivamente e perguntar como os dados olharam para um ponto específico do tempo na história. Isso significa que os usuários podem visualizar facilmente as visualizações atuais e históricas dos dados e executar análises sobre ele.

## <a name="globally-distributed-machine-learning-and-ai"></a>Aprendizado de máquina distribuído globalmente e IA

À medida que as empresas enfrentam volumes de dados em rápido crescimento e uma variedade crescente de tipos e formatos de dados, a capacidade de obter insights mais profundos e precisos torna-se quase impossível em escala petabyte em todo o mundo. Com o Apache Spark embutido nativamente, o Azure Cosmos DB fornece uma plataforma de análise distribuída globalmente que oferece uma extensa biblioteca de algoritmos de aprendizagem de máquina. Você pode usar notebooks Jupyter interativos para construir e treinar modelos e recursos de gerenciamento de clusters. Esses recursos permitem que você prover clusters Spark altamente sintonizados e auto-elásticos demanda.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Deep Learning em dados distribuídos globalmente

O deep learning é a maneira ideal de fornecer soluções de análise preditiva de big data à medida que o volume de dados e a complexidade continuam a crescer. Com o aprendizado profundo, as empresas podem aproveitar o poder de dados não estruturados e semiestruturados para fornecer casos de uso que aproveitam técnicas como IA, processamento de linguagem natural e muito mais.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Emissão de relatórios (integração com Power Apps, Power BI)

O Power BI fornece visualizações interativas com recursos de inteligência de negócios de autoatendimento, permitindo que os usuários finais criem relatórios e dashboards por conta própria. Usando o conector Spark incorporado, você pode conectar o Power BI Desktop aos clusters Apache Spark no Azure Cosmos DB. Este conector permite que você use consulta direta para descarregar o processamento para o Apache Spark no Azure Cosmos DB, o que é ótimo quando você tem uma enorme quantidade de dados que você não deseja carregar no Power BI ou quando você deseja realizar uma análise quase em tempo real.

## <a name="iot-analytics-at-global-scale"></a>Análise de IoT em escala global

Os dados gerados a partir do aumento dos sensores de rede trazem uma visibilidade sem precedentes em sistemas e processos anteriormente opacos. A chave é encontrar insights acionáveis nesta torrente de informações, independentemente de onde os dispositivos IoT estão distribuídos em todo o mundo. O Azure Cosmos DB permite que as empresas de IOT analisem dados de sensores de alta velocidade e séries tempontos em tempo real em qualquer lugar do mundo. Ele permite que você aproveite o verdadeiro valor de um mundo interconectado para oferecer melhores experiências ao cliente, eficiênciaoperacional e novas oportunidades de receita.

## <a name="stream-processing-and-event-analytics"></a>Processamento de fluxos e análise de eventos 

De arquivos de log a dados de sensores, as empresas têm cada vez mais a necessidade de lidar com "fluxos" de dados. Esses dados chegam em um fluxo constante, muitas vezes a partir de múltiplas fontes simultaneamente. Embora seja viável armazenar esses fluxos de dados em disco e analisá-los retrospectivamente, às vezes pode ser sensato ou importante processar e agir sobre os dados à medida que chegam. Por exemplo, fluxos de dados relacionados a transações financeiras podem ser processados em tempo real para identificar e recusar transações potencialmente fraudulentas.

## <a name="interactive-analytics"></a>Análise interativa

Além de executar consultas pré-definidas para criar painéis estáticos para vendas, produtividade ou preços de ações, você pode querer explorar os dados de forma interativa. Análises interativas permitem que você faça perguntas, veja os resultados, altere a pergunta inicial com base na resposta ou aprofunde-se nos resultados. O Apache Spark no Azure Cosmos DB suporta consultas interativas, respondendo e adaptando-se rapidamente.

## <a name="data-exploration-using-jupyter-notebooks"></a>Exploração de dados usando notebooks Jupyter

Quando você tem um novo conjunto de dados, antes de mergulhar em modelos e testes em execução, você precisa inspecionar seus dados. Em outras palavras, você precisa realizar a análise exploratória dos dados. A exploração de dados pode informar várias decisões. Por exemplo, você pode encontrar detalhes como os métodos apropriados para usar em seus dados, se os dados atendem a certas suposições de modelagem, se os dados devem ser limpos, reestruturados etc. Usando os notebooks Jupyter nativos do Azure Cosmos DB e apache spark, você pode fazer análises exploratórias rápidas e eficazes sobre dados transacionais e analíticos.

## <a name="next-steps"></a>Próximas etapas

Para começar com esses casos de uso, acesse os seguintes artigos:

* [Notebooks Jupyter incorporados no Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Como habilitar notebooks para contas do Azure Cosmos](enable-notebooks.md)
* [Crie um notebook para analisar e visualizar dados](create-notebook-visualize-data.md)