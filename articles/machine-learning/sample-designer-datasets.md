---
title: Usar conjuntos de dados de exemplo no designer do Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os conjuntos de dados de exemplo incluídos no designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037298"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Usar os conjuntos de dados de exemplo no designer do Azure Machine Learning (versão prévia)

Ao criar um pipeline no designer do Azure Machine Learning (versão prévia), diversos conjuntos de dados de exemplo são incluídos por padrão. Esses conjuntos de dados de exemplo são usados pelos pipelines de exemplo na home page do designer. 

Os conjuntos de dados de exemplo estão disponíveis na categoria **Conjuntos de Dados**-**Amostras**. Encontre-a na paleta do módulo à esquerda da tela do designer. Você pode usar qualquer um desses conjuntos de dados no próprio pipeline arrastando-o para a tela.

## <a name="datasets"></a>Conjunto de dados


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

## <a name="next-steps"></a>Próximas etapas

* Conheça os conceitos básicos da análise preditiva e do aprendizado de máquina com o [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md)

* Saiba como modificar as [amostras existentes do designer](samples-designer.md) para adaptá-las às suas necessidades.
