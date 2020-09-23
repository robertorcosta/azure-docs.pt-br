---
title: Começar a explorar a demonstração do Assistente de Métricas
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a interface Web do Assistente de Métricas usando a demonstração que fornecemos
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: fbc73e20b8cc2baa1cc5c5a5b2f674fb1b2dde84
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943245"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Início Rápido: Explorar a demonstração do Assistente de Métricas usando dados de exemplo

> [!Note]
> A demonstração do Assistente de Métricas é somente leitura e você não poderá integrar seus dados. Para usar o serviço em seus dados, primeiro é necessário [criar um recurso do Assistente de Métricas](web-portal.md).

Use este artigo para começar a explorar rapidamente os principais recursos do Assistente de Métricas. Fornecemos um site de demonstração usando dados de exemplo e configurações predefinidas, para que você se familiarize com o portal da Web com todos os recursos.

Clique [neste link](https://aka.ms/MetricsAdvisor/Demo) para acessar o site de demonstração.

## <a name="view-the-available-sample-data"></a>Exibir os dados de exemplo disponíveis

Depois de entrar no site de demonstração, você verá uma página de **Feed de dados**. Um feed de dados é um grupo lógico de dados de série temporal, que é consultado de sua fonte de dados. Para obter mais informações sobre os termos e conceitos usados no Assistente de Métricas, confira o [Glossário](../glossary.md). 

Há vários feeds de dados listados, que são ingeridos de diferentes tipos de fontes de dados, como o Banco de dados SQL do Azure ou a Tabela do Azure. Cada um deles usa definições de configuração ligeiramente diferentes para se conectar aos armazenamentos de dados associados.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Lista de dados de exemplo" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>Explorar as configurações do feed de dados

Clique no feed de dados *Amostra – Custo/Receita – Cidade/Categoria*. Você verá várias seções de detalhes do feed:

* Nome do feed de dados e status de ingestão.
* Uma lista de métricas consultadas da fonte de dados. Por exemplo, *custo* e *receita*. 
* Histórico de alertas para quando o feed de dados ficar indisponível. 
* Logs de quando o feed de dados foi atualizado.   
* Informações e configurações do feed de dados.

:::image type="content" source="../media/data-feed-view.png" alt-text="Exibição do feed de dados" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Exibir visualizações e configurações de série temporal

Clique na métrica *custo* no feed de dados *Amostra – Custo/Receita – Cidade/Categoria*. Você verá a série temporal associada segmentada por dimensões, com visualizações de acordo com os dados históricos da métrica. A faixa azul em volta dos dados da métrica representa o intervalo de valores esperados dos modelos de machine learning do Assistente de Métricas. Os pontos que ficarem fora dessa faixa serão marcados como pontos vermelhos, que são as anomalias detectadas. 

:::image type="content" source="../media/series-visualization.png" alt-text="Visualização em série" lightbox="../media/series-visualization.png":::

A detecção de anomalias é configurável ajustando as **configurações de detecção** no lado esquerdo da página de detalhes da métrica. Vários métodos de detecção de anomalias estão disponíveis e você pode combiná-los. Além disso, você pode experimentar diferentes níveis de sensibilidade, direções de detecção e outras configurações. O link de **Configuração avançada** na parte inferior das **configurações de detecção** permite que você crie configurações de detecção mais complexas e personalizadas, que podem ser usadas em grupos ou séries individuais. 

Você também pode ajustar a detecção de anomalias fornecendo comentários para o algoritmo de detecção. Clique em uma anomalia e use o painel **Adicionar comentários** para configurar o status da anomalia, a sazonalidade e o status do ponto de alteração. Esses comentários serão incorporados à detecção para pontos futuros.  

Na parte inferior do painel **Adicionar comentários**, há um link **Para o hub de incidentes**, que levará você até a página de análise de incidentes para analisar a causa raiz do incidente.  

:::image type="content" source="../media/incident-link.png" alt-text="Link do incidente" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Explorar os resultados da detecção de anomalias e realizar a análise da causa raiz

Quando clicar no link **Para o hub de incidentes** de uma anomalia, você verá uma página de análise de incidentes contendo insights de diagnóstico sobre o incidente, como Gravidade, o número de anomalias envolvidas e a hora de início/término. A seção **Causa raiz** exibe conselhos automatizados analisando a árvore de incidentes, levando em conta: desvio, distribuição e contribuição para anomalias pai, que podem ser a causa raiz do incidente.

A seção **Diagnóstico** mostra uma árvore do incidente, com várias guias para diagnosticar o incidente.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Diagnóstico de incidentes" lightbox="../media/incident-diagnostic.png":::

Ao identificar a causa raiz do incidente, você pode tomar medidas e atenuar o problema antes que a situação piore. Você também pode explorar mais insights clicando nos outros recursos de diagnóstico fornecidos. 

## <a name="next-steps"></a>Próximas etapas

- [Usar o portal da Web](web-portal.md)
- [Usar a API REST](rest-api.md)
- [Integrar seus feeds de dados](../how-tos/onboard-your-data.md)
    - [Gerenciar feeds de dados](../how-tos/manage-data-feeds.md)
    - [Configurações para diferentes fontes de dados](../data-feeds-from-different-sources.md)
