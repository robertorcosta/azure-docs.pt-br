---
title: 'Designer: exemplo de previsão de atrasos nos voos'
titleSuffix: Azure Machine Learning
description: Crie um classificador e use um código R personalizado para prever os atrasos nos voos com o designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: d459350572d68cc5dcbbfd56933483404b94f918
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963268"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Criar um classificador e usar o R para prever os atrasos nos voos com o designer do Azure Machine Learning

**Amostra 6 do designer (versão prévia)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Este pipeline usa dados históricos de voos e meteorológicos para prever se um voo de passageiros agendado será atrasado em mais de 15 minutos. Esse problema pode ser abordado como um problema de classificação, com a previsão de duas classes: atrasado ou em ponto.

Este é o grafo do pipeline final desta amostra:

[![Grafo do pipeline](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 6 para abri-la.

## <a name="get-the-data"></a>Obter os dados

Esta amostra usa o conjunto de dados **Dados de Atrasos nos Voos**. Ele faz parte da coleção de dados TranStats do Departamento de Transportes dos EUA. O conjunto de dados contém informações de atrasos nos voos de abril a outubro de 2013. Ele foi pré-processado da seguinte maneira:

* Filtragem para incluir os 70 aeroportos mais movimentados nos Estados Unidos continentais.
* Nova rotulagem dos voos cancelados como atrasados em mais de 15 minutos.
* Filtragem dos voos desviados.
* Seleção de 14 colunas.

Para complementar os dados de voos, o **Conjunto de Dados Meteorológicos** é usado. Os dados meteorológicos contêm observações meteorológicas terrestres por hora da NOAA e representam as observações das estações meteorológicas para os aeroportos, abrangendo o mesmo período do conjunto de dados de voos. Ele foi pré-processado da seguinte maneira:

* As IDs das estações meteorológicas foram mapeadas para as IDs dos aeroportos correspondentes.
* As estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram removidas.
* A coluna Date foi dividida em colunas separadas: Year, Month e Day.
* Seleção de 26 colunas.

## <a name="pre-process-the-data"></a>Pré-processar os dados

Um conjunto de dados geralmente exige um pré-processamento para ser analisado.

![data-process](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>Dados de voos

As colunas **Carrier**, **OriginAirportID** e **DestAirportID** são salvas como inteiros. No entanto, eles são atributos categóricos; use o módulo **Editar Metadados** para convertê-los em categóricos.

![edit-metadata](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

Em seguida, use o módulo **Selecionar Colunas** no módulo Conjunto de Dados para excluir do conjunto de dados as colunas que são possíveis condutores-alvo de perda: **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled** e **Year**. 

Para unir os registros de voos com os registros meteorológicos por hora, use a hora de partida agendada como uma das chaves de junção. Para fazer a junção, a coluna CSRDepTime precisa ser arredondada para a hora anterior mais próxima, o que é feito no módulo **Executar Script R**. 

### <a name="weather-data"></a>Dados de clima

As colunas que têm uma grande proporção de valores ausentes são excluídas usando o módulo **Colunas do Projeto**. Essas colunas incluem todas as colunas com valor de cadeia de caracteres: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure** e **StationPressure**.

O módulo **Limpar Dados Ausentes** é então aplicado às colunas restantes para remover linhas com alguns dados ausentes.

Os tempos de observação meteorológica são arredondados para a hora completa seguinte mais próxima. As horas de voos agendadas e as horas de observação meteorológica são arredondadas em direções opostas para garantir que o modelo use apenas o clima antes da hora do voo. 

Como os dados meteorológicos são relatados na hora local, as diferenças de fuso horário são levadas em conta subtraindo as colunas de fuso horário da hora de partida agendada e da hora de observação meteorológica. Essas operações são feitas usando o módulo **Executar Script R**.

### <a name="joining-datasets"></a>Como unir conjuntos de dados

Os registros de voos são unidos com os dados meteorológicos na origem do voo (**OriginAirportID**) usando o módulo **Unir Dados**.

 ![unir voo e clima pela origem](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


Os registros de voos são unidos com os dados meteorológicos usando o destino do voo (**DestAirportID**).

 ![Unir voo e clima pelo destino](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Como preparar amostras de treinamento e teste

O módulo **Dividir Dados** divide os dados em registros de abril a setembro para treinamento e em registros de outubro para teste.

 ![Dividir dados de treinamento e teste](./media/how-to-designer-sample-classification-flight-delay/split.png)

As colunas de ano, mês e fuso horário são removidas do conjunto de dados de treinamento usando o módulo Selecionar Colunas.

## <a name="define-features"></a>definir recursos

No aprendizado de máquina, recursos são propriedades individuais mensuráveis de algo em que você está interessado. Encontrar um conjunto forte de recursos exige experimentação e conhecimento do domínio. Alguns recursos são melhores para prever o destino do que outros. Além disso, alguns recursos podem ter uma correlação forte com outros recursos e não adicionarão novas informações ao modelo. Esses recursos podem ser removidos.

Para criar um modelo, você pode usar todos os recursos disponíveis ou selecionar um subconjunto dos recursos.

## <a name="choose-and-apply-a-learning-algorithm"></a>escolher e aplicar um algoritmo de aprendizado

Crie um modelo usando o módulo **Regressão Logística de Duas Classes** e treine-o no conjunto de dados de treinamento. 

O resultado do módulo **Treinar Modelo** é um modelo de classificação treinado que pode ser usado para pontuar novas amostras a fim de fazer previsões. Use o conjunto de teste para gerar pontuações dos modelos treinados. Em seguida, use o módulo **Avaliar Modelo** para analisar e comparar a qualidade dos modelos.
pipeline Depois de executar o pipeline, veja a saída do módulo **Pontuar Modelo** clicando na porta de saída e selecionando **Visualizar**. A saída inclui os rótulos pontuados e as probabilidades para os rótulos.

Por fim, para testar a qualidade dos resultados, adicione o módulo **Avaliar Modelo** para a tela do pipeline e conecte a porta de entrada esquerda à saída do módulo Pontuar Modelo. Execute o pipeline e veja a saída do módulo **Avaliar Modelo**, clicando na porta de saída e selecionando **Visualizar**.

## <a name="evaluate"></a>Avaliar
O modelo de regressão logística tem uma AUC igual a 0,631 no conjunto de teste.

 ![avaliar](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>Próximas etapas

Explore as outras amostras disponíveis para o designer:

- [Amostra 1 – Regressão: Prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 2 – Regressão: Comparar algoritmos para a previsão de preços de automóveis](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 3 – Classificação com seleção de recursos: Previsão de receita](how-to-designer-sample-classification-predict-income.md)
- [Amostra 4: classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5 – Classificação: Prever a rotatividade](how-to-designer-sample-classification-churn.md)
- [Amostra 7 – Classificação de textos: Conjunto de dados SP 500 da Wikipédia](how-to-designer-sample-text-classification.md)