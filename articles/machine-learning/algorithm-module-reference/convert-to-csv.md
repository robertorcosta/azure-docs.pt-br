---
title: 'Converter para CSV: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Convert to CSV no Azure Machine Learning para converter um conjunto de dados em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477673"
---
# <a name="convert-to-csv-module"></a>Converter para módulo CSV

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para converter um conjunto de dados em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.

### <a name="more-about-the-csv-format"></a>Mais sobre o formato CSV 

O formato CSV, que significa "valores separados por vírgula", é um formato de arquivo usado por muitas ferramentas externas de aprendizado de máquina. CSV é um formato de intercâmbio comum ao trabalhar com linguagens de código aberto, como R ou Python.

Mesmo que você faça a maior parte do seu trabalho no Azure Machine Learning, há momentos em que você pode achar útil converter seu conjunto de dados em CSV para usar em ferramentas externas. Por exemplo: 

+ Baixe o arquivo CSV para abri-lo com o Excel ou importe-o em um banco de dados relacional.  
+ Salve o arquivo CSV para armazenamento em nuvem e conecte-se a ele a partir do Power BI para criar visualizações.  
+ Use o formato CSV para preparar dados para uso em R e Python. 

Quando você converte um conjunto de dados em CSV, o csv é salvo no seu espaço de trabalho Azure ML. Você pode usar um utilitário de armazenamento Azure para abrir e usar o arquivo diretamente. Você também pode acessar o CSV no designer selecionando o módulo **Converter para CSV** e, em seguida, selecionar o ícone de histograma na guia **Saídas** no painel direito para exibir a saída. Você pode baixar o CSV da pasta Resultados para um diretório local.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar converter para CSV


1.  Adicione o módulo Converter para CSV ao seu pipeline. Você pode encontrar este módulo no grupo **de Transformação de Dados** no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de dados.   
  
3.  Envie o oleoduto.

### <a name="results"></a>Resultados
  

Selecione a guia **Saídas** no painel direito de **Converter para CSV**e selecione em um desses ícones as **saídas De porta**.  

+ **Registrar conjunto de dados**: Selecione o ícone e salve o arquivo CSV de volta ao espaço de trabalho do Azure ML como um conjunto de dados separado. Você pode encontrar o conjunto de dados como um módulo na árvore do módulo a seção **Meus conjuntos de dados.**

 + **Exibir saída**: Selecione o ícone do olho e siga as instruções para navegar na pasta **Results_dataset** e baixe o arquivo data.csv.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 