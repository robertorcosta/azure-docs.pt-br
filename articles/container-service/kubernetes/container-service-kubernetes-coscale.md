---
title: (PRETERIDO) Monitorar um cluster Kubernetes do Azure com o CoScale
description: Monitorar um cluster Kubernetes no Serviço de Contêiner do Azure usando CoScale
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f195a5c05c6c95dac898b2d471747952a3446d52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81681728"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(PRETERIDO) Monitorar um cluster Kubernetes do Serviço de Contêiner do Azure com o CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste artigo, mostramos como implantar o agente [CoScale](https://web.archive.org/web/20180317071550/https://www.coscale.com/) para monitorar todos os nós e contêineres em seu cluster Kubernetes do Serviço de Contêiner do Azure. Você precisa de uma conta com CoScale para essa configuração. 


## <a name="about-coscale"></a>Sobre o CoScale 

CoScale é uma plataforma de monitoramento que reúne as métricas e eventos de todos os contêineres em várias plataformas de orquestração. CoScale oferece monitoramento de pilha completa para ambientes Kubernetes. Ele fornece visualizações e análise para todas as camadas da pilha: sistema operacional, Kubernetes, Docker e aplicativos executados dentro de seu contêiner. O CoScale oferece vários painéis de monitoramentos internos e conta com detecção integrada de anomalias, a fim de permitir que operadores e desenvolvedores encontrem rapidamente os problemas de infraestrutura e de aplicativos.

![Interface do usuário do CoScale](./media/container-service-kubernetes-coscale/coscale.png)

Conforme mostra este artigo, você pode instalar agentes em um cluster Kubernetes para executar o CoScale como uma solução SaaS. Se você quiser manter seus dados no local, o CoScale também está disponível para instalação local.


## <a name="prerequisites"></a>Pré-requisitos

Primeiro, você precisa [criar uma conta do CoScale](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

Este passo a passo presume que você tenha [criado um cluster Kubernetes usando o Serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Isso também pressupõe que você tenha as ferramentas `az` da CLI do Azure e `kubectl`.

Você pode testar se tem a ferramenta `az` instalada executando:

```azurecli
az --version
```

Se você não tem a ferramenta `az` instalada, há instruções [aqui](/cli/azure/install-azure-cli).

Você pode testar se tem a ferramenta `kubectl` instalada executando:

```bash
kubectl version
```

Se não tem `kubectl` instalado, você pode executar:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalar o agente do CoScale com um DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) são usados pelo kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento, como o agente do CoScale.

Depois de fazer logon no CoScale, acesse a [página do agente](https://developer.newrelic.com/) para instalar agentes do CoScale em seu cluster usando um DaemonSet. A interface do usuário do CoScale fornece etapas de configuração interativas para criar um agente e começar a monitorar todo seu cluster Kubernetes.

![Configuração do agente do CoScale](./media/container-service-kubernetes-coscale/installation.png)

Para iniciar o agente no cluster, execute o comando fornecido:

![Iniciar o agente CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

É isso! Quando seus agentes estiverem em execução, você deverá ver os dados no console em alguns minutos. Visite a [página do agente](https://developer.newrelic.com/) para ver um resumo de seu cluster, execute etapas adicionais de configuração e veja painéis, por exemplo, **Visão geral do cluster Kubernetes**.

![Visão geral do cluster Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

O agente do CoScale é implantado automaticamente em novas máquinas no cluster. O agente é atualizado automaticamente quando uma nova versão é lançada.


## <a name="next-steps"></a>Próximas etapas

Consulte a [documentação do CoScale](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) e [blog](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) para obter mais informações sobre soluções de monitoramento CoScale. 

