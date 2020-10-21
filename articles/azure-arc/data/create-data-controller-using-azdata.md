---
title: Criar controlador de dados usando [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]
description: Crie um controlador de dados de arco do Azure em um cluster kubernetes típico de vários nós que você já criou usando o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: eb5fac5987a36c425b9e130df17412e5ad902345
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310932"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Criar um controlador de dados de arco do Azure usando o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Examine o tópico [criar o controlador de dados de arco do Azure](create-data-controller.md) para obter informações gerais.

Para criar o controlador de dados de arco do Azure usando o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , você precisará ter o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] instalado.

   [Instalar o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Independentemente da plataforma de destino escolhida, você precisará definir as variáveis de ambiente a seguir antes da criação do usuário administrador do controlador de dados. Você pode fornecer essas credenciais a outras pessoas que precisam ter acesso de administrador ao controlador de dados, conforme necessário.

**AZDATA_USERNAME** -um nome de usuário de sua escolha para o administrador do controlador de dados. Exemplo: `arcadmin`

**AZDATA_PASSWORD** -uma senha de sua escolha para o usuário administrador do controlador de dados. A senha deve ter pelo menos oito caracteres de comprimento e conter caracteres de três dos quatro conjuntos a seguir: letras maiúsculas, letras minúsculas, números e símbolos.

### <a name="linux-or-macos"></a>Linux ou macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Você precisará se conectar e autenticar em um cluster kubernetes e ter um contexto kubernetes existente selecionado antes de iniciar a criação do controlador de dados de arco do Azure. A maneira como você se conecta a um cluster ou serviço do kubernetes varia. Consulte a documentação da distribuição ou serviço kubernetes que você está usando em como se conectar ao servidor de API do kubernetes.

Você pode verificar se tem uma conexão kubernetes atual e confirmar seu contexto atual com os comandos a seguir.

```console
kubectl get namespace

kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Criar o controlador de dados de arco do Azure

> [!NOTE]
> Você pode usar um valor diferente para o `--namespace` parâmetro do comando azdata Arc DC Create nos exemplos abaixo, mas certifique-se de usar esse nome de namespace para o `--namespace parameter` em todos os outros comandos abaixo.

Siga a seção apropriada abaixo, dependendo da sua plataforma de destino para configurar sua criação.

[Criar no serviço kubernetes do Azure (AKS)](#create-on-azure-kubernetes-service-aks)

[Criar no mecanismo de AKS no Hub de Azure Stack](#create-on-aks-engine-on-azure-stack-hub)

[Criar em AKS no Azure Stack HCI](#create-on-aks-on-azure-stack-hci)

[Criar no Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)

[Criar na plataforma de contêiner do Red Hat OpenShift (OCP)](#create-on-red-hat-openshift-container-platform-ocp)

[Criar em código-fonte aberto, upstream kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)

[Criar no serviço kubernetes elástico do AWS (EKS)](#create-on-aws-elastic-kubernetes-service-eks)

[Criar no serviço de mecanismo do Google Cloud kubernetes (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Criar no serviço kubernetes do Azure (AKS)

Por padrão, o perfil de implantação AKS usa a `managed-premium` classe de armazenamento. A `managed-premium` classe de armazenamento só funcionará se você tiver VMs que foram implantadas usando imagens de VM que têm discos Premium.

Se você pretende usar `managed-premium` como sua classe de armazenamento, poderá executar o comando a seguir para criar o controlador de dados. Substitua os espaços reservados no comando pelo nome do grupo de recursos, pela ID da assinatura e pelo local do Azure.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Se você não tiver certeza de qual classe de armazenamento usar, deverá usar a `default` classe de armazenamento que é suportada independentemente do tipo de VM que você está usando. Ele simplesmente não fornecerá o desempenho mais rápido.

Se você quiser usar a `default` classe de armazenamento, poderá executar este comando:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Depois de executar o comando, continue em para [monitorar o status de criação](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Criar no mecanismo de AKS no Hub de Azure Stack

Por padrão, o perfil de implantação usa a `managed-premium` classe de armazenamento. A `managed-premium` classe de armazenamento só funcionará se você tiver VMs de trabalho que foram implantadas usando imagens de VM que têm discos Premium no Hub Azure Stack.

Você pode executar o seguinte comando para criar o controlador de dados usando a classe de armazenamento Managed-Premium:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Se você não tiver certeza de qual classe de armazenamento usar, deverá usar a `default` classe de armazenamento que é suportada independentemente do tipo de VM que você está usando. No Hub Azure Stack, os discos Premium e os discos standard são apoiados pela mesma infraestrutura de armazenamento. Portanto, espera-se que eles forneçam o mesmo desempenho geral, mas com limites de IOPS diferentes.

Se você quiser usar a `default` classe de armazenamento, poderá executar esse comando.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Depois de executar o comando, continue em para [monitorar o status de criação](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Criar em AKS no Azure Stack HCI

Por padrão, o perfil de implantação usa uma classe de armazenamento denominada `default` e o tipo de serviço `LoadBalancer` .

Você pode executar o comando a seguir para criar o controlador de dados usando a `default` classe de armazenamento e o tipo de serviço `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Depois de executar o comando, continue em para [monitorar o status de criação](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Criar no Azure Red Hat OpenShift (ARO)

Para criar um controlador de dados no Azure Red Hat OpenShift, você precisará executar os seguintes comandos em seu cluster para relaxar as restrições de segurança. Esse é um requisito temporário que será removido no futuro.
> [!NOTE]
>   Use o mesmo namespace aqui e no `azdata arc dc create` comando a seguir. O exemplo é `arc` .

Primeiro, baixe a restrição de contexto de segurança (SCC) personalizada do [GitHub](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml) e aplique-a ao cluster.

Você pode executar o seguinte comando para criar o controlador de dados:
> [!NOTE]
>   Use o mesmo namespace aqui e nos `oc adm policy add-scc-to-user` comandos acima. O exemplo é `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Depois de executar o comando, continue em para [monitorar o status de criação](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Criar na plataforma de contêiner do Red Hat OpenShift (OCP)


> [!NOTE]
> Se você estiver usando a plataforma de contêiner do Red Hat OpenShift no Azure, é recomendável usar a versão mais recente disponível.

Para criar um controlador de dados na plataforma de contêiner do Red Hat OpenShift, será necessário executar os seguintes comandos em relação ao cluster para relaxar as restrições de segurança. Esse é um requisito temporário que será removido no futuro.
> [!NOTE]
>   Use o mesmo namespace aqui e no `azdata arc dc create` comando a seguir. O exemplo é `arc` .

```console
oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
```

Você também precisará determinar a classe de armazenamento a ser usada executando o comando a seguir.

```console
kubectl get storageclass
```

Primeiro, comece criando um novo arquivo de perfil de implantação personalizado com base no perfil de implantação Azure-Arc-openshift executando o comando a seguir. Esse comando criará um diretório `custom` no diretório de trabalho atual e um arquivo de perfil de implantação personalizado `control.json` nesse diretório.

Use o perfil `azure-arc-openshift` para a plataforma de contêiner OpenShift.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```
Use o perfil `azure-arc-azure-openshift` do Azure redhat Open Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

Agora, defina a classe de armazenamento desejada substituindo o `<storageclassname>` comando abaixo pelo nome da classe de armazenamento que você deseja usar que foi determinada executando o `kubectl get storageclass` comando acima.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Por padrão, o perfil de implantação Azure-Arc-openshift usa `NodePort` como o tipo de serviço. Se você estiver usando um cluster OpenShift integrado a um balanceador de carga, poderá alterar a configuração para usar o tipo de serviço Balancer usando o seguinte comando:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Ao usar o OpenShift, você pode querer executar com as políticas de segurança padrão no OpenShift ou deseja, geralmente, bloquear o ambiente mais do que o típico. Opcionalmente, você pode optar por desabilitar alguns recursos para minimizar as permissões necessárias no momento da implantação e em tempo de execução executando os comandos a seguir.

Este comando desabilita as coleções de métricas sobre pods. Você não poderá ver as métricas sobre pods nos painéis do Grafana se desabilitar esse recurso. O padrão é true.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Este comando desabilita as coleções de métricas sobre nós. Você não poderá ver as métricas sobre os nós nos painéis do Grafana se desabilitar esse recurso. O padrão é true.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Esse comando desabilita a capacidade de tirar despejos de memória para fins de solução de problemas.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

Agora você está pronto para criar o controlador de dados usando o comando a seguir.
> [!NOTE]
>   Use o mesmo namespace aqui e nos `oc adm policy add-scc-to-user` comandos acima. O exemplo é `arc` .

> [!NOTE]
>   O `--path` parâmetro deve apontar para o _diretório_ que contém o control.jsno arquivo não para o control.jsno próprio arquivo.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Depois de executar o comando, continue em para [monitorar o status de criação](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Criar em código-fonte aberto, upstream kubernetes (kubeadm)

Por padrão, o perfil de implantação kubeadm usa uma classe de armazenamento chamada `local-storage` e o tipo de serviço `NodePort` . Se isso for aceitável, você poderá ignorar as instruções abaixo que definem a classe de armazenamento e o tipo de serviço desejados e executar imediatamente o `azdata arc dc create` comando a seguir.

Se você quiser personalizar seu perfil de implantação para especificar um tipo de serviço e/ou classe de armazenamento específico, comece criando um novo arquivo de perfil de implantação personalizado com base no perfil de implantação kubeadm executando o comando a seguir. Esse comando criará um diretório `custom` no diretório de trabalho atual e um arquivo de perfil de implantação personalizado `control.json` nesse diretório.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

Você pode pesquisar as classes de armazenamento disponíveis executando o comando a seguir.

```console
kubectl get storageclass
```

Agora, defina a classe de armazenamento desejada substituindo o `<storageclassname>` comando abaixo pelo nome da classe de armazenamento que você deseja usar que foi determinada executando o `kubectl get storageclass` comando acima.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Por padrão, o perfil de implantação kubeadm usa `NodePort` como o tipo de serviço. Se você estiver usando um cluster kubernetes integrado a um balanceador de carga, poderá alterar a configuração usando o comando a seguir.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Agora você está pronto para criar o controlador de dados usando o comando a seguir.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Depois de executar o comando, continue em para [monitorar o status de criação](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Criar no serviço kubernetes elástico do AWS (EKS)

Por padrão, a classe de armazenamento EKS é `gp2` e o tipo de serviço é `LoadBalancer` .

Execute o comando a seguir para criar o controlador de dados usando o perfil de implantação EKS fornecido.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Depois de executar o comando, continue em para [monitorar o status de criação](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Criar no serviço de mecanismo do Google Cloud kubernetes (GKE)

Por padrão, a classe de armazenamento GKE é `standard` e o tipo de serviço é `LoadBalancer` .

Execute o comando a seguir para criar o controlador de dados usando o perfil de implantação GKE fornecido.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Depois de executar o comando, continue em para [monitorar o status de criação](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Monitorando o status de criação

A criação do controlador levará alguns minutos para ser concluída. Você pode monitorar o progresso em outra janela do terminal com os seguintes comandos:

> [!NOTE]
>  Os comandos de exemplo a seguir pressupõem que você criou um controlador de dados e o namespace kubernetes com o nome `arc` . Se você usou um nome de namespace/controlador de dados diferente, poderá substituir `arc` pelo seu nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Você também pode verificar o status de criação de qualquer Pod específico executando um comando como abaixo. Isso é especialmente útil para solucionar problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Solucionando problemas de criação

Se você encontrar qualquer problemas com a criação, consulte o [Guia de solução de problemas](troubleshoot-guide.md).
