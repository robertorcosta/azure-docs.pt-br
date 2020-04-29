---
title: Sobre ambientes de Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Neste artigo, Aprenda as vantagens dos ambientes de aprendizado de máquina, que permitem definições de dependência de aprendizado de máquina reproduzíveis, auditáveis e portáteis em uma variedade de destinos de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064203"
---
# <a name="what-are-azure-machine-learning-environments"></a>O que são ambientes Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Os ambientes de Azure Machine Learning especificam os pacotes python, as variáveis de ambiente e as configurações de software em relação aos scripts de treinamento e pontuação. Eles também especificam tempos de execução (Python, Spark ou Docker). Os ambientes são entidades gerenciadas e com controle de versão dentro de seu espaço de trabalho de Machine Learning que permitem a reproduzidas, a auditoria e o aprendizado de produção de máquinas portáteis em uma variedade de destinos de computação.

Você pode usar um `Environment` objeto em sua computação local para:
* Desenvolva seu script de treinamento.
* Reutilize o mesmo ambiente em Azure Machine Learning computação para treinamento de modelo em escala.
* Implante seu modelo com o mesmo ambiente.

O diagrama a seguir ilustra como você pode usar um `Environment` único objeto em sua configuração de execução, para treinamento e sua configuração de inferência e implantação, para implantações de serviço Web.

![Diagrama de um ambiente no fluxo de trabalho do Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem ser amplamente divididos em três categorias: *organizadas*, *gerenciadas pelo usuário*e *gerenciadas pelo sistema*.

Os ambientes organizados são fornecidos por Azure Machine Learning e estão disponíveis em seu espaço de trabalho por padrão. Eles contêm coleções de pacotes e configurações do Python para ajudá-lo a começar com várias estruturas do Machine Learning. 

Em ambientes gerenciados pelo usuário, você é responsável por configurar seu ambiente e instalar cada pacote de que seu script de treinamento precisa no destino de computação. O Conda não verifica seu ambiente nem instala nada para você. Se você estiver definindo seu próprio ambiente, deverá listar `azureml-defaults` com a `>= 1.0.45` versão como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web.

Você usa ambientes gerenciados pelo sistema quando deseja que o [Conda](https://conda.io/docs/) gerencie o ambiente do Python e as dependências de script para você. O serviço assume esse tipo de ambiente por padrão, devido à sua utilidade em destinos de computação remota que não são configuráveis manualmente.

## <a name="create-and-manage-environments"></a>Criar e gerenciar ambientes

Você pode criar ambientes:

* Definir novos `Environment` objetos, seja usando um ambiente organizado ou definindo suas próprias dependências.
* Usando objetos `Environment` existentes do seu espaço de trabalho. Essa abordagem permite a consistência e a reprodução com suas dependências.
* Importando de uma definição de ambiente Anaconda existente.
* Usando a CLI do Azure Machine Learning

Para obter exemplos de código específicos, consulte a seção "criar um ambiente" de [reutilizar ambientes para treinamento e implantação](how-to-use-environments.md#create-an-environment). Os ambientes também são facilmente gerenciados por meio de seu espaço de trabalho. Eles incluem a seguinte funcionalidade:

* Os ambientes são automaticamente registrados em seu espaço de trabalho quando você envia um experimento. Eles também podem ser registrados manualmente.
* Você pode buscar ambientes do seu espaço de trabalho para usar para treinamento ou implantação ou para fazer edições na definição do ambiente.
* Com o controle de versão, você pode ver as alterações em seus ambientes ao longo do tempo, o que garante reprodução.
* Você pode criar imagens do Docker automaticamente de seus ambientes.

Para obter exemplos de código, consulte a seção "gerenciar ambientes" de [reutilizar ambientes para treinamento e implantação](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Criação de ambiente, cache e reutilização

O serviço Azure Machine Learning cria definições de ambiente em imagens do Docker e ambientes Conda. Ele também armazena em cache os ambientes para que eles possam ser reutilizados em execuções de treinamento subsequentes e implantações de ponto de extremidade de serviço.

### <a name="building-environments-as-docker-images"></a>Criando ambientes como imagens do Docker

Normalmente, quando você envia uma execução pela primeira vez usando um ambiente, o serviço de Azure Machine Learning invoca uma [tarefa de compilação ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) no registro de contêiner do Azure (ACR) associado ao espaço de trabalho. Em seguida, a imagem do Docker criada é armazenada em cache no ACR do espaço de trabalho. No início da execução de execução, a imagem é recuperada pelo destino de computação.

A compilação da imagem consiste em duas etapas:

 1. Baixando uma imagem base e executando qualquer etapa do Docker
 2. Criar um ambiente Conda de acordo com as dependências Conda especificadas na definição de ambiente.

A segunda etapa será omitida se você especificar [dependências gerenciadas pelo usuário](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). Nesse caso, você é responsável por instalar qualquer pacote do Python, incluindo-os em sua imagem base ou especificando etapas personalizadas do Docker na primeira etapa. Você também é responsável por especificar o local correto para o executável do Python.

### <a name="image-caching-and-reuse"></a>Cache e reutilização de imagens

Se você usar a mesma definição de ambiente para outra execução, o serviço de Azure Machine Learning reutiliza a imagem armazenada em cache do ACR do espaço de trabalho. 

Para exibir os detalhes de uma imagem armazenada em cache, use o método [Environment. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) .

Para determinar se deve reutilizar uma imagem armazenada em cache ou criar uma nova, o serviço computa [um valor de hash](https://en.wikipedia.org/wiki/Hash_table) da definição do ambiente e o compara com os hashes dos ambientes existentes. O hash se baseia em:
 
 * Valor da propriedade da imagem base
 * Valor da propriedade de etapas do Docker personalizado
 * Lista de pacotes do Python na definição de Conda
 * Lista de pacotes na definição do Spark 

O hash não depende do nome do ambiente ou da versão. Alterações de definição de ambiente, como adicionar ou remover um pacote Python ou alterar a versão do pacote, fazem com que o valor de hash seja alterado e dispare uma recompilação de imagem. No entanto, se você simplesmente renomear seu ambiente ou criar um novo ambiente com as propriedades e os pacotes exatos de um existente, o valor de hash permanecerá o mesmo e a imagem armazenada em cache será usada.

Consulte o diagrama a seguir que mostra três definições de ambiente. Dois deles têm um nome e uma versão diferentes, mas uma imagem de base idêntica e pacotes python. Eles têm o mesmo hash e, portanto, correspondem à mesma imagem armazenada em cache. O terceiro ambiente tem diferentes pacotes e versões do Python e, portanto, corresponde a uma imagem armazenada em cache diferente.

![Diagrama de cache de ambiente como imagens do Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Se você criar um ambiente com uma dependência de pacote desafixada, ```numpy```por exemplo, esse ambiente continuará usando a versão do pacote instalada _no momento da criação do ambiente_. Além disso, qualquer ambiente futuro com definição correspondente continuará usando a versão antiga. 

Para atualizar o pacote, especifique um número de versão para forçar a recompilação da ```numpy==1.18.1```imagem, por exemplo. Observe que são instaladas novas dependências, incluindo aquelas aninhadas que podem interromper um cenário de trabalho anterior.

> [!WARNING]
>  O método [Environment. Build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) recriará a imagem armazenada em cache, com possível efeito colateral de atualização de pacotes desafixados e interrupção de reprodução para todas as definições de ambiente correspondentes à imagem armazenada em cache.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar e usar ambientes](how-to-use-environments.md) no Azure Machine Learning.
* Consulte a documentação de referência do SDK do Python para a [classe de ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Consulte a documentação de referência do SDK do R para [ambientes](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
