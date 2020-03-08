---
title: Criar um cluster do Azure Red Hat OpenShift 4,3 | Microsoft Docs
description: Criar um cluster com o Azure Red Hat OpenShift 3,11
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: toa, openshift, AZ aro, Red Hat, CLI
ms.openlocfilehash: 81f8edb42be1f73692062d36440890ef5a1e7c9a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899302"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Criar, acessar e gerenciar um cluster do Azure Red Hat OpenShift 4,3

> [!IMPORTANT]
> O Azure Red Hat OpenShift (ARO) 4,3 é oferecido na versão prévia. Os recursos de visualização são autoatendimento e são fornecidos como estão e disponíveis e são excluídos do SLA (contrato de nível de serviço) e da garantia limitada. Portanto, os recursos não são destinados ao uso em produção.

## <a name="prerequisites"></a>Prerequisites

Você precisará do seguinte para criar um cluster do Azure Red Hat OpenShift 4,3:

- CLI do Azure versão 2.0.72 ou posterior
  
- A extensão ' AZ toa '

- Uma rede virtual que contém duas sub-redes vazias, cada uma sem um grupo de segurança de rede anexado.  O cluster será implantado nessas sub-redes.

- Um aplicativo AAD do cluster (ID do cliente e segredo) e uma entidade de serviço ou permissões do AAD suficientes para `az aro create` para criar um aplicativo AAD e uma entidade de serviço para você automaticamente.

- A entidade de serviço RP e a entidade de serviço de cluster devem ter a função de colaborador na rede virtual do cluster.  Se você tiver a função de "administrador de acesso do usuário" na rede virtual, `az aro create` irá configurar as atribuições de função automaticamente.

### <a name="install-the-az-aro-extension"></a>Instalar a extensão ' AZ toa '
A extensão `az aro` permite que você crie, acesse e exclua clusters do Azure Red Hat OpenShift diretamente da linha de comando usando o CLI do Azure.

> [!Note] 
> A extensão de `az aro` é atual na versão prévia. Ele pode ser alterado ou removido em uma versão futura.
> Para aceitar a visualização da extensão de `az aro`, você precisa registrar o provedor de recursos de `Microsoft.RedHatOpenShift`.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Fazer logon no Azure.

   ```console
   az login
   ```

2. Execute o seguinte comando para instalar a extensão de `az aro`:

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. Verifique se a extensão da toa está registrada.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Criar uma rede virtual contendo duas sub-redes vazias

Siga estas etapas para criar uma rede virtual que contém duas sub-redes vazias.

1. Defina as variáveis a seguir.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Crie um grupo de recursos para o cluster.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Crie a rede virtual.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Adicione duas sub-redes vazias à sua rede virtual.

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Desabilite as políticas de rede para o serviço de vínculo privado em sua rede virtual e sub-redes. Esse é um requisito para que o serviço de toa acesse e gerencie o cluster.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Criar um cluster

Execute o comando a seguir para criar um cluster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> Normalmente, leva cerca de 35 minutos para criar um cluster.

## <a name="access-the-cluster-console"></a>Acessar o console de cluster

Você pode encontrar a URL do console de cluster (do formulário `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) no recurso de cluster do Azure Red Hat OpenShift 4,3. Execute o seguinte comando para exibir o recurso:

```console
az aro list -o table
```

Você pode fazer logon no cluster usando o `kubeadmin` usuário.  Execute o seguinte comando para localizar a senha para o usuário do `kubeadmin`:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Excluir um cluster

Execute o comando a seguir para excluir um cluster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```