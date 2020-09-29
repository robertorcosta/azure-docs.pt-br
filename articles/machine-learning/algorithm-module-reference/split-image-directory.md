---
title: Dividir Diretório de Imagens
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo dividir imagem de imagens no designer de Azure Machine Learning (versão prévia) para dividir as imagens de um diretório de imagem em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: fe5ba25904298fe1a394a4b01d6bdacc72d599c9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448681"
---
# <a name="split-image-directory"></a>Dividir Diretório de Imagens

Este tópico descreve como usar o módulo dividir imagem de imagens no designer de Azure Machine Learning, para dividir as imagens de um diretório de imagem em dois conjuntos distintos.

Esse módulo é particularmente útil quando você precisa separar dados de imagem em conjuntos de treinamento e teste. 

## <a name="how-to-configure-split-image-directory"></a>Como configurar o diretório de imagem dividida

1. Adicione o módulo **dividir imagem do diretório** ao seu pipeline. Você pode encontrar esse módulo na categoria ' Pesquisa Visual Computacional/transformação de dados de imagem '.

2. Conecte-o ao módulo do qual a saída é o diretório de imagens.

3. **Fração de entrada de imagens na primeira saída** para especificar a porcentagem de dados a serem colocados na divisão esquerda, por padrão, 0,9. Se o resultado da fração não for inteiro, o módulo usará o menor inteiro próximo.


## <a name="technical-notes"></a>Observações técnicas

### <a name="expected-inputs"></a>Entradas esperadas

| Nome                  | Tipo           | Descrição              |
| --------------------- | -------------- | ------------------------ |
| Diretório da imagem de entrada | ImageDirectory | Diretório de imagens a ser dividido |

### <a name="module-parameters"></a>Parâmetros do módulo

| Nome                                   | Tipo  | Intervalo | Opcional | Descrição                            | Padrão |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fração de imagens na primeira saída | Float | 0-1   | Obrigatório | Fração de imagens na primeira saída | 0,9     |

### <a name="outputs"></a>Saídas

| Nome                    | Tipo           | Descrição                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Directory1 da imagem de saída | ImageDirectory | Diretório de imagens que contém imagens selecionadas |
| Directory2 da imagem de saída | ImageDirectory | Diretório de imagem que contém todas as outras imagens |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 

