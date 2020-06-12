---
title: Conectar um cluster do Kubernetes habilitado para o Azure Arc (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Conectar um cluster do Kubernetes habilitado para o Azure Arc com o Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, containers
ms.openlocfilehash: 962b6a17743ea2beed1e16503739c55c83babbce
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860538"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Conectar um cluster do Kubernetes habilitado para o Azure Arc (versão prévia)

Conectar um cluster Kubernetes ao Azure Arc. 

## <a name="before-you-begin"></a>Antes de começar

Verifique se os seguintes requisitos estão prontos:

* Um cluster do Kubernetes em execução
* Você precisará de acesso com kubeconfig e acesso de administrador de cluster. 
* O usuário ou a entidade de serviço usada com os comandos `az login` e `az connectedk8s connect` deve ter as permissões 'Ler' e 'Gravar' no tipo de recurso 'Microsoft.Kubernetes/connectedclusters'. A função "Integração do Azure Arc para Kubernetes" que tem essas permissões pode ser usada para atribuições de função no usuário ou na entidade de serviço usada com a CLI do Azure para integração.
* Versão mais recente das extensões *connectedk8s* e *k8sconfiguration*

## <a name="supported-regions"></a>Regiões com suporte

* Leste dos EUA
* Europa Ocidental

## <a name="network-requirements"></a>Requisitos de rede

Os agentes do Azure Arc exigem que protocolos/portas/URLs de saída a seguir funcionem.

* TCP na porta 443--> `https://:443`
* TCP na porta 9418--> `git://:9418`

| Ponto de extremidade (DNS)                                                                                               | Descrição                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Necessário para que o agente se conecte ao Azure e registre o cluster                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Ponto de extremidade do plano de dados para o agente enviar o status por push e obter informações de configuração                                      |
| `https://docker.io`                                                                                            | Necessário para efetuar pull de imagens de contêiner                                                                                         |
| `https://github.com`, git://github.com                                                                         | Os exemplos de repositórios GitOps estão hospedados no GitHub. O agente de configuração requer conectividade com qualquer ponto de extremidade git especificado. |
| `https://login.microsoftonline.com`                                                                            | Necessário para obter e atualizar tokens do Azure Resource Manager                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Necessário para efetuar pull de imagens de contêiner para agentes do Azure Arc                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registre os dois provedores de Kubernetes habilitados para o Azure Arc:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

O registro é um processo assíncrono. Ele pode levar aproximadamente 10 minutos. Você pode monitorar o processo de registro com os seguintes comandos:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>Instalar extensões da CLI do Azure

Instale a extensão `connectedk8s`, que ajuda a conectar clusters do Kubernetes ao Azure:

```console
az extension add --name connectedk8s
```

Instalar a extensão `k8sconfiguration`:

```console
az extension add --name k8sconfiguration
```

Execute os comandos a seguir para atualizar as extensões para as versões mais recentes.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Use um grupo de recursos para armazenar metadados para o cluster.

Primeiro, crie um grupo de recursos para manter o recurso de cluster conectado.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Saída:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Conectar um cluster

Em seguida, conectaremos nosso cluster do Kubernetes ao Azure. O fluxo de trabalho do `az connectedk8s connect` é o seguinte:

1. Verifique a conectividade com o cluster do Kubernetes: via `KUBECONFIG`, `~/.kube/config` ou `--kube-config`
1. Implante agentes do Azure Arc para Kubernetes usando o Helm 3, no namespace `azure-arc`

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Saída:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verifique o cluster conectado

Liste os clusters conectados:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Saída:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

O Kubernetes habilitado para Azure Arc implanta alguns operadores no namespace `azure-arc`. Você pode exibir essas implantações e pods aqui:

```console
kubectl -n azure-arc get deployments,pods
```

**Saída:**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Agentes do Azure Arc para Kubernetes

O Kubernetes habilitado para Azure Arc consiste em alguns agentes (operadores) que são executados no cluster implantado no namespace `azure-arc`.

* `deployment.apps/config-agent`: observa o cluster conectado para os recursos de configuração de controle do código de origem aplicados no cluster e atualiza o estado de conformidade
* `deployment.apps/controller-manager`: é um operador de operadores e orquestra interações entre os componentes do Azure Arc
* `deployment.apps/metrics-agent`: coleta métricas de outros agentes do Arc para garantir que eles estejam apresentando um desempenho ideal
* `deployment.apps/cluster-metadata-operator`: coleta metadados do cluster - versão do cluster, contagem de nós e versão do agente do Arc
* `deployment.apps/resource-sync-agent`: sincroniza os metadados de cluster mencionados acima para o Azure
* `deployment.apps/clusteridentityoperator`: mantém o certificado MSI (Identidade de Serviço Gerenciado) usado por outros agentes para comunicação com o Azure
* `deployment.apps/flux-logs-agent`: coleta logs dos operadores de flux implantados como parte da configuração de controle do código de origem

## <a name="delete-a-connected-cluster"></a>Excluir um cluster conectado

Você pode excluir um recurso do `Microsoft.Kubernetes/connectedcluster` usando a CLI do Azure ou o portal do Azure.

O comando de CLI do Azure `az connectedk8s delete` remove o recurso `Microsoft.Kubernetes/connectedCluster` no Azure. A CLI do Azure exclui todos os recursos do `sourcecontrolconfiguration` associados no Azure. A CLI do Azure usa a desinstalação do helm para remover os agentes no cluster.

O portal do Azure exclui o recurso do `Microsoft.Kubernetes/connectedcluster` no Azure e exclui todos os recursos do `sourcecontrolconfiguration` associados no Azure.

Para remover os agentes no cluster, você precisa executar `az connectedk8s delete` ou `helm uninstall azurearcfork8s`.

## <a name="next-steps"></a>Próximas etapas

* [Usar o GitOps em um cluster conectado](./use-gitops-connected-cluster.md)
* [Usar Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)
