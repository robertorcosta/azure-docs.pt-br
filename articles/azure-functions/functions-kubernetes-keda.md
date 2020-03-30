---
title: Funções do Azure em Kubernetes com KEDA
description: Entenda como executar funções do Azure em Kubernetes na nuvem ou no local usando o KEDA, autoscaling baseado em eventos baseado em Kubernetes.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301668"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funções do Azure em Kubernetes com KEDA

O tempo de execução do Azure Functions oferece flexibilidade na hospedagem onde e como você deseja.  [O KEDA](https://keda.sh) (Event Driven Autoscaling, baseado em Kubernetes) emparelha-se perfeitamente com o tempo de execução e ferramentas do Azure Functions para fornecer escala orientada a eventos em Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Como funcionam as funções baseadas em Kubernetes

O serviço Funções do Azure é composto por dois componentes-chave: um tempo de execução e um controlador de escala.  O tempo de execução functions é executado e executa seu código.  O tempo de execução inclui a lógica de como ativar, registrar e gerenciar execuções de funções.  O tempo de execução do Azure Functions pode ser executado *em qualquer lugar*.  O outro componente é um controlador de escala.  O controlador de escala monitora a taxa de eventos que estão direcionando sua função e dimensiona proativamente o número de instâncias que executam seu aplicativo.  Para saber mais, confira [Escala e hospedagem do Azure Functions](functions-scale.md).

Funções baseadas em Kubernetes fornecem o tempo de execução functions em um [contêiner Docker](functions-create-function-linux-custom-image.md) com dimensionamento orientado por eventos através do KEDA.  O KEDA pode dimensionar em 0 instâncias (quando não ocorrem eventos) e em *n* instâncias. Ele faz isso expondo métricas personalizadas para o autoscaler Kubernetes (Horizontal Pod Autoscaler).  O uso de recipientes Functions com KEDA permite replicar recursos de função sem servidor em qualquer cluster Kubernetes.  Essas funções também podem ser implantadas usando o recurso [de nãodos virtuais Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) para infra-estrutura sem servidor.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gerenciamento de KEDA e funções em Kubernetes

Para executar funções no cluster Kubernetes, você deve instalar o componente KEDA. Você pode instalar este componente usando [as ferramentas principais do Azure Functions](functions-run-local.md).

### <a name="installing-with-helm"></a>Instalando com helm

Existem várias maneiras de instalar keda em qualquer cluster Kubernetes, incluindo Helm.  As opções de implantação estão documentadas no [site keda](https://keda.sh/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Implantando um aplicativo de função para kubernetes

Você pode implantar qualquer aplicativo de função em um cluster Kubernetes executando o KEDA.  Uma vez que suas funções são executadas em um contêiner Docker, seu projeto precisa de um `Dockerfile`.  Se ele ainda não tiver um, você pode adicionar um arquivo Docker executando o seguinte comando na raiz do projeto Funções:

```cli
func init --docker-only
```

Para construir uma imagem e implantar suas funções no Kubernetes, execute o seguinte comando:

> [!NOTE]
> As Ferramentas Principais aproveitarão a CLI docker para construir e publicar a imagem. Certifique-se de ter o docker já `docker login`instalado e conectado à sua conta com .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Substitua `<name-of-function-deployment>` pelo nome do aplicativo de funções.

Isso cria um recurso `Deployment` Kubernetes, um `ScaledObject` recurso e `Secrets`, que `local.settings.json` inclui variáveis de ambiente importadas do seu arquivo.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Implantando um aplicativo de função a partir de um registro privado

O fluxo acima funciona para registros privados também.  Se você estiver retirando sua imagem de `--pull-secret` contêiner de um registro privado, inclua a bandeira `func kubernetes deploy`que faz referência ao segredo kubernetes que mantém as credenciais de registro privado ao executar .

## <a name="removing-a-function-app-from-kubernetes"></a>Removendo um aplicativo de função do Kubernetes

Depois de implantado, você pode remover `Deployment`uma `ScaledObject`função `Secrets` removendo o associado , um criado.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalação de KEDA de Kubernetes

As etapas para desinstalar o KEDA estão documentadas [no site keda](https://keda.sh/deploy/).

## <a name="supported-triggers-in-keda"></a>Gatilhos suportados no KEDA

O KEDA tem suporte para os seguintes gatilhos da função Azure:

* [Filas de Armazenamento do Azure](functions-bindings-storage-queue.md)
* [Filas de ônibus de serviço do Azure](functions-bindings-service-bus.md)
* [Azure Event / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Fila rabbitmq](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Suporte ao gatilho HTTP

Você pode usar funções do Azure que expõem gatilhos HTTP, mas o KEDA não os gerencia diretamente.  Você pode aproveitar o gatilho prometheus KEDA para [dimensionar as funções HTTP Azure de 1 a *n* instâncias](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Crie uma função usando uma imagem personalizada](functions-create-function-linux-custom-image.md)
* [Codificar e testar o Azure Functions localmente](functions-develop-local.md)
* [Como funciona o plano de consumo de funções do Azure](functions-scale.md)