---
title: Converter em Diretório de Imagem
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo converter em diretório de imagem para converter o conjunto de informações no formato de diretório de imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 41724753df0d529e4c44344e8e975e68ee5eafd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84904585"
---
# <a name="convert-to-image-directory"></a>Converter em Diretório de Imagem

Este artigo descreve como usar o módulo converter em diretório de imagem para ajudar a converter o conjunto de dados de imagem no tipo de dado ' diretório de imagem ', que é um formato de dados padronizado em tarefas relacionadas à imagem, como classificação de imagem no designer de Azure Machine Learning (versão prévia).

## <a name="how-to-use-convert-to-image-directory"></a>Como usar converter em diretório de imagem  

1.  Adicione o módulo **converter no diretório de imagens** ao seu experimento. Você pode encontrar esse módulo na categoria ' Pesquisa Visual Computacional/transformação de dados de imagem ' na lista de módulos. 

2.  Conecte um conjunto de dados de imagem como entrada. Verifique se há uma imagem no conjunto de dados de entrada.
    Há suporte para os seguintes formatos de conjunto de os:

    - Arquivo compactado nestas extensões: '. zip ', '. tar ', '. gz ', '. bz2 '.
    - Pasta que contém imagens. É **altamente recomendável compactar essa pasta primeiro, então, usar o arquivo compactado como DataSet**.

    > [!NOTE]
    > Se usar conjunto de informações de imagem no aprendizado supervisionado, o rótulo será necessário.
    > Para a tarefa de classificação de imagem, o rótulo poderá ser gerado como a imagem ' Category ' na saída do módulo se esse conjunto de imagens for organizado no formato torchvision ImageFolder. Caso contrário, somente imagens serão salvas sem rótulo. Aqui está um exemplo de como você pode organizar o conjunto de imagens de imagem para obter o rótulo, usar a categoria da imagem como nome da subpasta. Consulte conjuntos de dados [torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) para obter mais informações.
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  Enviar o pipeline.

## <a name="results"></a>Resultados

A saída do módulo **converter para o diretório de imagens** está no formato do diretório de imagens e pode ser conectada a outros módulos relacionados à imagem dos quais o formato da porta de entrada também é o diretório de imagens.

## <a name="technical-notes"></a>Observações técnicas 

###  <a name="expected-inputs"></a>Entradas esperadas  

| Nome          | Tipo                  | Descrição   |
| ------------- | --------------------- | ------------- |
| Conjunto de dados de entrada | AnyDirectory, ZipFile | Conjunto de dados de entrada |

###  <a name="output"></a>Saída  

| Nome                   | Tipo           | Descrição            |
| ---------------------- | -------------- | ---------------------- |
| Diretório de imagens de saída | ImageDirectory | Diretório de imagens de saída |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
