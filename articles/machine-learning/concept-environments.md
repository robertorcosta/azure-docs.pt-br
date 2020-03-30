---
title: Sobre os ambientes de machine learning do Azure
titleSuffix: Azure Machine Learning
description: Neste artigo, conheça as vantagens dos ambientes de aprendizagem de máquina, que permitem definições de dependência de aprendizado de máquina reprodutíveis, auditáveis e portáteis em uma variedade de metas de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064203"
---
# <a name="what-are-azure-machine-learning-environments"></a>O que são ambientes de Machine Learning do Azure?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Os ambientes de aprendizado de máquina do Azure especificam os pacotes Python, variáveis de ambiente e configurações de software em torno de seus scripts de treinamento e pontuação. Eles também especificam tempos de execução (Python, Spark ou Docker). Os ambientes são gerenciados e versões entidades dentro do seu espaço de trabalho de Aprendizado de Máquina que permitem fluxos de trabalho reprodutíveis, auditáveis e portáteis de aprendizado de máquina em uma variedade de metas de computação.

Você pode `Environment` usar um objeto em sua computação local para:
* Desenvolva seu roteiro de treinamento.
* Reutilize o mesmo ambiente no Azure Machine Learning Compute para treinamento de modelo em escala.
* Implante seu modelo com o mesmo ambiente.

O diagrama a seguir ilustra `Environment` como você pode usar um único objeto tanto na configuração de execução, no treinamento quanto na configuração de inferência e implantação, para implantações de serviços web.

![Diagrama de um ambiente no fluxo de trabalho de aprendizado de máquina](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem ser amplamente divididos em três categorias: *curadoria,* *gerenciada pelo usuário*e *gerenciada pelo sistema.*

Os ambientes com curadoria são fornecidos pelo Azure Machine Learning e estão disponíveis em seu espaço de trabalho por padrão. Eles contêm coleções de pacotes Python e configurações para ajudá-lo a começar com várias estruturas de aprendizado de máquina. 

Em ambientes gerenciados pelo usuário, você é responsável por configurar seu ambiente e instalar todos os pacotes que seu script de treinamento precisa no alvo da computação. Conda não verifica seu ambiente ou instala nada para você. Se você está definindo seu próprio `azureml-defaults` ambiente, você deve listar com a versão `>= 1.0.45` como uma dependência de pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

Você usa ambientes gerenciados pelo sistema quando deseja que o [Conda](https://conda.io/docs/) gerencie o ambiente Python e as dependências de script para você. O serviço assume esse tipo de ambiente por padrão, devido à sua utilidade em alvos de computação remota que não são configuráveis manualmente.

## <a name="create-and-manage-environments"></a>Criar e gerenciar ambientes

Você pode criar ambientes por:

* Definindo `Environment` novos objetos, seja usando um ambiente curado ou definindo suas próprias dependências.
* Usando objetos existentes `Environment` do seu espaço de trabalho. Essa abordagem permite consistência e reprodutibilidade com suas dependências.
* Importando de uma definição de ambiente Anaconda existente.
* Usando o CLI de aprendizagem de máquina do Azure

Para obter amostras de código específicas, consulte a seção "Criar um ambiente" dos [ambientes reutilizar para treinamento e implantação](how-to-use-environments.md#create-an-environment). Os ambientes também são facilmente gerenciados através do seu espaço de trabalho. Eles incluem a seguinte funcionalidade:

* Os ambientes são automaticamente registrados no seu espaço de trabalho quando você envia um experimento. Eles também podem ser registrados manualmente.
* Você pode buscar ambientes do seu espaço de trabalho para usar para treinamento ou implantação, ou para fazer edições para a definição do ambiente.
* Com a versão, você pode ver mudanças em seus ambientes ao longo do tempo, o que garante a reprodutibilidade.
* Você pode construir imagens Docker automaticamente a partir de seus ambientes.

Para obter amostras de código, consulte a seção "Gerenciar ambientes" dos [ambientes reutilizar para treinamento e implantação](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Construção ambiental, cache e reutilização

O serviço de aprendizado de máquina do Azure constrói definições de ambiente em imagens Docker e ambientes conda. Ele também armazena os ambientes para que eles possam ser reutilizados em corridas de treinamento subseqüentes e implantações de ponto final de serviço.

### <a name="building-environments-as-docker-images"></a>Construindo ambientes como imagens docker

Normalmente, quando você envia uma execução pela primeira vez usando um ambiente, o serviço de aprendizado de máquina do Azure invoca uma [tarefa de construção de ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) no ACR (ACR) de contêiner do Azure associado ao Espaço de Trabalho. A imagem do Docker construída é então armazenada em cache no ACR do Workspace. No início da execução da execução, a imagem é recuperada pelo alvo da computação.

A compilação da imagem consiste em duas etapas:

 1. Baixando uma imagem base e executando quaisquer etapas do Docker
 2. Construção de um ambiente conda de acordo com as dependências de conda especificadas na definição do ambiente.

A segunda etapa é omitida se você especificar [dependências gerenciadas pelo usuário](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). Neste caso, você é responsável por instalar quaisquer pacotes Python, incluindo-os em sua imagem base ou especificando etapas personalizadas do Docker no primeiro passo. Você também é responsável por especificar o local correto para o executável Python.

### <a name="image-caching-and-reuse"></a>Cache de imagem e reutilização

Se você usar a mesma definição de ambiente para outra execução, o serviço azure Machine Learning reutiliza a imagem armazenada em cache do ACR do Workspace. 

Para visualizar os detalhes de uma imagem em cache, use o método [Environment.get_image_details.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)

Para determinar se é necessário reutilizar uma imagem armazenada em cache ou construir uma nova, o serviço calcula um valor de [hash](https://en.wikipedia.org/wiki/Hash_table) da definição do ambiente e a compara com os hashes dos ambientes existentes. O hash é baseado em:
 
 * Valor da propriedade da imagem base
 * Docker personalizado degraus valor de propriedade
 * Lista de pacotes Python na definição de Conda
 * Lista de pacotes na definição de Faísca 

O hash não depende do nome do ambiente ou da versão. As mudanças na definição do ambiente, como adicionar ou remover um pacote Python ou alterar a versão do pacote, fazem com que o valor de hash mude e aciona uma reconstrução de imagem. No entanto, se você simplesmente renomear seu ambiente ou criar um novo ambiente com as propriedades exatas e pacotes de um já existente, então o valor de hash permanece o mesmo e a imagem armazenada em cache é usada.

Veja o diagrama a seguir que mostra três definições de ambiente. Dois deles têm nome e versão diferentes, mas imagem base idêntica e pacotes Python. Eles têm o mesmo hash e, portanto, correspondem à mesma imagem armazenada em cache. O terceiro ambiente tem diferentes pacotes e versões Python e, portanto, corresponde a uma imagem diferente em cache.

![Diagrama do cache do ambiente como imagens docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Se você criar um ambiente com uma dependência ```numpy```de pacote não fixada, por exemplo, esse ambiente continuará usando a versão do pacote instalada _no momento da criação do ambiente_. Além disso, qualquer ambiente futuro com definição de correspondência continuará usando a versão antiga. 

Para atualizar o pacote, especifique um ```numpy==1.18.1```número de versão para forçar a reconstrução de imagens, por exemplo . Observe que novas dependências, incluindo as aninhadas, serão instaladas que podem quebrar um cenário de trabalho anterior.

> [!WARNING]
>  O método [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) reconstruirá a imagem em cache, com possível efeito colateral da atualização de pacotes não fixados e da reprodutibilidade de quebra de todas as definições de ambiente correspondentes a essa imagem armazenada em cache.

## <a name="next-steps"></a>Próximas etapas

* Aprenda a [criar e usar ambientes](how-to-use-environments.md) no Azure Machine Learning.
* Consulte a documentação de referência python SDK para a [classe de ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Consulte a documentação de referência R SDK para [ambientes](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
