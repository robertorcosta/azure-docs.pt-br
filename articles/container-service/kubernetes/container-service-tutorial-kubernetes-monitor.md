---
title: (PRETERIDO) Tutorial do Serviço de Contêiner do Azure – Monitorar o Kubernetes
description: Tutorial do Serviço de Contêiner do Azure – monitorar o Kubernetes com o Log Analytics
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 35f7c2fb968ea5285af5f2d597c67b3b22065906
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278232"
---
# <a name="deprecated-monitor-a-kubernetes-cluster-with-log-analytics"></a>(PRETERIDO) Monitorar um cluster Kubernetes do Azure com o Log Analytics

> [!TIP]
> Para a versão atualizada deste tutorial que usa o Serviço de Kubernetes do Azure, confira [Visão Geral do Azure Monitor para contêineres (versão prévia)](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Monitorar seu cluster do Kubernetes e os contêineres é fundamental, principalmente ao gerenciar um cluster de produção em grande escala com vários aplicativos.

Você pode usufruir de várias soluções de monitoramento do Kubernetes, da Microsoft ou de outros provedores. Neste tutorial, você deve monitorar seu cluster Kubernetes usando a solução Contêineres no [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md), a solução de gerenciamento de TI baseada em nuvem da Microsoft. (A solução Contêineres está na versão prévia.)

Este tutorial, parte sete de sete, aborda as seguintes tarefas:

> [!div class="checklist"]
> * Obter as configurações de espaço de trabalho do Log Analytics
> * Configurar agentes do Log Analytics nos nós do Kubernetes
> * Acessar informações de monitoramento no portal do Log Analytics ou no Portal do Azure

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em um cluster de contêiner, essas imagens foram carregadas no Registro de Contêiner do Azure e um cluster do Kubernetes foi criado.

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="get-workspace-settings"></a>Obter configurações de workspace

Quando você puder acessar o [portal do Log Analytics](https://mms.microsoft.com), acesse **Configurações** > **Fontes Conectadas** > **Servidores Linux**. Lá, você poderá encontrar a *ID do Workspace* e uma *Chave do Workspace* primária ou secundária. Anote esses valores, que serão necessários para configurar os agentes do Log Analytics no cluster.

## <a name="create-kubernetes-secret"></a>Criar segredo Kubernetes

Armazene as configurações de espaço de trabalho do Log Analytics em um segredo Kubernetes chamado `omsagent-secret` usando o comando [kubectl create secret][kubectl-create-secret]. Atualize `WORKSPACE_ID` com sua ID de espaço de trabalho do Log Analytics e `WORKSPACE_KEY` com a chave do espaço de trabalho.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Configurar os agentes do Log Analytics

O arquivo de manifesto Kubernetes a seguir pode ser usado para configurar os agentes de monitoramento de contêiner em um cluster Kubernetes. Ele cria um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) do Kubernetes, que executa um único pod idêntico em cada nó de cluster.

Salve o texto a seguir em um arquivo chamado `oms-daemonset.yaml`.

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

Crie o DaemonSet com o seguinte comando:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Para ver se o DaemonSet foi criado, execute:

```azurecli-interactive
kubectl get daemonset
```

A saída deverá ser semelhante a esta:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Depois que os agentes estiverem em execução, levará vários minutos para que o Log Analytics ingira e processe os dados.

## <a name="access-monitoring-data"></a>Acessar dados de monitoramento

Exiba e analise os dados de monitoramento com o contêiner com a [solução Contêiner](../../azure-monitor/insights/containers.md) no portal do Log Analytics ou no Portal do Azure.

Para instalar a solução Contêiner usando o [portal do Log Analytics](https://mms.microsoft.com), acesse a **Galeria de Soluções**. Em seguida, adicione a **solução Contêiner**. Como alternativa, adicione a solução Contêineres por meio do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

No portal do Log Analytics, procure um bloco de resumo **Contêineres** no painel. Clique no bloco para obter detalhes, incluindo: eventos de contêiner, erros, status, inventário de imagem e uso da CPU e de memória. Para obter informações mais detalhadas, clique em uma linha em qualquer bloco ou execute uma [pesquisa de logs](../../log-analytics/log-analytics-log-searches.md).

![Painel de contêineres no portal do Azure](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Da mesma forma, no portal do Azure, acesse o **Log Analytics** e selecione o nome do workspace. Para ver o bloco de resumo **Contêineres** clique em **Soluções** > **Contêineres**. Para ver os detalhes, clique no bloco.

Consulte a [documentação do Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md) para obter orientações detalhadas de como consultar e analisar dados de monitoramento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você monitorou o cluster Kubernetes com o Log Analytics. As tarefas abordadas incluíram:

> [!div class="checklist"]
> * Obter as configurações de espaço de trabalho do Log Analytics
> * Configurar agentes do Log Analytics nos nós do Kubernetes
> * Acessar informações de monitoramento no portal do Log Analytics ou no Portal do Azure


Siga este link para ver exemplos de scripts criados previamente para o Serviço de Contêiner.

> [!div class="nextstepaction"]
> [Exemplos de script do Serviço de Contêiner do Azure](cli-samples.md)
