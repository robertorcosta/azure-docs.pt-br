---
title: Criar um cluster do Azure Red Hat OpenShift 4,3 | Microsoft Docs
description: Criar um cluster com o Azure Red Hat OpenShift 4,3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: toa, openshift, AZ aro, Red Hat, CLI
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398884"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Criar, acessar e gerenciar um cluster do Azure Red Hat OpenShift 4,3

> [!IMPORTANT]
> Observe que o Azure Red Hat OpenShift 4,3 está disponível atualmente apenas em versão prévia privada no leste dos EUA e leste dos EUA 2. A aceitação da visualização privada é apenas por convite. Certifique-se de registrar sua assinatura antes de tentar habilitar esse recurso: [registro de visualização privada do Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Os recursos de visualização são autoatendimento e são fornecidos como estão e disponíveis e são excluídos do SLA (contrato de nível de serviço) e da garantia limitada. Portanto, os recursos não são destinados ao uso em produção.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para criar um cluster do Azure Red Hat OpenShift 4,3:

- CLI do Azure versão 2.0.72 ou posterior
  
- A extensão ' AZ toa '

- Uma rede virtual que contém duas sub-redes vazias, cada uma sem um grupo de segurança de rede anexado.  O cluster será implantado nessas sub-redes.

- Um aplicativo AAD do cluster (ID do cliente e segredo) e a entidade de serviço ou permissões `az aro create` do AAD suficientes para que o crie um aplicativo AAD e uma entidade de serviço para você automaticamente.

- A entidade de serviço RP e a entidade de serviço de cluster devem ter a função de colaborador na rede virtual do cluster.  Se você tiver a função de "administrador de acesso do usuário" na rede `az aro create` virtual, o configurará as atribuições de função automaticamente.

### <a name="install-the-az-aro-extension"></a>Instalar a extensão ' AZ toa '
A `az aro` extensão permite que você crie, acesse e exclua clusters do Azure Red Hat OpenShift diretamente da linha de comando usando o CLI do Azure.

> [!Note] 
> A `az aro` extensão é atual na versão prévia. Ele pode ser alterado ou removido em uma versão futura.
> Para aceitar a `az aro` visualização da extensão, você precisa registrar o provedor de `Microsoft.RedHatOpenShift` recursos.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Fazer logon no Azure.

   ```console
   az login
   ```

2. Execute o seguinte comando para instalar a `az aro` extensão:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Verifique se a extensão da toa está registrada.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Obter um segredo de pull do Red Hat (opcional)

Um segredo de pull do Red Hat permite que o cluster acesse registros de contêiner do Red Hat e conteúdo adicional. O uso de um segredo de pull é opcional, mas recomendado.

Para obter seu segredo de pull:

1. Acesse https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Faça logon em sua conta do Red Hat ou crie uma nova conta do Red Hat usando seu email comercial; aceite os termos e condições.
1. Selecione **baixar segredo de pull**.

Salve o arquivo *pull-Secret. txt* em um local seguro; Você usará o arquivo cada vez que criar um cluster.

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
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> Normalmente, leva cerca de 35 minutos para criar um cluster.

## <a name="access-the-cluster-console"></a>Acessar o console de cluster

Você pode encontrar a URL do console de cluster (do `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`formulário) no recurso de cluster do Azure Red Hat OpenShift 4,3. Execute o seguinte comando para exibir o recurso:

```console
az aro list -o table
```

Você pode fazer logon no cluster usando o `kubeadmin` usuário.  Execute o seguinte comando para localizar a senha para o `kubeadmin` usuário:

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
