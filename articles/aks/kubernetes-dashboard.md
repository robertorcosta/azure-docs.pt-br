---
title: Gerenciar um cluster do Serviço de Kubernetes do Azure com o painel da Web
description: Saiba como usar o painel da interface do usuário Web interna do Kubernetes para gerenciar um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595341"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Acessar o painel da Web do Kubernetes no AKS (Serviço de Kubernetes do Azure)

Kubernetes inclui um painel da web que pode ser usado para operações básicas de gerenciamento. Este painel permite exibir o status de integridade básico e as métricas dos seus aplicativos, criar e implantar serviços e editar aplicativos existentes. Este artigo mostra como acessar o painel do Kubernetes usando a CLI do Azure e, em seguida, orienta você por meio de algumas operações básicas do painel.

Para obter mais informações sobre o painel do Kubernetes, consulte [Painel de interface do usuário Web do Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster do AKS e estabelecido uma conexão `kubectl` com o cluster. Se você precisar criar um cluster AKS, consulte o [Início rápido do AKS][aks-quickstart].

A CLI do Azure versão 2.0.46 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Iniciar o painel do Kubernetes

Para iniciar o painel de Kubernetes, use o [comando de navegação][az-aks-browse] az aks. O exemplo abaixo abre o painel para o cluster nomeado *myAKSCluster* em um grupo de recursos chamado *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Este comando cria um proxy entre o sistema de desenvolvimento e a API do Kubernetes, e abre um navegador da Web para o painel do Kubernetes. Se o navegador da Web não abrir o painel do Kubernetes, copie e cole o endereço da URL anotado na CLI do Azure, que geralmente é `http://127.0.0.1:8001`.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Se o cluster do AKS usa RBAC, um *ClusterRoleBinding* deve ser criado antes de poder acessar o painel corretamente. Por padrão, o painel do Kubernetes é implantado com um mínimo de acesso de leitura e exibe os erros de acesso do RBAC. O painel do Kubernetes não oferece suporte a credenciais fornecidas pelo usuário para determinar o nível de acesso, em vez disso, ele usa as funções concedidas à conta de serviço. Um administrador de cluster pode optar por conceder acesso adicional para o *painel do kubernetes* conta de serviço, no entanto, isso pode ser um vetor para elevação de privilégios. Você também pode integrar a autenticação do Active Directory para fornecer um nível mais granular de acesso.
> 
> Para criar uma vinculação, use o comando [kubectl create clusterrolebinding.][kubectl-create-clusterrolebinding] O exemplo a seguir mostra como criar uma vinculação de amostra, no entanto, esta vinculação de amostra não aplica nenhum componente de autenticação adicional e pode levar a um uso inseguro. O painel do Kubernetes está aberto para qualquer pessoa com acesso à URL. Não expor publicamente o painel do Kubernetes.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Para obter mais informações sobre como usar os métodos de autenticação diferentes, consulte o wiki do painel do Kubernetes em [controles de acesso][dashboard-authentication].

![A página de visão geral do painel da Web do Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Criar um aplicativo

Para ver como o painel do Kubernetes pode reduzir a complexidade das tarefas de gerenciamento, vamos criar um aplicativo. Você pode criar um aplicativo usando o painel do Kubernetes fornecendo texto de entrada e um arquivo YAML ou por meio de um assistente gráfico.

Para criar um aplicativo, conclua as seguintes etapas:

1. Selecione o botão **Criar** na janela superior direita.
1. Para usar o assistente gráfico, escolha **Criar um aplicativo**.
1. Forneça um nome para a implantação, como *nginx*
1. Insira o nome da imagem de contêiner a ser usada, como *nginx:1.15.5*
1. Para expor a porta 80 para o tráfego da Web, crie um serviço de Kubernetes. Em **Serviço**, selecione **Externo** e insira **80** para a porta e para a porta de destino.
1. Quando estiver pronto, selecione **Implantar** para criar o aplicativo.

![Implantar um aplicativo no painel da Web do Kubernetes](./media/kubernetes-dashboard/create-app.png)

Leva um minuto ou dois para que um endereço IP externo seja atribuído ao serviço de Kubernetes. No lado esquerdo, em **Descoberta e Balanceamento de Carga** selecione **Serviços**. O serviço do aplicativo é listado, incluindo os *Pontos de extremidade externos*, conforme é mostrado no exemplo a seguir:

![Exibir uma lista de serviços e pontos de extremidade](./media/kubernetes-dashboard/view-services.png)

Selecione o endereço do ponto de extremidade para abrir uma janela do navegador da Web para a página padrão do NGINX:

![Exibir a página do NGINX padrão do aplicativo implantado](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Exibir informações de pod

O painel do Kubernetes pode fornecer o monitoramento básico de métricas e informações de solução de problemas, como logs.

Para obter mais informações sobre os pods de aplicativo, selecione **Pods** no menu à esquerda. A lista de pods disponíveis é mostrada. Escolha o pod *nginx* para exibir informações, como o consumo de recursos:

![Exibir informações de pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Editar o aplicativo

Além de criar e exibir os aplicativos, o painel do Kubernetes pode ser usado para editar e atualizar implantações de aplicativo. Para fornecer redundância adicional ao aplicativo, vamos aumentar o número de réplicas do NGINX.

Para editar uma implantação:

1. Selecione **Implantações** no menu à esquerda e, em seguida, escolha a implantação *nginx*.
1. Selecione **Editar** na barra de navegação superior à direita.
1. Localize o valor `spec.replica`, próximo à linha 20. Para aumentar o número de réplicas do aplicativo, altere esse valor de *1* para *3*.
1. Selecione **Atualizar** quando estiver pronto.

![Edite a implantação para atualizar o número de réplicas](./media/kubernetes-dashboard/edit-deployment.png)

Levará alguns instantes para que os pods sejam criados dentro de um conjunto de réplicas. No menu à esquerda, escolha **Conjuntos de Réplicas** e, em seguida, escolha o conjunto de réplicas *nginx*. A lista de pods agora reflete a contagem de réplica atualizada, conforme é mostrado na saída de exemplo a seguir:

![Exibir informações sobre o conjunto de réplicas](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o painel do Kubernetes, confira [Painel da interface do usuário da Web do Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
