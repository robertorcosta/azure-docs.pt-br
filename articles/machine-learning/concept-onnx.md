---
title: 'ONNX: inferência de alta perf, multiplataforma'
titleSuffix: Azure Machine Learning
description: Saiba como usar o Open Neural Network Exchange (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270167"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: crie e acelere modelos ML

Saiba como usar o [Open Neural Network Exchange](https://onnx.ai) (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizado de máquina. Inferência, ou pontuação do modelo, é a fase em que o modelo implantado é usado para predição, mais comumente em dados de produção. 

Otimizar modelos de aprendizado de máquina para inferência (ou pontuação de modelo) é difícil, pois você precisa ajustar o modelo e a biblioteca de inferências para aproveitar ao máximo os recursos de hardware. O problema torna-se extremamente difícil se você quiser obter um desempenho ideal em diferentes tipos de plataformas (nuvem/borda, CPU/GPU, etc.), já que cada uma tem recursos e características diferentes. A complexidade aumenta se você tiver modelos de uma variedade de frameworks que precisam ser executados em uma variedade de plataformas. É muito demorado para otimizar todas as diferentes combinações de frameworks e hardware. Uma solução para treinar uma vez em sua estrutura preferida e correr em qualquer lugar na nuvem ou borda é necessária. É aqui que entra o ONNX.

A Microsoft e uma comunidade de parceiros criaram o ONNX como um padrão aberto para representar modelos de aprendizado de máquina. Modelos de [muitas estruturas,](https://onnx.ai/supported-tools) incluindo TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet e MATLAB podem ser exportados ou convertidos para o formato ONNX padrão. Uma vez que os modelos estão no formato ONNX, eles podem ser executados em uma variedade de plataformas e dispositivos.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) é um mecanismo de inferência de alto desempenho para implantar modelos ONNX na produção. É otimizado para nuvem e borda e funciona em Linux, Windows e Mac. Escrito em C++, ele também tem APIs C, Python e C#. O ONNX Runtime oferece suporte para todas as especificações ONNX-ML e também se integra com aceleradores em diferentes hardwares, como tensorRT em GPUs NVidia.

O ONNX Runtime é usado em serviços da Microsoft de alta escala, como Bing, Office e Serviços Cognitivos. Os ganhos de desempenho dependem de uma série de fatores, mas esses serviços da Microsoft viram um __ganho médio de desempenho de 2x na CPU__. O ONNX Runtime também é usado como parte do Windows ML em centenas de milhões de dispositivos. Você pode usar o tempo de execução com o Azure Machine Learning. Ao usar o ONNX Runtime, você pode se beneficiar das extensas otimizações de nível de produção, testes e melhorias contínuas.

[![Diagrama de fluxo ONNX mostrando treinamento, conversores e implantação](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obter modelos ONNX

Você pode obter modelos ONNX de várias maneiras:
+ Treine um novo modelo ONNX no Azure Machine Learning (veja exemplos na parte inferior deste artigo)
+ Converter o modelo existente de outro formato para ONNX (veja os [tutoriais)](https://github.com/onnx/tutorials) 
+ Obtenha um modelo ONNX pré-treinado do [ONNX Model Zoo](https://github.com/onnx/models) (veja exemplos na parte inferior deste artigo)
+ Gerar um modelo ONNX personalizado do [Serviço de Visão Personalizada do Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Muitos modelos, incluindo classificação de imagem, detecção de objetos e processamento de texto podem ser representados como modelos ONNX. No entanto, alguns modelos podem não ser capazes de ser convertidos com sucesso. Se você encontrar essa situação, por favor, registre um problema no GitHub do respectivo conversor que você usou. Você pode continuar usando seu modelo de formato existente até que o problema seja resolvido.

## <a name="deploy-onnx-models-in-azure"></a>Implantar modelos do ONNX no Azure

Com o Azure Machine Learning, você pode implantar, gerenciar e monitorar seus modelos ONNX. Usando o [fluxo de trabalho de implantação](concept-model-management-and-deployment.md) padrão e o ONNX Runtime, é possível criar um ponto de extremidade REST hospedado na nuvem. Veja os cadernos Jupyter no final deste artigo para experimentá-lo por si mesmo. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instale e use o ONNX Runtime com Python

Os pacotes Python para ONNX Runtime estão disponíveis em [PyPi.org](https://pypi.org) [(CPU](https://pypi.org/project/onnxruntime), [GPU).](https://pypi.org/project/onnxruntime-gpu) Leia os [requisitos do sistema](https://github.com/Microsoft/onnxruntime#system-requirements) antes da instalação. 

 Para instalar o ONNX Runtime para Python, use um dos seguintes comandos: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Para chamar o ONNX Runtime no script do Python, use:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

A documentação que acompanha o modelo geralmente informa as entradas e saídas para usar o modelo. Também é possível usar uma ferramenta de visualização como o [Netron](https://github.com/lutzroeder/Netron) para exibir o modelo. O Runtime do ONNX também permite consultar os metadados do modelo, as entradas e as saídas:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Para fazer inferências no modelo, use `run` e passe a lista de saídas que deseja receber (deixe em branco se quiser todas) e um mapa dos valores de entrada. O resultado é uma lista das saídas.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Para obter a referência completa da API do Python, consulte os [documentos de referência do ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exemplos

Consulte [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx), por exemplo, notebooks que criam e implantam modelos ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Obter mais informações

Saiba mais sobre ONNX ou colabore com o projeto:
+ [Site do projeto ONNX](https://onnx.ai)
+ [Código do ONNX no GitHub](https://github.com/onnx/onnx)

Saiba mais sobre o ONNX Runtime ou contribua para o projeto:
+ [Repositório GitHub do ONNX Runtime](https://github.com/Microsoft/onnxruntime)


