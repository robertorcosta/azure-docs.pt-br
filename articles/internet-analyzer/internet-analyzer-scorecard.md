---
title: Interpretando seu Scorecard | Microsoft Docs
description: A FAQ para Analisador de Internet Do Azure.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512880"
---
# <a name="interpreting-your-scorecard"></a>Interpretar seu scorecard

A guia scorecard contém os resultados agregados e analisados de seus testes. Cada teste tem seus próprios cartões de pontuação. Os scorecards fornecem resumos rápidos e significativos dos resultados de medição para fornecer resultados baseados em dados para seus requisitos de rede. O Internet Analyzer cuida da análise, permitindo que você se concentre na decisão.

A guia scorecard pode ser encontrada no menu de recursos do Analisador da Internet. 


## <a name="filters"></a>Filtros

* ***Teste:*** Selecione o teste para o que você gostaria de ver os resultados - cada teste tem seu próprio cartão de pontuação. Os dados do teste aparecerão assim que houver dados suficientes para concluir a análise – na maioria dos casos, isso deve ser dentro de 24 horas. 
* ***Período de tempo & data final:*** São gerados três scorecards diariamente – cada scorecard reflete um período de agregação diferente – as 24 horas anteriores (dia), os sete dias anteriores (semana) e os 30 dias anteriores (mês). Use o filtro "End Date" para selecionar o último dia do período de tempo que deseja ver. 
* ***País:*** Para cada país que você tem usuários finais, um scorecard é gerado. O filtro global contém todos os usuários finais.

## <a name="measurement-count"></a>Contagem de medição

O número de medições impacta a confiança da análise. Quanto maior a contagem, mais preciso é o resultado. No mínimo, os testes devem apontar para um mínimo de 100 medições por ponto final por dia. Se as contagens de medição forem muito baixas, configure o cliente JavaScript para ser executado com mais freqüência em seu aplicativo. A contagem de medidas para os pontos finais A e B deve ser muito semelhante, embora pequenas diferenças sejam esperadas e ok. No caso de grandes diferenças, os resultados não devem ser confiáveis.

## <a name="percentiles"></a>Percentuais

A latência, medida em milissegundos, é uma métrica popular para medir a velocidade entre uma fonte e o destino na Internet. Os dados de latência não são normalmente distribuídos (ou seja, não segue uma "Curva do Sino") porque há uma "cauda longa" de grandes valores de latência que distorcem os resultados ao usar estatísticas como a média aritmética. Como alternativa, os percentis fornecem uma maneira "livre de distribuição" para analisar dados. Como exemplo, a mediana, ou 50º percentil, resume o meio da distribuição - metade dos valores estão acima dela e metade está abaixo dela. Um valor de 75% do percentil significa que é maior que 75% de todos os valores da distribuição. O Analisador de Internet refere-se a percentis em taquigrafia como P50, P75 e P95.

Percentis do Analisador de Internet são _métricas de amostra_. Isso contrasta com a verdadeira _métrica populacional._ Por exemplo, a latência média diária da população real entre estudantes da Universidade do Sul da Califórnia e da Microsoft é o valor médio de latência de todos os pedidos durante esse dia. Na prática, medir o valor de todos os pedidos é impraticável, por isso assumimos que uma amostra razoavelmente grande é representativa da verdadeira população.

Para fins de análise, P50 (mediana), é útil como valor esperado para uma distribuição de latência. Percentis mais altos, como P95, são úteis para identificar o quão alta é a latência nos piores casos. Se você estiver interessado em entender a latência do cliente em geral, p50 é a métrica correta para se concentrar. Se você está preocupado em entender o desempenho para os clientes com pior desempenho, então o P95 deve ser o foco. P75 é um equilíbrio entre esses dois.


## <a name="deltas"></a>Deltas

Um delta é a diferença nos valores métricos para os pontos finais A e B. Deltas são calculados para mostrar o benefício de B sobre A. Valores positivos indicam que B teve um desempenho melhor que A, enquanto valores negativos indicam que o desempenho de B é pior. Deltas podem ser absolutos (por exemplo, 10 milissegundos) ou relativos (5%).

## <a name="confidence-interval"></a>intervalo de confiança 

Os intervalos de confiança (IC) são uma gama de valores que têm uma probabilidade de conter a métrica populacional, como mediana, P75 ou média. Seguimos a convenção estatística comum de uso do IC 95%.

Para o Analisador de Internet, um intervalo de confiança estreito é bom porque mostra que a métrica da amostra é provavelmente muito próxima da métrica populacional real. Um amplo intervalo de confiança significa menos certeza de que nossa métrica amostral reflete a verdadeira métrica populacional. A melhor maneira de melhorar o IC é aumentar a contagem de medidas.

## <a name="time-series"></a>Série temporal 

Uma série temporal mostra como uma métrica muda ao longo do tempo. Na Internet, há muitos fatores temporais que impactam o desempenho, como períodos de pico de tráfego, diferenças populacionais durante a semana e feriados.


## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte nossa [visão geral do Analisador da Internet](internet-analyzer-overview.md).
