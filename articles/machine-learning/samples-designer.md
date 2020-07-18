---
title: Pipelines de exemplo e conjuntos de dados para o designer (versão prévia)
titleSuffix: Azure Machine Learning
description: Use amostras no designer do Azure Machine Learning para iniciar rapidamente seus pipelines de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.custom: designer
ms.openlocfilehash: d2ed9a605b19287c2ea693187c445dc8a4b3b932
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206329"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer-preview"></a>Pipelines e conjuntos de dados de exemplo para o designer do Azure Machine Learning (versão prévia)

Use os exemplos internos do designer do Azure Machine Learning para começar rapidamente a criar os próprios pipelines de aprendizado de máquina. O [repositório GitHub](https://github.com/Azure/MachineLearningDesigner) do designer do Azure Machine Learning contém uma documentação detalhada para ajudar você a entender alguns cenários comuns de aprendizado de máquina.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).
* Um workspace do Azure Machine Learning com o SKU Enterprise.

## <a name="use-sample-pipelines"></a>Usar pipelines de exemplo

O designer salva uma cópia dos pipelines de exemplo no seu workspace do estúdio. Você pode editar o pipeline para adaptá-lo às suas necessidades e salvá-lo como o próprio. Use-os como um ponto de partida para iniciar rapidamente seus projetos.

Veja como usar um exemplo de designer:

1. Entre em <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selecione o workspace com o qual deseja trabalhar.

1. Selecione **Designer**.

1. Selecione um pipeline de exemplo na seção **Novo pipeline**.

    Selecione **Mostrar mais amostras** para obter uma lista completa de amostras.

1. Para executar um pipeline, primeiro defina o destino de computação padrão no qual executar o pipeline.

   1. No painel **Configurações** à direita da tela, selecione **Selecionar de destino de computação**.

   1. Na caixa de diálogo exibida, selecione um destino de computação existente ou crie um. Clique em **Salvar**.

   1. Selecione **Enviar** na parte superior da tela para enviar uma execução de pipeline.

   Dependendo do pipeline de exemplo e das configurações de computação, as execuções podem levar algum tempo para serem concluídas. As configurações de computação padrão têm um tamanho de nó mínimo de 0, o que significa que o designer precisa alocar recursos depois de ficar ocioso. Execuções de pipeline repetidas levarão menos tempo, já que os recursos de computação já estão alocados. Além disso, o designer usa resultados armazenados em cache para cada módulo para melhorar ainda mais a eficiência.


1. Depois que a execução do pipeline for concluída, você poderá examinar o pipeline e exibir a saída de cada módulo para saber mais. Use as seguintes etapas para exibir as saídas do módulo:

   1. Selecione um módulo na tela.

   1. No painel de detalhes do módulo à direita da tela, selecione **Saídas + logs**. Selecione o ícone de grafo ![ícone de visualizar](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para ver os resultados de cada módulo. 

   Use os exemplos como pontos de partida para alguns dos cenários de aprendizado de máquina mais comuns.

## <a name="regression"></a>Regressão

Explore esses exemplos internos de regressão.

| Titulo do exemplo | Descrição | 
| --- | --- |
| [Amostra 1: regressão – previsão de preços de automóveis (básica)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Preveja os preços de carros usando a regressão linear. |
| [Amostra 2: regressão – previsão de preço de automóveis (avançada)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Preveja os preços de carros usando a floresta de decisão e os regressores de árvore de decisão aumentada. Compare os modelos para encontrar o melhor algoritmo.

## <a name="classification"></a>classificação

Explore esses exemplos internos de classificação. Saiba mais sobre as amostras sem os links de documentação abrindo as amostras e vendo os comentários do módulo.

| Titulo do exemplo | Descrição | 
| --- | --- |
| [Amostra 3: classificação binária com seleção de recursos – previsão de receita](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Preveja a receita como alta ou baixa usando uma árvore de decisão aumentada de duas classes. Use a correlação de Pearson para selecionar recursos.
| [Amostra 4: classificação binária com script Python personalizado – previsão de risco de crédito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Classifique os pedidos de crédito como de alto ou baixo risco. Use o módulo Executar Script Python para ponderar seus dados.
| [Amostra 5: classificação binária – previsão de relacionamento com o cliente](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Preveja a rotatividade de clientes usando árvores de decisão aumentadas de duas classes. Use o SMOTE para fazer a amostragem dos dados polarizados.
| [Amostra 7: classificação de textos – conjunto de dados SP 500 da Wikipédia](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Classifique os tipos de empresas com base nos artigos da Wikipédia com a regressão logística multiclasse. |
| Amostra 12: Classificação multiclasse – reconhecimento de letras | Crie um conjunto de classificadores binários para classificar letras escritas. |

## <a name="recommender"></a>Sistema de recomendação

Explore esses exemplos internos de sistema de recomendação. Saiba mais sobre as amostras sem os links de documentação abrindo as amostras e vendo os comentários do módulo.

| Titulo do exemplo | Descrição | 
| --- | --- |
| Amostra 10: recomendação – tweets de classificação de filmes | Crie um mecanismo de recomendação de filmes com base nos títulos e na classificação de filmes. |

## <a name="utility"></a>Utilitário

Saiba mais sobre as amostras que descrevem os recursos e os utilitários de aprendizado de máquina. Saiba mais sobre as amostras sem os links de documentação abrindo as amostras e vendo os comentários do módulo.

| Titulo do exemplo | Descrição | 
| --- | --- |
| [Amostra 6: usar um script R personalizado – previsão de atraso de voos](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Amostra 8: validação cruzada para classificação binária – previsão de receita de adulto | Use a validação cruzada para criar um classificador binário para a receita de adulto.
| Amostra 9: Importância do recurso de permuta | Use a importância do recurso de permuta para computar pontuações de importância para o conjunto de dados de teste. 
| Amostra 11: ajustar parâmetros para classificação binária – previsão de receita de adulto | Use os hiperparâmetros do modelo de ajuste para encontrar hiperparâmetros ideais para a criação de um classificador binário. |

## <a name="datasets"></a>Conjunto de dados

Ao criar um pipeline no designer do Azure Machine Learning, diversos conjuntos de dados de exemplo são incluídos por padrão. Esses conjuntos de dados de exemplo são usados pelos pipelines de exemplo na home page do designer. 

Os conjuntos de dados de exemplo estão disponíveis na categoria **Conjuntos de Dados**-**Amostras**. Encontre-a na paleta do módulo à esquerda da tela do designer. Você pode usar qualquer um desses conjuntos de dados no próprio pipeline arrastando-o para a tela.

| Nome do&nbsp;conjunto de dados&nbsp;&nbsp;&nbsp;&nbsp;| Descrição do conjunto de dados |
|-------------|:--------------------|
| Conjunto de dados de classificação binária de receita no recenseamento adulto | Um subconjunto do banco de dados do recenseamento de 1994, usando adultos em fase de trabalho, com idade acima de 16 anos com um índice de receita ajustado de > 100.<br/>**Uso**: classificar pessoas usando dados demográficos para prever se uma pessoa recebe acima de 50 mil por ano.<br/> **Pesquisa relacionada**: Kohavi, R., Becker, B., (1996). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science|
|Dados de preço de automóvel (Brutos)|Informações sobre automóveis por marca e modelo, incluindo o preço, recursos como número de cilindradas e MPG, bem como uma pontuação de risco de seguro.<br/> A pontuação de risco é inicialmente associada com preço automático. Depois ela é ajustada ao risco real em um processo conhecido pelos atuários como valor simbólico. Um valor de +3 indica que o automóvel apresenta risco e um valor de -3 indica que ele provavelmente é seguro.<br/>**Uso**:</b> prever a pontuação de risco por recursos, usando a regressão ou a classificação multivariada.<br/>**Pesquisa relacionada**:</b> Schlimmer, J.C. (1987). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science. |
| Rótulos de apetência CRM compartilhados |Rótulos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|Rótulos de variação CRM compartilhados|Rótulos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|Conjunto de dados CRM compartilhado | Estes dados foram obtidos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Esse conjunto de dados contém 50 mil clientes da empresa francesa de telecomunicações Orange. Cada cliente possui 230 recursos anônimos, dos quais 190 são numéricos e 40 categóricos. Os recursos são muito esparsos. |
|Rótulos de vendas agregadas CRM compartilhados|Rótulos do desafio de previsão de relacionamento com o Cliente do KDD Cup 2009 ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Dados de atrasos de voo|Dados de desempenho pontual do voo do passageiro obtidos da coleção de dados TranStats do Departamento de Transportes dos EUA ([On-Time](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>O conjunto de dados abrange o período de abril a outubro de 2013. Antes do upload no designer, o conjunto de dados foi processado da seguinte forma: <br/>– O conjunto de dados foi filtrado para abranger somente os 70 aeroportos mais movimentados dos EUA continentais <br/>– Os voos cancelados foram rotulados como atrasados por mais de 15 minutos <br/>– Os voos desviados foram filtrados <br/>– As seguintes colunas foram selecionadas: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled|
|Conjunto de dados do cartão de crédito alemão UCI|O conjunto de dados UCI Statlog (cartão de crédito alemão) ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))), usando o arquivo german.data.<br/>O conjunto de dados classifica pessoas, descritas como um conjunto de atributos, como baixos ou altos riscos de crédito. Cada exemplo representa uma pessoa. Há 20 recursos, ambos numéricos e categóricos, e um rótulo binário (o valor de risco de crédito). Entradas de risco de crédito alto têm o rótulo = 2, entradas de risco de crédito baixo têm o rótulo = 1. O custo de classificar incorretamente um exemplo de risco baixo como alto é 1, considerando que o custo de classificar incorretamente um exemplo de risco alto como baixo é 5.|
|Títulos de filmes no IMDB|O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: ID do filme no IMDB, nome do filme, gênero e ano de produção. Há 17 mil filmes no conjunto de dados. O conjunto de dados foi introduzido no artigo “S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filmes coletado do Twitter. Oficina de crowdsourcing and computação humana para sistemas recomendados, CrowdRec em RecSys 2013."|
|Classificação de filmes|O conjunto de dados é uma versão estendida do conjunto de dados de Movie Tweetings. O conjunto de dados tem as classificações de 170 mil de filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é uma tupla: ID de usuário, ID de filme no IMDB, classificação, carimbo de data/hora, número de favoritos para este tweet e número de retweets deste tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para desafio de sistemas de recomendação 2014.|
|Conjunto de dados de clima|Observações climáticas terrestres de hora em hora da NOAA ([dados mesclados de 201304 para 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Os dados de clima abrangem observações feitas de estações meteorológica de aeroportos, que abrangem o período de abril a outubro de 2013. Antes do upload no designer, o conjunto de dados foi processado da seguinte forma:    <br/> – As IDs das estações meteorológicas foram mapeadas para as IDs dos aeroportos correspondentes    <br/> – As estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas    <br/> – A coluna Data foi dividida nas colunas separadas Ano, Mês e Dia    <br/> – As seguintes colunas foram selecionadas: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Conjunto de dados da SP 500 da Wikipédia|Os dados foram extraídos da Wikipédia (https://www.wikipedia.org/), com base em artigos de cada empresa S&P 500, armazenados como dados XML.    <br/>Antes do upload no designer, o conjunto de dados foi processado da seguinte forma:    <br/> – Extração do conteúdo de texto de cada empresa específica    <br/> – Remoção da formatação do wiki    <br/> – Remoção de caracteres não alfanuméricos    <br/> – Conversão de todo o texto em minúsculas    <br/> – Adição de categorias de empresas conhecidas    <br/>Observe que, para algumas empresas, um artigo não foi encontrado, assim, o número de registros é menor que 500.|


## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Conheça os conceitos básicos da análise preditiva e do aprendizado de máquina com o [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md)

