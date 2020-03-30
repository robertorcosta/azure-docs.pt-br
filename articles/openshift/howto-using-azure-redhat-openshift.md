---
title: Criar um cluster Azure Red Hat OpenShift 4.3 | Microsoft Docs
description: Crie um cluster com o Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.openlocfilehash: 423f09c135da51b8401c1933a4a271d0becd2c8f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349427"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Criar, acessar e gerenciar um cluster Azure Red Hat OpenShift 4.3

> [!IMPORTANT]
> Observe que o Azure Red Hat OpenShift 4.3 está atualmente disponível apenas em pré-visualização privada no Leste dos EUA. A aceitação de pré-visualização privada é apenas por convite. Certifique-se de registrar sua assinatura antes de tentar habilitar este recurso: [Azure Red Hat OpenShift Private Preview Registration](https://aka.ms/aro-preview-register)

> [!NOTE]
> Os recursos de visualização são de autoatendimento e são fornecidos como está e disponível e são excluídos do contrato de nível de serviço (SLA) e garantia limitada. Portanto, os recursos não são feitos para uso de produção.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para criar um cluster Azure Red Hat OpenShift 4.3:

- Azure CLI versão 2.0.72 ou superior
  
- A extensão 'az aro'

- Uma rede virtual contendo duas sub-redes vazias, cada uma sem nenhum grupo de segurança de rede conectado.  Seu cluster será implantado nessas sub-redes.

- Um aplicativo AAD de cluster (ID cliente e secreto) e `az aro create` o diretor de serviço, ou permissões AAD suficientes para criar um aplicativo E serviço AAD para você automaticamente.

- O principal de serviço de RP e o principal de serviço de cluster devem ter cada um a função Contribuinte na rede virtual de cluster.  Se você tiver a função "Administrador de `az aro create` Acesso ao Usuário" na rede virtual, configurará as atribuições de função para você automaticamente.

### <a name="install-the-az-aro-extension"></a>Instale a extensão 'az aro'
A `az aro` extensão permite criar, acessar e excluir clusters Azure Red Hat OpenShift diretamente da linha de comando usando o Cli do Azure.

> [!Note] 
> A `az aro` extensão está em fase de pré-visualização. Ele pode ser alterado ou removido em uma versão futura.
> Para optar pela `az aro` pré-visualização de `Microsoft.RedHatOpenShift` extensão, você precisa registrar o provedor de recursos.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Fazer logon no Azure.

   ```console
   az login
   ```

2. Execute o seguinte comando `az aro` para instalar a extensão:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Verifique se a extensão ARO está registrada.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Crie uma rede virtual contendo duas sub-redes vazias

Siga essas etapas para criar uma rede virtual contendo duas sub-redes vazias.

1. Defina as variáveis a seguir.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   PULL_SECRET="<optional-pull-secret>"
   ```
   >[!NOTE]
   > O segredo de tração opcional permite que seu cluster acesse os registros de contêineres red hat, juntamente com conteúdo adicional.
   >
   > Acesse seu segredo de https://cloud.redhat.com/openshift/install/azure/installer-provisioned atração navegando e clicando em *Copy Pull Secret*.
   >
   > Você precisará fazer login na sua conta red hat ou criar uma nova conta red hat com seu e-mail comercial e aceitar os termos e condições.
 

2. Crie um grupo de recursos para o seu cluster.

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

5. Desative as políticas de rede para o Serviço de Link Privado em sua rede virtual e sub-redes. Este é um requisito para que o serviço ARO acesse e gerencie o cluster.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Criar um cluster

Execute o seguinte comando para criar um cluster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --pull-secret "$PULL_SECRET"
```

>[!NOTE]
> Normalmente leva cerca de 35 minutos para criar um cluster.

## <a name="access-the-cluster-console"></a>Acesse o console de cluster

Você pode encontrar a URL do `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`console de cluster (do formulário) o recurso de cluster Azure Red Hat OpenShift 4.3. Execute o seguinte comando para exibir o recurso:

```console
az aro list -o table
```

Você pode fazer login `kubeadmin` no cluster usando o usuário.  Execute o seguinte comando para `kubeadmin` encontrar a senha para o usuário:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Excluir um cluster

Execute o seguinte comando para excluir um cluster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
