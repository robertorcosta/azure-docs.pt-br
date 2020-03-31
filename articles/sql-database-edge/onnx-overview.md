---
title: Aprendizado de máquina e IA com ONNX no Azure SQL Database Edge Preview | Microsoft Docs
description: O aprendizado de máquina no Azure SQL Database Edge Preview suporta modelos no formato ONNX (Open Neural Network Exchange, troca de redes neurais abertas). ONNX é um formato aberto que você pode usar para trocar modelos entre várias estruturas de aprendizado de máquina e ferramentas.
keywords: implantar borda de banco de dados sql
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366268"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Aprendizado de máquina e IA com ONNX no SQL Database Edge Preview

O aprendizado de máquina no Azure SQL Database Edge Preview suporta modelos no formato [ONNX (Open Neural Network Exchange, troca de redes neurais abertas).](https://onnx.ai/) ONNX é um formato aberto que você pode usar para trocar modelos entre várias [estruturas e ferramentas de aprendizado de máquina.](https://onnx.ai/supported-tools)

## <a name="overview"></a>Visão geral

Para inferir modelos de aprendizado de máquina no Azure SQL Database Edge, primeiro você precisará obter um modelo. Este pode ser um modelo pré-treinado ou um modelo personalizado treinado com sua estrutura de escolha. O Azure SQL Database Edge suporta o formato ONNX e você precisará converter o modelo para este formato. Não deve haver impacto na precisão do modelo, e uma vez que você tenha o modelo ONNX, você pode implantar o modelo no Azure SQL Database Edge e usar [pontuação nativa com a função PREDICT T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Obter modelos ONNX

Para obter um modelo no formato ONNX:

- **Serviços de construção de modelos**: Serviços como o [recurso automatizado machine learning no Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) e suporte ao [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) exportam diretamente o modelo treinado no formato ONNX.

- [**Converter e/ou exportar modelos existentes:**](https://github.com/onnx/tutorials#converting-to-onnx-format)Várias estruturas de treinamento (por exemplo, [PyTorch,](https://pytorch.org/docs/stable/onnx.html)Chainer e Caffe2) suportam a funcionalidade de exportação nativa para o ONNX, o que permite salvar seu modelo treinado para uma versão específica do formato ONNX. Para estruturas que não suportam exportação nativa, existem pacotes instalados onnx converter autônomos que permitem converter modelos treinados de diferentes frameworks de aprendizado de máquina para o formato ONNX.

     **Estruturas com suporte**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Para obter a lista completa de frameworks e exemplos suportados, consulte [Convertendo para o formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitações

Atualmente, nem todos os modelos ONNX são suportados pelo Azure SQL Database Edge. O suporte é limitado a modelos com **tipos de dados numéricos:**

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real e flutuante](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Outros tipos numéricos podem ser convertidos para tipos suportados usando [CAST e CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

As entradas do modelo devem ser estruturadas para que cada entrada ao modelo corresponda a uma única coluna em uma tabela. Por exemplo, se você estiver usando um dataframe pandas para treinar um modelo, então cada entrada deve ser uma coluna separada para o modelo.

## <a name="next-steps"></a>Próximas etapas

- [Implantar a borda do banco de dados SQL através do portal Azure](deploy-portal.md)
- [Implantar um modelo ONNX no Azure SQL Database Edge Preview](deploy-onnx.md)
