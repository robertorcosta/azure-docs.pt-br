---
title: Usar GitOps com Helm para uma configuração de cluster habilitada para Azure Arc (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usar GitOps com Helm para uma configuração de cluster habilitada para Azure Arc (versão prévia)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Serviço do Kubernetes do Azure, contêineres
ms.openlocfilehash: 9cd1169c7a622da0e4be3900f94dc31fc99e762d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83662534"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>Usar GitOps com Helm para uma configuração de cluster habilitada para Azure Arc (versão prévia)

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

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>Visão geral do uso do Helm com Kubernetes habilitados para Azure Arc

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
    └── prod
        └── azure-vote-app.yaml
```

No repositório git, temos dois diretórios, um contendo um gráfico de Helm e outro contendo a configuração das versões. No diretório `releases/prod`, o `azure-vote-app.yaml` contém a configuração HelmRelease mostrada abaixo:

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

A configuração de versão Helm contém os seguintes campos:

- `metadata.name` é obrigatório e precisa seguir as convenções de nomenclatura de Kubernetes
- `metadata.namespace` é opcional e determina onde a versão é criada
- `spec.releaseName` é opcional e, se não for fornecida, o nome da versão será $namespace-$name
- `spec.chart.path` é o diretório que contém o gráfico, fornecido em relação à raiz do repositório
- `spec.values` são personalizações do usuário de valores de parâmetro padrão do próprio gráfico

As opções especificadas em spec.values de HelmRelease substituirão as opções especificadas em values.yaml da origem do gráfico.

Saiba mais sobre o HelmRelease na [documentação oficial do operador de Helm](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html)

## <a name="create-a-configuration"></a>Criar uma configuração

Usando a extensão da CLI do Azure para `k8sconfiguration`, vamos vincular nosso cluster conectado ao repositório git de exemplo. Daremos a essa configuração um nome `azure-voting-app` e implantaremos o operador Flux ao namespace `prod`.

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parâmetros de configuração

Para personalizar a criação da configuração, [saiba mais sobre parâmetros adicionais que você pode usar](./use-gitops-connected-cluster.md#additional-parameters).


## <a name="validate-the-configuration"></a>Validar a configuração

Usando a CLI do Azure, verifique se o `sourceControlConfiguration` foi criado com êxito.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Observe que o recurso `sourceControlConfiguration` é atualizado com o status de conformidade, mensagens e informações de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Compliant",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Validar o aplicativo

Agora, verificaremos se o aplicativo está em execução obtendo o IP do serviço.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Saída:**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Localize o endereço IP externo da saída acima e abra-o em um navegador.

## <a name="next-steps"></a>Próximas etapas

- [Usar Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)
