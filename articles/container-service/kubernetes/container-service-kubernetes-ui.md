---
title: (PRETERIDO) Gerenciar o cluster Kubernetes do Azure com a interface do usuário da Web
description: Usando a interface do usuário da Web Kubernetes no Serviço de Contêiner do Azure
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371130"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(PRETERIDO) Usando a interface do usuário da Web DO Kubernetes com o Serviço de Contêiner do Azure

> [!TIP]
> Para a versão atualizada deste artigo que usa o Serviço de Kubernetes do Azure, confira [Acessar o painel da Web do Kubernetes no AKS (Serviço de Kubernetes do Azure)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo presume que você tenha [criado um cluster Kubernetes usando o Serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).


Isso também pressupõe que você tenha as ferramentas `kubectl` e da CLI do Azure instaladas.

Você pode testar se tem a ferramenta `az` instalada executando:

```azurecli
az --version
```

Se você não tem a ferramenta `az` instalada, há instruções [aqui](https://github.com/azure/azure-cli#installation).

Você pode testar se tem a ferramenta `kubectl` instalada executando:

```console
kubectl version
```

Se não tem `kubectl` instalado, você pode executar:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Visão geral

### <a name="connect-to-the-web-ui"></a>Conecte-se à interface do usuário da Web
Você pode iniciar a interface do usuário Web Kubernetes executando:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Isso deve abrir um navegador da Web configurado para comunicar-se com um proxy seguro conectando seu computador local à interface do usuário Web do Kubernetes.

### <a name="create-and-expose-a-service"></a>Criar e expor um serviço
1. Na interface do usuário da Web Kubernetes, clique no botão **Criar** na janela superior direita.

    ![Criar interface do usuário Kubernetes](./media/container-service-kubernetes-ui/create.png)

    Isso abrirá uma caixa de diálogo em que você poderá começar a criar o aplicativo.

2. Atribua o nome `hello-nginx`. Use [ `nginx` ](https://hub.docker.com/_/nginx/) o contêiner do Docker e implante três réplicas deste serviço web.

    ![Caixa de diálogo Criar Pod Kubernetes](./media/container-service-kubernetes-ui/nginx.png)

3. Em **Serviço**, selecione **Externo** e insira a porta 80.

    Essa configuração realiza o balanceamento de carga de tráfego para as três réplicas.

    ![Caixa de diálogo Criar serviço Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Clique em **Implantar** para implantar esses contêineres e serviços.

    ![Implantar Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Exibir seus contêineres
Depois que você pressionar **Implantar**, a interface do usuário mostrará uma exibição do serviço que ela implanta:

![Status do Kubernetes](./media/container-service-kubernetes-ui/status.png)

Você pode ver o status de cada objeto Kubernetes no círculo no lado esquerdo da interface do usuário, em **Pods**. Se for um círculo parcialmente cheio, o objeto ainda está sendo implantado. Quando um objeto é totalmente implantado, ele exibe uma marca de seleção verde:

![Kubernetes implantado](./media/container-service-kubernetes-ui/deployed.png)

Quando tudo estiver em execução, clique em um dos Pods para ver detalhes sobre o serviço Web em execução.

![Pods Kubernetes](./media/container-service-kubernetes-ui/pods.png)

Na exibição **Pods**, você pode ver informações sobre os contêineres no pod, bem como os recursos de CPU e memória usados por esses contêineres:

![Recursos do Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Se não encontrar os recursos, você precisará aguardar alguns minutos para propagar os dados de monitoramento.

Para ver os logs para o contêiner, clique em **Exibir logs**.

![Logs do Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Exibindo o serviço
Além de executar seus contêineres, a interface do usuário Kubernetes criou um `Service` externo que provisiona um balanceador de carga para trazer o tráfego para os contêineres no cluster.

No painel de navegação esquerdo, clique em **Serviços** para exibir todos os serviços (deve haver apenas um).

![Serviços Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

Nessa exibição, você deve ver um ponto de extremidade externo (endereço IP) que foi alocado para o serviço.
Se clicar no endereço IP, você deverá ver o contêiner nginx em execução por trás do balanceador de carga.

![nginx view](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Redimensionar o serviço
Além de exibir os objetos na interface do usuário, você também pode editar e atualizar os objetos da API Kubernetes.

Primeiro, clique em **Implantações** no painel de navegação à esquerda para ver a implantação do seu serviço.

Quando você estiver nessa exibição, clique no conjunto de réplicas e clique no botão **Editar** na barra de navegação superior:

![Editar Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Edite o campo `spec.replicas` para que seja `2` e clique em **Atualizar**.

Isso fará com que o número de réplicas diminua para duas por meio da exclusão de um de seus pods.

 

