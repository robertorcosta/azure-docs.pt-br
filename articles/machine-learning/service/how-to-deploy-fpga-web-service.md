---
title: O que são FPGA – como implantar
titleSuffix: Azure Machine Learning
description: Saiba como implantar um serviço Web com um modelo em execução em um FPGA com Azure Machine Learning para inferência de latência extremamente baixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: 9c3c844ba7044f8e1c9c313f1ac63b94310ea322
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350543"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>O que são FPGA (matrizes de portão programável por campo) e como implantar

Este artigo fornece uma introdução às FPGA (matrizes de portão programável por campo) e mostra como implantar seus modelos usando Azure Machine Learning em um FPGA do Azure. 

As FPGAs contêm uma matriz de blocos lógicos programáveis e uma hierarquia de interconexões reconfiguráveis. As interconexões permitem que esses blocos sejam configurados de várias maneiras após a fabricação. Comparado a outros chips, as FPGAs fornecem uma combinação de programação e desempenho.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. CPU, GPU e ASIC

O diagrama e a tabela a seguir mostram uma comparação das FPGAs com outros processadores.

![Diagrama de Azure Machine Learning comparação de FPGA](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processador||Descrição|
|---|:-------:|------|
|Circuitos integrados específicos do aplicativo|ASICs|Circuitos personalizados, como as Unidades de Processador (TPU) do TensorFlow da Google, fornecem a maior eficiência. Elas não podem ser reconfiguradas conforme suas necessidades mudam.|
|Matrizes de portas programáveis em campo|FPGAs|As FPGAs, como aquelas disponíveis no Azure, fornecem desempenho aproximado ao dos ASICs. Eles também são flexíveis e reconfiguráveis ao longo do tempo, a fim de implementar a nova lógica.|
|Unidades de processamento gráfico|GPUs|Uma opção popular para cálculos de IA. As GPUs oferecem funcionalidades de processamento paralelo, tornando-as mais rápidas na renderização de imagens comparado às CPUs.|
|Unidades de processamento central|CPUs|Processadores de uso geral cujo desempenho não é ideal para processamento de elementos gráficos e vídeo.|

O FPGAs no Azure baseia-se nos dispositivos FPGA da Intel, que os cientistas de dados e os desenvolvedores usam para acelerar os cálculos de AI em tempo real. Essa arquitetura habilitada para FPGA oferece desempenho, flexibilidade e escala e está disponível no Azure.

O FPGAs possibilita a obtenção de baixa latência para solicitações de inferência em tempo real (ou pontuação de modelo). As solicitações assíncronas (envio em lote) não são necessárias. O envio em lote pode causar latência, porque mais dados precisam ser processados. As implementações de unidades de processamento neural não exigem envio em lote; Portanto, a latência pode ser muitas vezes menor, em comparação com processadores de CPU e GPU.

### <a name="reconfigurable-power"></a>Potência reconfigurável
Reconfigure as FPGAs para diferentes tipos de modelos de machine learning. Essa flexibilidade torna mais fácil acelerar os aplicativos baseados na precisão numérica mais ideal e o modelo de memória que está sendo usado. Como as FPGAs são reconfiguráveis, você pode ficar atualizado com os requisitos dos algoritmos de IA em rápida mudança.

## <a name="whats-supported-on-azure"></a>O que tem suporte no Azure
O Microsoft Azure é o maior investimento de nuvem do mundo em FPGAs. Usando essa arquitetura de hardware habilitado para FPGA, as redes neurais treinadas são executadas rapidamente e com uma latência mais baixa. O Azure pode paralelizar DNN (redes neurais profundas) pré-instaladas em FPGAs para escalar horizontalmente seu serviço. As DNNs podem ser pré-treinadas, como um transformador de recursos profundo para transferência de aprendizado, ou ajustadas com pesos atualizados.

O FPGAs no Azure dá suporte a:

+ Cenários de classificação e reconhecimento de imagens
+ Implantação do TensorFlow
+ Hardware de FPGA da Intel 

Esses modelos de DNN estão disponíveis no momento:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGAs estão disponíveis nessas regiões do Azure:
  - East US
  - Sudeste da Ásia
  - Europa Ocidental
  - Oeste dos EUA 2

> [!IMPORTANT]
> Para otimizar a latência e a taxa de transferência, o cliente que está enviando dados para o modelo FPGA deve estar em uma das regiões acima (aquela em que você implantou o modelo).

A **família PBS de VMs do Azure** contém o Intel Arria 10 FPGAs. Ele será mostrado como "Standard PBS Family vCPUs" quando você verificar sua alocação de cota do Azure. A VM PB6 tem seis vCPUs e uma FPGA, e ela será automaticamente provisionada pelo Azure ML como parte da implantação de um modelo em um FPGA. Ele só é usado com o Azure ML e não pode executar fragmentado arbitrários. Por exemplo, você não poderá atualizar o FPGA com fragmentado para fazer criptografia, codificação, etc.

### <a name="scenarios-and-applications"></a>Cenários e aplicativos

Os FPGAs do Azure são integrados com o Azure Machine Learning. A Microsoft usa FPGAs para avaliação de DNN, classificação da Pesquisa do Bing e aceleração de SDN (rede definida por software) para reduzir a latência durante a liberação de CPUs para outras tarefas.

Os cenários a seguir usam FPGAs:
+ [Sistema automatizado de inspeção ótica](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapeamento de cobertura de terra](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)



## <a name="example-deploy-models-on-fpgas"></a>Exemplo: Implantar modelos em FPGAs 

Você pode implantar um modelo como um serviço Web no FPGAs com Azure Machine Learning Modelos de Aceleração de Hardware. O uso de FPGAs fornece inferência de latência ultra baixa, mesmo com um único tamanho de lote. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção.


### <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.  Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Cota de FPGA. Use o CLI do Azure para verificar se você tem cota:

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Os outros locais possíveis são ``southeastasia``, ``westeurope``e ``westus2``.

    O comando retorna um texto semelhante ao seguinte:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Verifique se você tem pelo menos 6 vCPUs em __CurrentValue__.

    Se você não tiver cota, envie uma solicitação em [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI).

- Um workspace do Azure Machine Learning e o SDK de aprendizado de máquina do Azure para Python instalado. Para obter mais informações, consulte [criar um espaço de trabalho](how-to-manage-workspace.md).
 
- O SDK do Python para modelos com aceleração de hardware:

    ```shell
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Criar e colocar em contêineres modelos

Este documento descreverá como criar um grafo TensorFlow para pré-processar a imagem de entrada, torná-la um featurizer usando ResNet 50 em um FPGA e, em seguida, executar os recursos por meio de um classificador treinado no conjunto de dados ImageNet.

Siga as instruções para:

* Definir o modelo TensorFlow
* Converter o modelo
* Implantar o modelo
* Consumir o modelo implantado
* Excluir serviços implantados

Use o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) para criar uma definição de serviço. Uma definição de serviço é um arquivo que descreve um pipeline de grafos (entrada, recursos e classificador) com base no TensorFlow. O comando de implantação compacta automaticamente a definição e os grafos em um arquivo zip e carrega o zip no Armazenamento de Blobs do Azure. O DNN já está implantado para ser executado no FPGA.

### <a name="load-azure-ml-workspace"></a>Carregar espaço de trabalho do Azure ML

Carregue seu espaço de trabalho do Azure ML.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Imagem de pré-processamento

A entrada para o serviço Web é uma imagem JPEG.  A primeira etapa é decodificar a imagem JPEG e pré-processar-la.  As imagens JPEG são tratadas como cadeias de caracteres e o resultado são os tempos que serão a entrada para o modelo ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Carregar featurizer

Inicialize o modelo e baixe um ponto de verificação TensorFlow da versão quantizada do ResNet50 para ser usado como um featurizer.  Você pode substituir "QuantizedResnet50" no trecho de código abaixo pelo importando outras redes neurais profundas:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Adicionar classificador

Este classificador foi treinado no conjunto de dados do ImageNet.  Exemplos de aprendizado de transferência e treinamento de seus pesos personalizados estão disponíveis no conjunto de [blocos de anotações de exemplo](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Salvar o modelo

Agora que o pré-processador, ResNet 50 featurizer e o classificador foram carregados, salve o grafo e as variáveis associadas como um modelo.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Economizar tempos de entrada e de saída
Os tempos de entrada e de saída que foram criados durante o pré-processamento e as etapas do classificador serão necessários para a conversão e a inferência de modelos.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Salve os tempos de entrada e saída, pois você precisará deles para a conversão de modelo e solicitações de inferência.

Os modelos disponíveis e os tempos de saída do classificador padrão correspondentes estão abaixo, que é o que você usaria para a inferência se você usou o classificador padrão.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registrar modelo

[Registre](./concept-model-management-and-deployment.md) o modelo usando o SDK com o arquivo zip no armazenamento de BLOBs do Azure. Adicionar marcas e outros metadados sobre o modelo ajuda você a controlar seus modelos treinados.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Se você já registrou um modelo e deseja carregá-lo, você pode recuperá-lo.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Converter modelo

Converta o grafo TensorFlow para o formato de troca de rede neural aberto ([ONNX](https://onnx.ai/)).  Você precisará fornecer os nomes dos tempos de entrada e de saída, e esses nomes serão usados pelo cliente quando você consumir o serviço Web.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Criar imagem do Docker

O modelo convertido e todas as dependências são adicionados a uma imagem do Docker.  Essa imagem do Docker pode ser implantada e instanciada.  Os destinos de implantação com suporte incluem AKS na nuvem ou em um dispositivo de borda, como [Azure data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Você também pode adicionar marcas e descrições para sua imagem do Docker registrada.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Liste as imagens por marca e obtenha os logs detalhados para qualquer depuração.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Implantar na nuvem ou no Microsoft Edge

### <a name="deploy-to-the-cloud"></a>Implantar na nuvem

Para implantar o modelo como um serviço Web de produção em larga escala, use o AKS (Serviço de Kubernetes do Azure). Você pode criar um novo usando o SDK do Azure Machine Learning, a CLI, a página de aterrissagem do [portal do Azure](https://portal.azure.com) ou do [espaço de trabalho (versão prévia)](https://ml.azure.com).

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2”. If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

A implantação do AKS pode levar cerca de 15 minutos.  Verifique se a implantação foi bem-sucedida.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Implante o contêiner no cluster AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Testar o serviço de nuvem
A imagem do Docker dá suporte a gRPC e TensorFlow que atende à API "Predict".  Use o cliente de exemplo para chamar a imagem do Docker para obter previsões do modelo.  O código de cliente de exemplo está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se você quiser usar o TensorFlow servindo, você pode [baixar um cliente de exemplo](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Como esse classificador foi treinado no conjunto de dados [ImageNet](http://www.image-net.org/) , mapeie as classes para rótulos legíveis.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Limpar o serviço
Exclua o serviço Web, a imagem e o modelo (deve ser feito nesta ordem, pois há dependências).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Implantar em um servidor de borda local

Todos [os dispositivos](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) Azure data Box Edge contêm um FPGA para executar o modelo.  Somente um modelo pode ser executado em FPGA ao mesmo tempo.  Para executar um modelo diferente, basta implantar um novo contêiner. As instruções e o código de exemplo podem ser encontrados neste [exemplo do Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Proteger serviços web FPGA

Para proteger seus serviços Web do FPGA, consulte o documento [Secure Web Services](how-to-secure-web-service.md) .

## <a name="next-steps"></a>Próximas etapas

Confira estes blocos de anotações, vídeos e Blogs:

+ Vários [blocos de anotações de exemplo](https://aka.ms/aml-accel-models-notebooks).

+ [Hardware da Hiperescala: ML em escala no Azure + FPGA: Build 2018 (vídeo)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Por dentro da nuvem configurável baseada em FPGA da Microsoft (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projeto Brainwave para IA em tempo real: home page do projeto](https://www.microsoft.com/research/project/project-brainwave/)
