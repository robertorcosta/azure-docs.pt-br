---
title: Use o Geo AI
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como usar a Máquina Virtual de Ciência de Dados de Inteligência Artificial Geográfica para analisar dados e criar modelos com base em dados geoespaciais.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278425"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Usar a Máquina Virtual de Ciência de Dados de Inteligência Artificial Geográfica

Use Máquina Virtual da Ciência de Dados de Inteligência Artificial Geográfica para buscar dados para análise, executar a disputa de dados e criar modelos para aplicativos IA que consomem informações geoespaciais. Depois de provisionar seu VM Geo AI Data Science e fazer o seu contrato com o ArcGIS Pro através da sua conta ArcGIS, você pode começar a interagir com o desktop ArcGIS e os ArcGIs on-line. Você também pode acessar o ArcGIS a partir de interfaces Python e uma ponte de linguagem R pré-configurada no VM De Geo-Data Science. Para criar aplicativos ricos de IA, combine o VM de Geo-Data Science com as estruturas de aprendizado de máquina e deep-learning e outros softwares de ciência de dados que estão disponíveis nele.  


## <a name="configuration-details"></a>Detalhes da configuração

A biblioteca Python, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), que é usada para interagir com o ArcGIS, está instalada no ambiente ```c:\anaconda```global de conda raiz do VM de Data Science que é encontrado em .

- Se você estiver executando python em ```activate``` um prompt de comando, execute para ativar no ambiente Python raiz conda.
- Se você estiver usando um notebook IDE ou Jupyter, você pode selecionar o ambiente ou o kernel para ter certeza de que está no ambiente conda correto.

A ponte R para ArcGIS é instalada como uma biblioteca R chamada [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) na instância ```C:\Program Files\Microsoft\ML Server\R_SERVER```autônoma principal do Microsoft Machine Learning Server que está localizada em . Visual Studio, RStudio e Jupyter já estão pré-configurados para usar ```arcgisbinding``` este ambiente R e terão acesso à biblioteca R. 


## <a name="geo-ai-data-science-vm-samples"></a>Amostras de máquina virtual de ciência de dados de IA Geográfica

Além das amostras baseadas em framework de aprendizagem de máquina e de aprendizagem profunda da VM base Data Science, um conjunto de amostras geoespaciais também é fornecido como parte do VM Geo AI Data Science. Essas amostras podem ajudá-lo a iniciar o desenvolvimento de aplicações de IA usando dados geoespaciais e o software ArcGIS:


1. [Começando com análisegeoespacial com Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Uma amostra introdutória mostrando como trabalhar com dados geoespaciais através da interface Python para ArcGIS é fornecida pela biblioteca [arcpy.](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) Ele também mostra como combinar aprendizado de máquina tradicional com dados geoespaciais e, em seguida, visualizar o resultado em um mapa no ArcGIS.

2. [Começando com análise geoespacial com R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Uma amostra introdutória que mostra como trabalhar com dados geoespaciais usando a interface R para ArcGIS fornecida pela biblioteca [de arquivamento.](https://github.com/R-ArcGIS/r-bridge) 

3. [Classificação do uso de terra em nível de pixel](https://github.com/Azure/pixel_level_land_classification): um tutorial que mostra como criar um modelo de rede neural profundo que aceita uma imagem aérea como entrada e retorna um rótulo de cobertura de terra. Exemplos de rótulos de cobertura de terra são *florestados* e *água.* O modelo retorna um rótulo para cada pixel da imagem. 


## <a name="next-steps"></a>Próximas etapas

Exemplos adicionais que usam a máquina virtual de ciência de dados estão disponíveis aqui:

* [Amostras](dsvm-samples-and-walkthroughs.md)