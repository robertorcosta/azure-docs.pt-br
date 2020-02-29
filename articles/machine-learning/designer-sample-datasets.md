---
title: Usar os exemplos de conjuntos de dados
titleSuffix: Azure Machine Learning
description: Descrições dos conjuntos de valores usados em modelos de exemplo incluídos no Machine Learning designer. Você pode usar esses conjuntos de valores de exemplo para seus pipelines.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165056"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Usar os conjuntos de valores de exemplo no designer de Azure Machine Learning (versão prévia)

Quando você cria um novo pipeline no designer de Azure Machine Learning (versão prévia), vários exemplos de conjuntos de exemplo são incluídos por padrão. Muitos desses conjuntos de exemplo de conjunto de exemplos são usados pelos modelos de exemplo na página inicial do designer. Outros são incluídos como exemplos de vários tipos de dados usados no aprendizado de máquina.

Alguns desses conjuntos de dados estão disponíveis no armazenamento de Blobs do Azure. A tabela a seguir fornece um link direto para esses conjuntos de dados. Você pode usar esses conjuntos de dados em seus pipelines usando o módulo [importar data](./algorithm-module-reference/import-data.md) .

O restante desses conjuntos de exemplos de exemplo está disponível em **conjuntos** de-categoria de **exemplos** . Você pode encontrar isso na paleta de módulo à esquerda da tela no designer. Você pode usar qualquer um desses conjuntos de valores em seu próprio pipeline arrastando-o para a tela.

## <a name="datasets"></a>Conjuntos de dados

<table>

<tr>
  <th>Nome do conjunto de dados</th>
  <th>Descrição do conjunto de dados</th>
</tr>

<tr>
  <td>Conjunto de dados de classificação binária de receita no recenseamento adulto</td>
  <td>
Um subconjunto do banco de dados do recenseamento de 1994, usando adultos em fase de trabalho, com idade acima de 16 anos com um índice de receita ajustado de > 100.
<p></p>
<b>Uso:</b> classificar pessoas usando dados demográficos para prever se uma pessoa recebe acima de 50 mil por ano.
<p></p>
<b>Pesquisa relacionada:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Dados de preço de automóvel (Brutos)</td>
  <td>
Informações sobre automóveis por marca e modelo, incluindo o preço, recursos como número de cilindradas e MPG, bem como uma pontuação de risco de seguro.
<p></p>
A pontuação de risco é inicialmente associada com preço automático. Depois ela é ajustada ao risco real em um processo conhecido pelos atuários como valor simbólico. Um valor de +3 indica que o automóvel apresenta risco e um valor de -3 indica que ele provavelmente é seguro.
<p></p>
<b>Uso:</b> prever a pontuação de risco por recursos, usando a regressão ou a classificação multivariada. 
<p></p>
<b>Pesquisa relacionada:</b> Schlimmer, J.C. (1987). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>


<tr>
  <td>Rótulos de apetência CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Rótulos de variação CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>Conjunto de dados CRM compartilhado</td>
  <td>
Estes dados foram obtidos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Esse conjunto de dados contém 50 mil clientes da empresa francesa de telecomunicações Orange. Cada cliente possui 230 recursos anônimos, dos quais 190 são numéricos e 40 categóricos. Os recursos são muito esparsos.
  </td>
</tr>

<tr>
  <td>Rótulos de vendas agregadas CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento do cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Dados de atrasos de voo</td>
  <td>
Dados de desempenho no horário do vôo em tempo hábil obtidos da coleta de dados TranStats do departamento de transporte dos EUA (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">no momento</a>).
<p></p>
O conjunto de dados abrange o período de abril a outubro de 2013. Antes de carregar para o designer, o conjunto de um foi processado da seguinte maneira:
<ul>
  <li>O conjunto de dados foi filtrado para cobrir os 70 aeroportos mais movimentados dos EUA continentais.</li>
  <li>Os voos cancelados foram rotulados como atrasados por mais de 15 minutos</li>
  <li>Voos desviados foram retirados.</li>
  <li>As seguintes colunas foram selecionadas: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelled</li>
</ul>
</td>
</tr>

<tr>
  <td>Conjunto de dados do cartão de crédito alemão UCI</td>
  <td>
O conjunto de dados UCI Statlog (cartão de crédito alemão) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>), usando o arquivo german.data.
<p></p>
O conjunto de dados classifica pessoas, descritas como um conjunto de atributos, como baixos ou altos riscos de crédito. Cada exemplo representa uma pessoa. Há 20 recursos, ambos numéricos e categóricos, e um rótulo binário (o valor de risco de crédito). Entradas de risco de crédito alto têm o rótulo = 2, entradas de risco de crédito baixo têm o rótulo = 1. O custo de classificar incorretamente um exemplo de risco baixo como alto é 1, considerando que o custo de classificar incorretamente um exemplo de risco alto como baixo é 5.
  </td>
</tr>

<tr>
  <td>Títulos de filmes no IMDB</td>
  <td>
O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: ID de filme no IMDB, nome e gênero do filme e ano de produção. Há 17 mil filmes no conjunto de dados. O conjunto de dados foi introduzido no artigo “S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filmes coletado do Twitter. Oficina de crowdsourcing and computação humana para sistemas recomendados, CrowdRec em RecSys 2013."
  </td>
</tr>

<tr>
  <td>Classificações de filme</td>
  <td>
O conjunto de dados é uma versão estendida do conjunto de dados de Movie Tweetings. O conjunto de dados tem as classificações de 170 mil de filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é uma tupla: ID de usuário, ID de filme do IMDB, classificação, carimbo de data/hora, número de favoritos para este tweet e número de retweets desse tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para desafio de sistemas de recomendação 2014.
  </td>
</tr>


<tr>
  <td>Conjunto de dados de clima</td>
  <td>
Observações climáticas terrestres de hora em hora da NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">dados mesclados de 201304 para 201310</a>).
<p></p>
Os dados de clima abrangem observações feitas de estações meteorológica de aeroportos, que abrangem o período de abril a outubro de 2013. Antes de carregar para o designer, o conjunto de um foi processado da seguinte maneira:
<ul>
  <li>As IDs da estação meteorológica foram mapeadas para as IDs do aeroporto correspondentes.</li>
  <li>As estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas e retiradas</li>
  <li>A coluna de Data foi dividida em colunas separadas para Ano, Mês e Dia</li>
  <li>As seguintes colunas foram selecionadas: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Conjunto de dados da SP 500 da Wikipédia</td>
  <td>
Os dados foram extraídos do Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>), com base em artigos de cada empresa S&P 500, armazenados como dados XML.
<p></p>
Antes de carregar para o designer, o conjunto de um foi processado da seguinte maneira:
<ul>
  <li>Extraia o conteúdo do texto para cada empresa específica</li>
  <li>Remova a formatação wiki</li>
  <li>Remova caracteres não alfanuméricos</li>
  <li>Converta todo o texto para minúsculas</li>
  <li>Categorias de empresas conhecidas foram adicionadas</li>
</ul>
<p></p>
Observe que, para algumas empresas, um artigo não foi encontrado, assim, o número de registros é menor que 500.
  </td>
</tr>

</table>

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Conheça os conceitos básicos da análise preditiva e do aprendizado de máquina com [o tutorial: prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md)

* Usar o módulo [importar dados](./algorithm-module-reference/import-data.md) para importar exemplos de conjuntos
