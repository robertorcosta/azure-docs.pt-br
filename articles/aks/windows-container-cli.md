---
title: Executar o contêiner do Windows Server no cluster Azure Kubernetes Service
description: Aprenda a criar rapidamente um cluster Kubernetes, implante um aplicativo em um contêiner do Windows Server no Azure Kubernetes Service (AKS) usando o Azure CLI.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 8d2a91f63815e7ba4bcbe4084b80a06fa7779099
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886714"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Visualização - Crie um contêiner do Windows Server em um cluster Azure Kubernetes Service (AKS) usando o Azure CLI

O AKS (Serviço de Kubernetes do Azure) é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Neste artigo, você implanta um cluster AKS usando o Cli do Azure. Você também implanta um aplicativo de amostra de ASP.NET em um contêiner do Windows Server para o cluster.

Esse recurso está atualmente na visualização.

![Imagem de navegação para ASP.NET aplicativo de amostra](media/windows-container/asp-net-sample-app.png)

Este artigo assume uma compreensão básica dos conceitos kubernetes. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o CLI localmente, este artigo requer que você esteja executando a versão 2.0.61 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="before-you-begin"></a>Antes de começar

Você deve adicionar um pool de nó adicional depois de criar seu cluster que pode executar contêineres do Windows Server. A adição de um pool adicional de nó é coberta em uma etapa posterior, mas primeiro você precisa ativar alguns recursos de visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são opt-in de autoatendimento. As visualizações são fornecidas "como está" e "conforme disponível" e são excluídas dos contratos de nível de serviço e garantia limitada. As visualizações aks são parcialmente cobertas pelo suporte ao cliente na melhor base de esforço. Como tal, esses recursos não são destinados ao uso da produção. Para obter uma infromation adicional, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte da AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para usar os contêineres do Windows Server, você precisa da versão CLI *de visualização aks* versão 0.4.12 ou superior. Instale a extensão *aks-preview* Azure CLI usando o comando [az extension add][az-extension-add] e verifique se há atualizações disponíveis usando o comando [az extension update::][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Registre o recurso de visualização do Windows

Para criar um cluster AKS que pode usar vários pools de nó e executar contêineres do Windows Server, primeiro habilite os sinalizadores de recurso *do WindowsPreview* em sua assinatura. O recurso *WindowsPreview* também usa clusters de pool de vários nós e conjunto de escala de máquina virtual para gerenciar a implantação e configuração dos ádenos Kubernetes. Registre o sinalizador de recurso *do WindowsPreview* usando o comando [az feature register][az-feature-register] como mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Qualquer cluster AKS que você criar depois de registrar com sucesso o sinalizador de recurso *do WindowsPreview* use essa experiência de cluster de visualização. Para continuar a criar clusters regulares e totalmente suportados, não habilite recursos de visualização em assinaturas de produção. Use uma assinatura separada do Teste ou desenvolvimento do Azure para testar recursos de visualização.

Leva alguns minutos para o registro ser concluído. Verifique o status de registro usando o comando [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Quando o estado `Registered`de registro for, pressione ctrl-C para parar de monitorar o estado.  Em seguida, atualize o registro do provedor de recursos *Microsoft.ContainerService* usando o comando [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que suportam vários pools de nó:

* Você não pode excluir a primeira piscina de nó.

Enquanto esse recurso estiver em visualização, as seguintes limitações adicionais se aplicam:

* O cluster AKS pode ter um máximo de oito piscinas de nó.
* O cluster AKS pode ter um máximo de 400 nódulos nessas oito piscinas de nó.
* O nome do pool do nó do Servidor Windows tem um limite de 6 caracteres.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Ao criar um grupo de recursos, você é solicitado a especificar um local. Essa é a localização na qual os metadados do grupo de recursos são armazenados e na qual os recursos são executados no Azure, caso você não especifique outra região durante a criação de recursos. Crie um grupo de recursos usando o comando [az group create][az-group-create].

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no *local eastus.*

> [!NOTE]
> Este artigo usa a sintaxe Bash para os comandos neste tutorial.
> Se você estiver usando o Azure Cloud Shell, certifique-se de que a queda no canto superior esquerdo da janela Cloud Shell esteja definida como **Bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A seguinte saída de exemplo mostra o grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Para executar um cluster AKS que suporta pools de nó para contêineres do Windows Server, seu cluster precisa usar uma política de rede que use o plugin de rede [Azure CNI][azure-cni-about] (avançado). Para obter informações mais detalhadas para ajudar a planejar as faixas de sub-rede e considerações de rede necessárias, consulte [configurar a rede CNI do Azure][use-advanced-networking]. Use o comando [az aks create][az-aks-create] para criar um cluster AKS chamado *myAKSCluster*. Este comando criará os recursos de rede necessários se eles não existirem.
  * O cluster é configurado com dois nós
  * Os parâmetros *de nome de usuário* do administrador do Windows e do *windows-admin* definem as credenciais de administrador para quaisquer contêineres do Windows Server criados no cluster.

> [!NOTE]
> Para garantir que o cluster funcione de forma confiável, você deve executar pelo menos 2 (dois) nódulos no pool de nó padrão.

Forneça sua própria *PASSWORD_WIN* segura (lembre-se de que os comandos deste artigo são inseridos em uma concha BASH):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Se você tiver um erro de validação de senha, tente criar seu grupo de recursos em outra região.
> Em seguida, tente criar o cluster com o novo grupo de recursos.

> [!Note]
> Se você não conseguir criar o cluster AKS porque a versão não é suportada nesta região, então você pode usar o comando [az aks get-versions --location eastus] para encontrar a lista de versões suportadas para esta região.


Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster. Ocasionalmente, o cluster pode levar mais de alguns minutos para provisionar. Aguarde até 10 minutos nesses casos. 

## <a name="add-a-windows-server-node-pool"></a>Adicionar um pool de nó do Servidor Windows

Por padrão, um cluster AKS é criado com um pool de nós que pode executar contêineres Linux. Use `az aks nodepool add` o comando para adicionar um pool de nó adicional que pode executar contêineres do Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

O comando acima cria um novo pool de nó chamado *npwin* e o adiciona ao *myAKSCluster*. Ao criar um pool de nó para executar contêineres do Windows Server, o valor padrão para *tamanho de nó-vm* é *Standard_D2s_v3*. Se você optar por definir o parâmetro *tamanho nó-vm,* verifique a lista de [tamanhos de VM restritos][restricted-vm-sizes]. O tamanho mínimo recomendado é *Standard_D2s_v3*. O comando acima também usa a sub-rede padrão `az aks create`na vnet padrão criada ao ser executado .

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster do Kubernetes, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar o `kubectl` localmente, use o comando [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```console
kubectl get nodes
```

A saída do exemplo a seguir mostra todos os nós no cluster. Certifique-se de que o status de todos os nós está *pronto:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Executar o aplicativo

Um arquivo de manifesto do Kubernetes define um estado desejado para o cluster, como as imagens de contêiner a serem executadas. Neste artigo, um manifesto é usado para criar todos os objetos necessários para executar o aplicativo de amostra ASP.NET em um contêiner do Windows Server. Este manifesto inclui uma [implantação do Kubernetes][kubernetes-deployment] para o aplicativo de amostra de ASP.NET e um serviço externo [kubernetes][kubernetes-service] para acessar o aplicativo a partir da internet.

O aplicativo de exemplo ASP.NET é fornecido como parte do [.NET Framework Samples][dotnet-samples] e executado em um contêiner do Windows Server. O AKS exige que os contêineres do Windows Server sejam baseados em imagens do *Windows Server 2019* ou superior. O arquivo manifesto Kubernetes também deve definir um [seletor de nó][node-selector] para dizer ao seu cluster AKS para executar o pod do aplicativo de amostra de ASP.NET em um nó que pode executar contêineres do Windows Server.

Crie um arquivo chamado `sample.yaml` e copie a definição YAML a seguir. Se você usar o Azure Cloud Shell, esse arquivo poderá ser criado usando `vi` ou `nano`, como se você estivesse trabalhando em um sistema físico ou virtual:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Implante o aplicativo usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```console
kubectl apply -f sample.yaml
```

O exemplo a seguir mostra a implantação e o serviço criados com sucesso:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos. Ocasionalmente, o serviço pode levar mais do que alguns minutos para ser provido. Aguarde até 10 minutos nesses casos.

Para monitorar o andamento, use o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```console
kubectl get service sample --watch
```

Inicialmente, o *IP EXTERNO* para o serviço *de amostra* é mostrado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo de exemplo em ação, abra um navegador da Web para o endereço IP externo do seu serviço.

![Imagem de navegação para ASP.NET aplicativo de amostra](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Se você receber um tempo de conexão ao tentar carregar a página, você deve verificar se o aplicativo de amostra está pronto com o seguinte comando [kubectl obter pods --watch]. Às vezes, o recipiente do windows não será iniciado quando seu endereço IP externo estiver disponível.

## <a name="delete-cluster"></a>Excluir cluster

Quando o cluster não for mais necessário, use o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter etapas sobre como remover a entidade de serviço, veja [Considerações sobre a entidade de segurança e a exclusão de serviço AKS][sp-delete].

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um cluster Kubernetes e implantou um aplicativo de amostra de ASP.NET em um contêiner do Windows Server para ele. [Acesse o painel da Web do Kubernetes][kubernetes-dashboard] para o cluster recém-criado.

Para saber mais sobre o AKS e percorrer um código completo de exemplo de implantação, prossiga para o tutorial de cluster Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial aKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
