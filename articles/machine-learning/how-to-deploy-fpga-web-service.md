---
title: O que são FPGA - como implantar
titleSuffix: Azure Machine Learning
description: Aprenda a implantar um serviço web com um modelo em execução em um FPGA com o Azure Machine Learning para inferência de latência ultra-baixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8cb6cf49e302122849dc2402bcff008e72e15608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472351"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>O que são matrizes de portão programáveis em campo (FPGA) e como implantar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo fornece uma introdução aos arrays de portão programáveis em campo (FPGA) e mostra como implantar seus modelos usando o Azure Machine Learning para um Azure FPGA.

As FPGAs contêm uma matriz de blocos lógicos programáveis e uma hierarquia de interconexões reconfiguráveis. As interconexões permitem que esses blocos sejam configurados de várias maneiras após a fabricação. Comparado a outros chips, as FPGAs fornecem uma combinação de programação e desempenho.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. CPU, GPU e ASIC

O diagrama e a tabela a seguir mostram uma comparação das FPGAs com outros processadores.

![Diagrama de comparação FPGA de Aprendizagem de Máquina do Azure](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processador||Descrição|
|---|:-------:|------|
|Circuitos integrados específicos do aplicativo|ASICs|Circuitos personalizados, como as Unidades de Processador (TPU) do TensorFlow da Google, fornecem a maior eficiência. Elas não podem ser reconfiguradas conforme suas necessidades mudam.|
|Matrizes de portas programáveis em campo|FPGAs|As FPGAs, como aquelas disponíveis no Azure, fornecem desempenho aproximado ao dos ASICs. Eles também são flexíveis e reconfiguráveis ao longo do tempo, a fim de implementar a nova lógica.|
|Unidades de processamento gráfico|GPUs|Uma opção popular para cálculos de IA. As GPUs oferecem funcionalidades de processamento paralelo, tornando-as mais rápidas na renderização de imagens comparado às CPUs.|
|Unidades de processamento central|CPUs|Processadores de uso geral cujo desempenho não é ideal para processamento de elementos gráficos e vídeo.|

Os FPGAs no Azure são baseados nos dispositivos FPGA da Intel, que cientistas de dados e desenvolvedores usam para acelerar os cálculos de IA em tempo real. Essa arquitetura habilitada para FPGA oferece desempenho, flexibilidade e escala e está disponível no Azure.

Os FPGAs possibilitam a realização de baixa latência para solicitações de inferência (ou pontuação de modelo) em tempo real. As solicitações assíncronas (envio em lote) não são necessárias. O envio em lote pode causar latência, porque mais dados precisam ser processados. Implementações de unidades de processamento neural não requerem loteamento; portanto, a latência pode ser muitas vezes menor, em comparação com processadores de CPU e GPU.

### <a name="reconfigurable-power"></a>Potência reconfigurável
Reconfigure as FPGAs para diferentes tipos de modelos de machine learning. Essa flexibilidade torna mais fácil acelerar os aplicativos baseados na precisão numérica mais ideal e o modelo de memória que está sendo usado. Como as FPGAs são reconfiguráveis, você pode ficar atualizado com os requisitos dos algoritmos de IA em rápida mudança.

## <a name="whats-supported-on-azure"></a>O que tem suporte no Azure
O Microsoft Azure é o maior investimento de nuvem do mundo em FPGAs. Usando essa arquitetura de hardware habilitado para FPGA, as redes neurais treinadas são executadas rapidamente e com uma latência mais baixa. O Azure pode paralelamente a redes neurais profundas (DNN) pré-treinadas através de FPGAs para dimensionar seu serviço. As DNNs podem ser pré-treinadas, como um transformador de recursos profundo para transferência de aprendizado, ou ajustadas com pesos atualizados.

FPGAs no Azure suportam:

+ Cenários de classificação e reconhecimento de imagens
+ Implantação do TensorFlow
+ Hardware de FPGA da Intel

Estes modelos DNN estão atualmente disponíveis:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

Os FPGAs estão disponíveis nestas regiões do Azure:
  - Leste dos EUA
  - Sudeste Asiático
  - Europa Ocidental
  - Oeste dos EUA 2

> [!IMPORTANT]
> Para otimizar a latência e o throughput, o envio de dados do seu cliente para o modelo FPGA deve estar em uma das regiões acima (para a qual você implantou o modelo).

A **família PBS de VMs Azure** contém Intel Arria 10 FPGAs. Ele será exibido como "Standard PBS Family vCPUs" quando você verificar sua alocação de cotas do Azure. O PB6 VM tem seis vCPUs e um FPGA, e será automaticamente provisionado pelo Azure ML como parte da implantação de um modelo para um FPGA. Ele é usado apenas com OZure ML, e não pode executar bitstreams arbitrários. Por exemplo, você não será capaz de piscar o FPGA com bitstreams para fazer criptografia, codificação, etc.

### <a name="scenarios-and-applications"></a>Cenários e aplicativos

Os FPGAs do Azure são integrados ao Azure Machine Learning. A Microsoft usa FPGAs para avaliação de DNN, classificação da Pesquisa do Bing e aceleração de SDN (rede definida por software) para reduzir a latência durante a liberação de CPUs para outras tarefas.

Os seguintes cenários utilizam FPGAs:
+ [Sistema de inspeção óptica automatizado](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapeamento da cobertura da terra](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>Exemplo: Implantar modelos em FPGAs

Você pode implantar um modelo como um serviço web em FPGAs com modelos acelerados de hardware de aprendizado de máquina do Azure. O uso de FPGAs fornece inferência de latência ultra-baixa, mesmo com um único tamanho de lote. Inferência, ou pontuação do modelo, é a fase em que o modelo implantado é usado para predição, mais comumente em dados de produção.

### <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.  Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Cota FPGA. Use o Azure CLI para verificar se você tem cota:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Os outros locais ``southeastasia`` ``westeurope``possíveis ``westus2``são, e .

    O comando retorna texto semelhante ao seguinte:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Certifique-se de ter pelo menos 6 vCPUs em __CurrentValue__.

    Se você não tiver cota, envie [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)uma solicitação em .

- Um workspace do Azure Machine Learning e o SDK de aprendizado de máquina do Azure para Python instalado. Para obter mais informações, consulte [Criar um espaço de trabalho](how-to-manage-workspace.md).
 
- O Python SDK para modelos acelerados por hardware:

    ```bash
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Criar e contêiner modelos

Este documento descreverá como criar um gráfico TensorFlow para pré-processar a imagem de entrada, torná-la um featurizer usando o ResNet 50 em um FPGA e, em seguida, executar os recursos através de um classificador treinado no conjunto de dados ImageNet.

Siga as instruções para:

* Defina o modelo TensorFlow
* Converta o modelo
* Implantar o modelo
* Consumir o modelo implantado
* Excluir serviços implantados

Use o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) para criar uma definição de serviço. Uma definição de serviço é um arquivo que descreve um pipeline de grafos (entrada, recursos e classificador) com base no TensorFlow. O comando de implantação compacta automaticamente a definição e os grafos em um arquivo zip e carrega o zip no Armazenamento de Blobs do Azure. O DNN já está implantado para ser executado no FPGA.

### <a name="load-azure-machine-learning-workspace"></a>Espaço de trabalho de aprendizado de máquina do Azure load

Carregue seu espaço de trabalho de aprendizado de máquina do Azure.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Imagem de pré-processamento

A entrada para o serviço web é uma imagem JPEG.  O primeiro passo é decodificar a imagem JPEG e pré-processá-la.  As imagens JPEG são tratadas como strings e o resultado são tensores que serão a entrada para o modelo ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Carregador de featurizer

Inicialize o modelo e baixe um ponto de verificação TensorFlow da versão quantizada do ResNet50 para ser usado como um featurizer.  Você pode substituir "QuantizedResnet50" no trecho de código abaixo com a importação de outras redes neurais profundas:

- QuantizedResnet152
- QuantizedVgg16
- Densa121

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

Este classificador foi treinado no conjunto de dados do ImageNet.  Exemplos para aprendizado de transferência e treinamento seus pesos personalizados estão disponíveis no conjunto de [cadernos](https://aka.ms/aml-notebooks)de amostra .

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Salvar o modelo

Agora que o pré-processador, O Featurizer ResNet 50 e o classificador foram carregados, salve o gráfico e variáveis associadas como um modelo.

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

### <a name="save-input-and-output-tensors"></a>Salvar tensores de entrada e saída
Os tensores de entrada e saída que foram criados durante as etapas de pré-processamento e classificação serão necessários para conversão e inferência do modelo.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Salve os tensores de entrada e saída porque você precisará deles para solicitações de conversão de modelo e inferência.

Os modelos disponíveis e os tensores de saída do classificador padrão correspondentes estão abaixo, que é o que você usaria para inferência se você usasse o classificador padrão.

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
+ SsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registrar modelo

[Registre](concept-model-management-and-deployment.md) o modelo usando o SDK com o arquivo ZIP no armazenamento Azure Blob. Adicionar tags e outros metadados sobre o modelo ajuda você a acompanhar seus modelos treinados.

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

### <a name="convert-model"></a>Modelo de conversão

Converta o gráfico TensorFlow para o formato Open Neural Network Exchange[(ONNX).](https://onnx.ai/)  Você precisará fornecer os nomes dos tensores de entrada e saída, e esses nomes serão usados pelo seu cliente quando você consumir o serviço web.

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

O modelo convertido e todas as dependências são adicionados a uma imagem do Docker.  Esta imagem do Docker pode então ser implantada e instanciada.  Os alvos de implantação suportados incluem AKS na nuvem ou um dispositivo de borda, como [o Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Você também pode adicionar tags e descrições para sua imagem Docker registrada.

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

Liste as imagens por tag e obtenha os registros detalhados para qualquer depuração.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Implantar em nuvem ou borda

### <a name="deploy-to-the-cloud"></a>Implantar na nuvem

Para implantar o modelo como um serviço Web de produção em larga escala, use o AKS (Serviço de Kubernetes do Azure). Você pode criar um novo usando o estúdio Azure Machine Learning SDK, CLI ou [Azure Machine Learning](https://ml.azure.com).

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

A implantação do AKS pode levar cerca de 15 minutos.  Verifique se a implantação foi bem sucedida.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Implante o recipiente no cluster AKS.
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

#### <a name="test-the-cloud-service"></a>Teste o serviço de nuvem
A imagem Docker suporta gRPC e a API de "prever" do TensorFlow.  Use o cliente de amostra para chamar a imagem do Docker para obter previsões do modelo.  O código do cliente da amostra está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se você quiser usar o TensorFlow Serving, você pode [baixar um cliente de exemplo](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Uma vez que este classificador foi treinado no conjunto de dados [ImageNet,](http://www.image-net.org/) mapeie as classes para rótulos leíveis por humanos.

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

### <a name="clean-up-the-service"></a>Limpeza do serviço
Exclua seu serviço web, imagem e modelo (deve ser feito nesta ordem, pois existem dependências).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Implantar em um servidor de borda local

Todos os [dispositivos Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contêm um FPGA para executar o modelo.  Apenas um modelo pode estar sendo executado no FPGA ao mesmo tempo.  Para executar um modelo diferente, basta implantar um novo contêiner. Instruções e código de amostra podem ser encontrados [nesta amostra azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Proteger serviços web FPGA

Para proteger seus serviços web FPGA, consulte o documento [de serviços web Secure.](how-to-secure-web-service.md)

## <a name="next-steps"></a>Próximas etapas

Confira esses cadernos, vídeos e blogs:

+ Vários [cadernos de amostra](https://aka.ms/aml-accel-models-notebooks)

+ [Hardware de hiperescala: ML em escala no topo do Azure + FPGA: Build 2018 (vídeo)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Por dentro da nuvem configurável baseada em FPGA da Microsoft (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projeto Brainwave para IA em tempo real: home page do projeto](https://www.microsoft.com/research/project/project-brainwave/)
