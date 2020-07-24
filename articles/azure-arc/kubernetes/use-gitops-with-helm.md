---
title: Implantar gráficos do Helm usando o GitOps em um cluster kubernetes habilitado para Arc (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usar GitOps com Helm para uma configuração de cluster habilitada para Azure Arc (versão prévia)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Serviço do Kubernetes do Azure, contêineres
ms.openlocfilehash: f6a30dd66ccf476da0293bdebf9054b6781a6bf6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049941"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Implantar gráficos do Helm usando o GitOps em um cluster kubernetes habilitado para Arc (versão prévia)

Helm é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes. Semelhante a gerenciadores de pacotes do Linux, como APT e Yum, o Helm é usado para gerenciar gráficos Kubernetes, que são pacotes de recursos de Kubernetes pré-configurados.

Este artigo mostra como configurar e usar o Helm com Kubernetes habilitado para Azure Arc.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster conectado ao Kubernetes e habilitado para Azure Arc. Se você precisar de um cluster conectado, confira o [guia de início rápido sobre conexão a um cluster](./connect-cluster.md).

Primeiro, vamos definir as variáveis de ambiente a serem usadas em todo o tutorial. Você precisará do nome do grupo de recursos e do nome do cluster conectado.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Verifique se o cluster está habilitado com Arc

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Saída:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Visão geral de como usar GitOps e Helm com o Azure Arc habilitado kubernetes

 O operador do Helm fornece uma extensão para o Flux que automatiza as versões do Helm Chart. Confira a descrição de uma versão do Chart por meio de um recurso personalizado do Kubernetes chamado HelmRelease. O Flux sincroniza esses recursos do git para o cluster, e o operador Helm verifica se os gráficos do Helm são liberados conforme especificado nos recursos.

 Veja abaixo um exemplo de estrutura de repositório git que usaremos neste tutorial:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── vote-app.yaml
```

No repositório git, temos dois diretórios, um contendo um gráfico de Helm e um contendo a configuração de versões. No `releases` diretório, o `vote-app.yaml` contém a configuração HelmRelease mostrada abaixo:

```bash
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: vote-app
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-vote
  values:
    frontendServiceName: arc-k8s-demo-vote-front
```

A configuração de versão Helm contém os seguintes campos:

- `metadata.name` é obrigatório e precisa seguir as convenções de nomenclatura de Kubernetes
- `metadata.namespace` é opcional e determina onde a versão é criada
- `spec.releaseName` é opcional e, se não for fornecida, o nome da versão será $namespace-$name
- `spec.chart.path` é o diretório que contém o gráfico, fornecido em relação à raiz do repositório
- `spec.values` são personalizações do usuário de valores de parâmetro padrão do próprio gráfico

As opções especificadas em spec.values de HelmRelease substituirão as opções especificadas em values.yaml da origem do gráfico.

Saiba mais sobre o HelmRelease na [documentação oficial do operador de Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/)

## <a name="create-a-configuration"></a>Criar uma configuração

Usando a extensão da CLI do Azure para `k8sconfiguration`, vamos vincular nosso cluster conectado ao repositório git de exemplo. Daremos a essa configuração um nome `azure-voting-app` e implantaremos o operador Flux ao namespace `arc-k8s-demo`.

```bash
az k8sconfiguration create --name azure-voting-app \
  --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME \
  --operator-instance-name flux --operator-namespace arc-k8s-demo \
  --operator-params='--git-readonly --git-path=releases' \
  --enable-helm-operator --helm-operator-version='0.6.0' \
  --helm-operator-params='--set helm.versions=v3' \
  --repository-url https://github.com/Azure/arc-helm-demo.git  \
  --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parâmetros de configuração

Para personalizar a criação da configuração, [saiba mais sobre parâmetros adicionais que você pode usar](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Validar a configuração

Usando a CLI do Azure, verifique se o `sourceControlConfiguration` foi criado com êxito.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

O `sourceControlConfiguration` recurso é atualizado com o status de conformidade, mensagens e informações de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-voting-app",
  "name": "azure-voting-app",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Validar o aplicativo

Execute o comando a seguir e navegue até [localhost: 3000](http://localhost:3000) no navegador para verificar se o aplicativo está em execução.

```bash
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo-vote-front 3000:80
```

## <a name="next-steps"></a>Próximas etapas

- [Usar Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)
