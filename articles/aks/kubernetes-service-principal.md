---
title: Entidades de serviço para o AKS (Serviço de Kubernetes do Azure)
description: Criar e gerenciar uma entidade de serviço do Azure Active Directory para um cluster no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 2c792eb4dc060e3f5d7fa2d8f2176bdd51538c43
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392732"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Entidades de serviço com o AKS (Serviço de Kubernetes do Azure)

Para interagir com as APIs do Azure, um cluster AKS requer um [principal de serviço (AD) do Azure Active Directory (AD)][aad-service-principal] ou uma [identidade gerenciada](use-managed-identity.md). Um principal de serviço ou identidade gerenciada é necessário para criar e gerenciar dinamicamente outros recursos do Azure, como um balanceador de carga do Azure ou o Registro de Contêineres (ACR).

Este artigo mostra como criar e usar uma entidade de serviço para seus clusters do AKS.

## <a name="before-you-begin"></a>Antes de começar

Para criar uma entidade de serviço do Azure AD, você deve ter permissões para registrar um aplicativo com o locatário do Azure AD e para atribuir o aplicativo a uma função em sua assinatura. Se você não tiver as permissões necessárias, talvez precise solicitar ao administrador do seu Azure AD ou da assinatura que atribua as permissões necessárias ou crie previamente uma entidade de serviço para uso com o cluster do AKS.

Se você estiver usando um diretor de serviço de um inquilino Azure AD diferente, existem considerações adicionais em torno das permissões disponíveis quando você implanta o cluster. Você pode não ter as permissões apropriadas para ler e escrever informações do diretório. Para obter mais informações, consulte [Quais são as permissões de usuário padrão no Azure Active Directory?][azure-ad-permissions]

Você também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Criar e usar uma entidade de serviço automaticamente

Quando você cria um cluster do AKS no portal do Azure ou usando o comando [criar az aks][az-aks-create], o Azure pode gerar uma entidade de serviço automaticamente.

No exemplo a seguir da CLI do Azure, uma entidade de serviço não foi especificada. Neste cenário, a CLI do Azure cria um entidade de serviço para o cluster do AKS. Para concluir a operação com êxito, sua conta do Azure deve ter os direitos necessários para criar uma entidade de serviço.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Criar uma entidade de serviço manualmente

Para criar uma entidade de serviço com a CLI do Azure manualmente, use o comando [az ad sp create-for-rbac][az-ad-sp-create]. No exemplo abaixo, o parâmetro `--skip-assignment` impede que outras atribuições padrão sejam atribuídas:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

A saída deverá ser semelhante ao seguinte exemplo: Anote seu próprio `appId` e `password`. Esses valores serão usados quando você criar um cluster do AKS na próxima seção.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Especificar uma entidade de serviço para um cluster do AKS

Para usar uma entidade de serviço existente quando você criar um cluster do AKS usando o comando [criar az aks][az-aks-create], use os parâmetros `--service-principal` e `--client-secret` para especificar o `appId` e a `password` da saída do comando [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Se você estiver usando um diretor de serviço existente com segredo personalizado, certifique-se de que o segredo não é mais do que 190 bytes.

Se você implantar um cluster do AKS usando o portal do Azure, na página *Autenticação* do diálogo **Criar cluster do Kubernetes**, escolha **Configurar entidade de serviço**. Selecione **Usar existente** e especifique os seguintes valores:

- **ID do cliente da entidade de serviço** é seu *appId*
- **Segredo do cliente da entidade de serviço** é o valor da *senha*

![Imagem de navegação para o Voto do Azure](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegar acesso a outros recursos do Azure

A entidade de serviço do cluster do AKS pode ser usada para acessar outros recursos. Por exemplo, se você quiser implantar seu cluster AKS em uma sub-rede virtual azure existente ou se conectar ao ACR (ACR) de contêineres do Azure, você precisa delegar o acesso a esses recursos ao principal de serviço.

Para delegar permissões, crie uma atribuição de função usando o comando [az role assignment create.][az-role-assignment-create] Atribuir o `appId` a um escopo específico, como um grupo de recursos ou recurso de rede virtual. Em seguida, uma função define quais permissões a entidade de serviço tem no recurso, conforme mostrado no exemplo a seguir:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

O `--scope`, pois um recurso precisa ser uma ID de recurso completa, como */subscriptions/\<guid\>/resourceGroups/myResourceGroup* ou */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

As seções a seguir detalham as delegações comuns que talvez você precise fazer.

### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Se você usar o Azure Container Registry (ACR) como seu armazenamento de imagens de contêiner, você precisará conceder permissões ao diretor de serviço para que seu cluster AKS leia e puxe imagens. Atualmente, a configuração recomendada é usar o comando [az aks create][az-aks-create] ou [az aks update][az-aks-update] para integrar-se a um registro e atribuir a função apropriada para o diretor de serviço. Para obter etapas detalhadas, consulte [Authenticate com o Registro de Contêineres Azure do Azure Kubernetes Service][aks-to-acr].

### <a name="networking"></a>Rede

Você pode usar os recursos de rede quando rede e a sub-rede virtuais, ou endereços IP públicos, estiverem em outro grupo de recursos. Atribua um dos seguintes conjuntos de permissões de função:

- Crie uma [função personalizada][rbac-custom-role] e defina as permissões de função a seguir:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPEndereços/leitura*
  - *Microsoft.Network/publicIPAddresses/write*
- Ou, atribua a função interna [Colaborador de Rede][rbac-network-contributor] na sub-rede dentro da rede virtual

### <a name="storage"></a>Armazenamento

Talvez você precise acessar os recursos de disco existentes em outro grupo de recursos. Atribua um dos seguintes conjuntos de permissões de função:

- Crie uma [função personalizada][rbac-custom-role] e defina as permissões de função a seguir:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Ou, atribua a função interna [Colaborador da Conta de Armazenamento][rbac-storage-contributor] no grupo de recursos

### <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

Se você usar o Virtual Kubelet para integrar-se ao AKS e optar por executar ACI (Instâncias de Contêiner do Azure) no grupo de recursos separadamente do cluster do AKS, a entidade de serviço do AKS deverá receber permissões de *Colaborador* no grupo de recursos do ACI.

## <a name="additional-considerations"></a>Considerações adicionais

Ao usar o AKS e as entidades de serviço do Azure AD, tenha em mente as considerações a seguir.

- A entidade de serviço para o Kubernetes é parte da configuração do cluster. No entanto, não use a identidade para implantar o cluster.
- Por padrão, as credenciais principais do serviço são válidas por um ano. Você pode [atualizar ou girar as credenciais principais do serviço a][update-credentials] qualquer momento.
- Cada entidade de serviço é associada a um aplicativo Azure AD. O principal de serviço para um cluster Kubernetes pode ser associado a *https://www.contoso.org/example*qualquer nome de aplicativo Azure AD válido (por exemplo: ). A URL para o aplicativo não precisa ser um ponto de extremidade real.
- Ao especificar a **ID do cliente** da entidade de serviço, use o valor de `appId`.
- No nó do agente VMs no cluster Kubernetes, as credenciais principais de serviço são armazenadas no arquivo`/etc/kubernetes/azure.json`
- Se você usar o comando [az aks create][az-aks-create] para gerar a entidade de serviço automaticamente, as credenciais da entidade de serviço serão gravadas no arquivo `~/.azure/aksServicePrincipal.json` no computador usado para executar o comando.
- Se você não passar especificamente um principal de serviço em comandos Adicionais `~/.azure/aksServicePrincipal.json` AKS CLI, o principal de serviço padrão localizado em será usado.  
- Você também pode remover opcionalmente o arquivo aksServicePrincipal.json, e o AKS criará um novo diretor de serviço.
- Ao excluir um cluster do AKS que foi criado por [az aks create][az-aks-create], a entidade de serviço que foi criada automaticamente não será excluída.
    - Para excluir a entidade de serviço, consulte o cluster *servicePrincipalProfile.clientId* e, em seguida, exclua-a com [az ad app delete][az-ad-app-delete]. Substitua os seguintes nomes de cluster e de grupo de recursos por seus próprios valores:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Solucionar problemas

As principais credenciais de serviço para um cluster AKS são armazenadas em cache pela CLI do Azure. Se essas credenciais expirarem, você encontrará erros ao implantar clusters AKS. A seguinte mensagem de erro ao executar [aaks aks pode][az-aks-create] indicar um problema com as credenciais principais do serviço em cache:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Verifique a idade do arquivo de credenciais usando o seguinte comando:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

O prazo de validade padrão para as credenciais principais do serviço é de um ano. Se o seu arquivo *aksServicePrincipal.json* tiver mais de um ano, exclua o arquivo e tente implantar um cluster AKS novamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os diretores dos serviços do Azure Active Directory, consulte [objetos principais de aplicativos e serviços][service-principal].

Para obter informações sobre como atualizar as credenciais, consulte [Atualizar ou gire as credenciais de um diretor de serviço em AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
