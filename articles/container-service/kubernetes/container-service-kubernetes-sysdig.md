---
title: (PRETERIDO) Monitorar o cluster do Kubernetes do Azure - Sysdig
description: Monitoramento do cluster Kubernetes no Serviço de Contêiner do Azure usando Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371092"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(PRETERIDO) Monitorar um cluster do Kubernetes do Serviço de Contêiner do Azure usando Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo presume que você tenha [criado um cluster Kubernetes usando o Serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Isso também pressupõe que você tenha as ferramentas cli e kubectl do Azure instaladas.

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

## <a name="sysdig"></a>Sysdig
O Sysdig é um monitoramento externo como uma empresa de serviço que pode monitorar contêineres no cluster Kubernetes em execução no Azure. O uso do Sysdig requer uma conta ativa do Sysdig.
Você pode se inscrever para uma conta no [site](https://app.sysdigcloud.com).

Quando estiver conectado ao site de nuvem de Sysdig, clique em seu nome de usuário e, na página, você deverá ver a "Chave de acesso". 

![Chave de API d Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalação dos agentes do Sysdig para Kubernetes
Para monitorar seus contêineres, o Sysdig executa um processo em cada computador usando um `DaemonSet` Kubernetes.
Os DaemonSets são objetos da API do Kubernetes que executam uma única instância de um contêiner por computador.
Eles são perfeitos para instalar ferramentas como o agente de monitoramento do Sysdig.

Para instalar o daemonset Sysdig, você deve primeiro baixar [o modelo](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) de sysdig. Salve esse arquivo como `sysdig-daemonset.yaml`.

No Linux e no OS X, você pode executar:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

No PowerShell:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Em seguida, edite esse arquivo para inserir sua chave de acesso que você adquiriu da sua conta de Sysdig.

Por fim, crie o DaemonSet:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Exibir o monitoramento
Depois de instalados e em execução, os agentes devem bombear os dados de volta ao Sysdig.  Volte para o [painel sysdig](https://app.sysdigcloud.com) e você verá informações sobre seus contêineres.

Você também pode instalar painéis específicos do Kubernetes por meio do [assistente de novo painel](https://app.sysdigcloud.com/#/dashboards/new).
