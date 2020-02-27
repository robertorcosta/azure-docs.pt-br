---
title: Integrar com serviços gerenciados do Azure usando o Service Broker Aberto para Azure (OSBA)
description: Integrar com serviços gerenciados do Azure usando o Service Broker Aberto para Azure (OSBA)
author: zr-msft
ms.topic: overview
ms.date: 12/05/2017
ms.author: zarhoads
ms.openlocfilehash: 8d727256afbe152a4f7022d0fd2454c4677b023c
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595596"
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Integrar com serviços gerenciados do Azure usando o Service Broker Aberto para Azure (OSBA)

Junto com o [catálogo de serviços do Kubernetes ][kubernetes-service-catalog], o Service Broker Aberto para Azure (OSBA) permite que desenvolvedores utilizem serviços gerenciados pelo Azure no Kubernetes. Este guia se concentra na implantação do Serviço de catálogo do Kubernetes, Service Broker Aberto para Azure (OSBA) e aplicativos que usam serviços gerenciados pelo Azure usando o Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure

* CLI do Azure: [instale-a localmente][azure-cli-install] ou use-a no [Azure Cloud Shell][azure-cloud-shell].

* CLI 2.7+ do Helm: [instale-a localmente][helm-cli-install] ou use-a no [Azure Cloud Shell][azure-cloud-shell].

* Permissões para criar uma Entidade de Serviço com a função de Colaborador na sua assinatura Azure

* Um cluster do Serviço de Kubernetes do Azure (AKS) existente. Caso precise de um cluster AKS, siga o tutorial [Criar um cluster AKS ][create-aks-cluster].

## <a name="install-service-catalog"></a>Instalar o catálogo de serviços

A primeira etapa é instalar o catálogo de serviços em seu cluster Kubernetes usando um gráfico Helm. Atualize sua instalação Tiller (servidor do Helm) em seu cluster com:

```azurecli-interactive
helm init --upgrade
```

Agora, adicione o gráfico do catálogo de serviços para o repositório do Helm:

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Por fim, instale o catálogo de serviços com o gráfico do Helm. Se o cluster for habilitado em RBAC, execute este comando.

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set apiserver.storage.etcd.persistence.enabled=true --set apiserver.healthcheck.enabled=false --set controllerManager.healthcheck.enabled=false --set apiserver.verbosity=2 --set controllerManager.verbosity=2
```

Se o cluster não for habilitado em RBAC, execute este comando.

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false --set apiserver.storage.etcd.persistence.enabled=true --set apiserver.healthcheck.enabled=false --set controllerManager.healthcheck.enabled=false --set apiserver.verbosity=2 --set controllerManager.verbosity=2
```

Depois que o gráfico Helm tiver sido executado, verifique que `servicecatalog` aparece na saída do seguinte comando:

```azurecli-interactive
kubectl get apiservice
```

Por exemplo, você deverá ver uma saída semelhante a estas (mostrar aqui truncadas):

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Instalar o Service Broker Aberto para Azure

A próxima etapa é instalar o [Service Broker Aberto para Azure][open-service-broker-azure], que inclui o catálogo para os serviços gerenciados do Azure. Exemplos de serviços do Azure disponíveis são o Banco de Dados do Azure para PostgreSQL, o Banco de Dados do Azure para MySQL, o Banco de Dados SQL do Azure.

Comece adicionando o Service Broker Aberto ao repositório do Helm do Azure:

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Crie uma [Entidade de Serviço][create-service-principal] com o seguinte comando CLI do Azure:

```azurecli-interactive
az ad sp create-for-rbac
```

A saída deverá ser semelhante à esta abaixo. Anote os valores `appId`, `password` e `tenant`. Você irá usá-los na próxima etapa.

```JSON
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

Defina as seguintes variáveis de ambiente com os valores anteriores:

```azurecli-interactive
AZURE_CLIENT_ID=<appId>
AZURE_CLIENT_SECRET=<password>
AZURE_TENANT_ID=<tenant>
```

Agora, obtenha sua ID da assinatura do Azure:

```azurecli-interactive
az account show --query id --output tsv
```

Defina novamente as seguintes variáveis de ambiente com o valor anterior:

```azurecli-interactive
AZURE_SUBSCRIPTION_ID=[your Azure subscription ID from above]
```

Agora que você preencheu essas variáveis de ambiente, execute o seguinte comando para instalar o Service Broker aberta para o Azure usando o gráfico Helm:

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

Uma vez concluída a implantação de OSBA, instale a[CLI do serviço de catálogo][service-catalog-cli], uma interface de linha de comando fácil de usar para consultar os agentes de serviço, as classes de serviço, os planos de serviço e muito mais.

Execute os comandos a seguir para instalar o binário da CLI do serviço de catálogo:

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

Agora, liste os agentes de serviço instalados:

```azurecli-interactive
./svcat get brokers
```

Será exibida uma saída semelhante à seguinte:

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

Em seguida, liste as classes de serviço disponíveis. As classes de serviço exibidas são os serviços disponíveis gerenciados pelo Azure que podem ser provisionados pelo Service Broker Aberto para Azure.

```azurecli-interactive
./svcat get classes
```

Por fim, liste todos os planos de serviço disponíveis. Planos de serviço são as camada de serviço para os serviços gerenciados pelo Azure. Por exemplo, para o Banco de Dados do Azure para MySQL, os planos variam de `basic50` nível Básico com 50 Unidades de Transação do Banco de Dados (DTUs), para `standard800` nível Standard com 800 DTUs.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Instale o WordPress do gráfico Helm usando o Banco de Dados do Azure para MySQL

Nesta etapa, você usa o Helm para instalar um gráfico Helm para Wordpress atualizado. O gráfico provisiona uma instância externa do Banco de Dados do Azure para MySQL que o Wordpress pode usar. Esse processo pode levar alguns minutos.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0 --set replicaCount=1
```

Para verificar que a instalação tenha configurado os recursos corretos, liste as instâncias de serviço e associações instaladas:

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Liste os segredos instalados:

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>Próximas etapas

Ao seguir as instruções deste artigo, você implantou o catálogo de serviços para um cluster do Serviço de Kubernetes do Azure (AKS). Você usou o Service Broker Aberto para o Azure para implantar uma instalação do WordPress que usa os serviços gerenciados pelo Azure, neste caso, o Banco de Dados do Azure para MySQL.

Consulte o repositório [Azure/gráficos-helm][helm-charts] para acessar outros gráficos Helm com base em OSBA atualizados. Se você estiver interessado em criar seus próprios gráficos que funcionam com OSBA, consulte [criar um novo gráfico][helm-create-new-chart].

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: https://docs.helm.sh/helm/#helm-install
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
