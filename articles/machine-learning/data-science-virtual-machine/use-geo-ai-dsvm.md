---
title: Usar o ia geográfico
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "70278425"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Usar a Máquina Virtual de Ciência de Dados de Inteligência Artificial Geográfica

Use Máquina Virtual da Ciência de Dados de Inteligência Artificial Geográfica para buscar dados para análise, executar a disputa de dados e criar modelos para aplicativos IA que consomem informações geoespaciais. Depois de provisionar seu VM de Ciência de Dados de ia geográfico e entrar no ArcGIS pro por meio de sua conta do ArcGIS, você poderá começar a interagir com o ArcGIS Desktop e o ArcGIs online. Você também pode acessar o ArcGIS de interfaces do Python e uma ponte de linguagem R pré-configurada no VM de Ciência de Dados geográfico. Para criar aplicativos de ia avançados, combine a VM de Ciência de Dados geográfica com as estruturas de aprendizado de máquina e de aprendizado profundo e outros softwares de ciência de dados que estão disponíveis nela.  


## <a name="configuration-details"></a>Detalhes da configuração

A Biblioteca Python, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), que é usada para a interface com ArcGIS, é instalada no ambiente raiz global conda do VM de ciência de dados encontrado em ```c:\anaconda```.

- Se você estiver executando o Python em um prompt de comando ```activate``` , execute para ativar no ambiente Conda raiz Python.
- Se estiver usando um notebook IDE ou Jupyter, você poderá selecionar o ambiente ou o kernel para verificar se está no ambiente Conda correto.

A ponte R para ArcGIS é instalada como uma biblioteca do R chamada [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) no principal Microsoft Machine Learning Server instância autônoma localizada em ```C:\Program Files\Microsoft\ML Server\R_SERVER```. O Visual Studio, o RStudio e o Jupyter já estão pré-configurados para usar esse ambiente R e terão acesso à biblioteca do ```arcgisbinding``` r. 


## <a name="geo-ai-data-science-vm-samples"></a>Amostras de máquina virtual de ciência de dados de IA Geográfica

Além dos exemplos baseados em estrutura de aprendizado de máquina e de aprendizado profundo do VM de Ciência de Dados base, um conjunto de amostras geoespaciais também é fornecido como parte do VM de Ciência de Dados de ia geográfico. Esses exemplos podem ajudá-lo a iniciar seu desenvolvimento de aplicativos de ia usando dados geoespaciais e o software ArcGIS:


1. [Introdução à análise geoespacial com o Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): um exemplo introdutório que mostra como trabalhar com dados geoespaciais por meio da interface Python para ArcGIS é fornecido pela biblioteca [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) . Ele também mostra como combinar o aprendizado de máquina tradicional com dados geoespaciais e, em seguida, Visualizar o resultado em um mapa no ArcGIS.

2. [Introdução à análise geoespacial com R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): um exemplo introdutório que mostra como trabalhar com dados geoespaciais usando a interface R para ArcGIS que é fornecida pela biblioteca [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) . 

3. [Classificação do uso de terra em nível de pixel](https://github.com/Azure/pixel_level_land_classification): um tutorial que mostra como criar um modelo de rede neural profundo que aceita uma imagem aérea como entrada e retorna um rótulo de cobertura de terra. Exemplos de rótulos de tampa terrestre são em uma *floresta* e *água*. O modelo retorna um rótulo para cada pixel da imagem. 


## <a name="next-steps"></a>Próximas etapas

Exemplos adicionais que usam a máquina virtual de ciência de dados estão disponíveis aqui:

* [Amostras](dsvm-samples-and-walkthroughs.md)