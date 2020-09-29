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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450110"
---
# <a name="convert-to-image-directory"></a>Converter em Diretório de Imagem

Este artigo descreve como usar o módulo converter em diretório de imagem para ajudar a converter o conjunto de dados de imagem no tipo de dado ' diretório de imagem ', que é um formato de dados padronizado em tarefas relacionadas à imagem, como classificação de imagem no designer de Azure Machine Learning.

## <a name="how-to-use-convert-to-image-directory"></a>Como usar converter em diretório de imagem  

1.  Adicione o módulo **converter no diretório de imagens** à tela. Você pode encontrar esse módulo na categoria ' Pesquisa Visual Computacional/transformação de dados de imagem ' na lista de módulos. 

2.  A entrada do módulo **converter em diretório de imagem** deve ser um conjunto de dados de arquivo. [Registre um conjunto](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) de dados de imagem e conecte-o à porta de entrada do módulo. Verifique se há uma imagem no conjunto de dados de entrada. Atualmente, o designer não oferece suporte a visualização do conjunto de imagem.
 
    Há suporte para os seguintes formatos de conjunto de os:

    - Arquivo compactado nestas extensões: '. zip ', '. tar ', '. gz ', '. bz2 '.
    - Pasta que contém imagens. É **altamente recomendável compactar essa pasta primeiro, então, usar o arquivo compactado como DataSet**.

    > [!WARNING]
    > **Não é possível** usar o módulo **importar dados** para importar DataSet de imagem, pois o tipo de saída do módulo **importar dados** é o diretório dataframe, que contém apenas uma cadeia de caracteres de caminho de arquivo.
    

    > [!NOTE]
    > - Se usar o conjunto de informações de imagem no aprendizado supervisionado, você precisará especificar o rótulo do conjunto de informações de treinamento.
    > - Para a tarefa de classificação de imagem, o rótulo poderá ser gerado como a imagem ' Category ' na saída do módulo se esse conjunto de imagens for organizado no formato torchvision ImageFolder. Caso contrário, somente imagens serão salvas sem rótulo. Veja a seguir um exemplo de como você pode organizar o conjunto de imagem para obter o rótulo, usar a categoria da imagem como nome da subpasta. 
    > - Você não precisa carregar a mesma contagem de imagens em cada pasta de categoria.
    > - Há suporte para imagens com essas extensões (em minúsculas): '. jpg ', '. jpeg ', '. png ', '. ppm ', '. bmp ', '. PGM ', '. tif ', '. TIFF ', '. webp '. Você também pode ter vários tipos de imagens em uma pasta.    
    > - Consulte conjuntos de dados [torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) para obter mais informações.
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - Se usar DataSet de imagem para pontuação, o conjunto de dados do arquivo de entrada deste módulo deverá conter imagens não classificadas.
    
3.  Envie o pipeline. Esse módulo pode ser executado em uma GPU ou CPU.

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
