---
title: Tutorial – Conectar-se a um cluster do Red Hat OpenShift no Azure 4
description: Saiba como conectar um cluster do Red Hat OpenShift no Microsoft Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d7efe781f1ba2beb1fa7dd4fdaaad280fc789de2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204738"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Conectar-se a um cluster do Red Hat OpenShift 4 no Azure

Neste tutorial, parte dois de três, você se conectará a um cluster do Red Hat OpenShift no Azure executando o OpenShift 4 como o usuário do kubeadmin por meio do console Web do OpenShift. Você aprenderá como:
> [!div class="checklist"]
> * Obter as credenciais `kubeadmin` para o cluster
> * Instalar a CLI do OpenShift
> * Conectar-se a um cluster do Red Hat OpenShift no Azure usando a CLI do OpenShift

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um cluster do Red Hat OpenShift no Azure foi criado. Se você ainda não realizou essas etapas e gostaria de acompanhar, comece com o [Tutorial 1 – Criar um cluster do Red Hat OpenShift no Azure 4.](tutorial-create-cluster.md)

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.75 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Você pode fazer logon no cluster usando o usuário `kubeadmin`.  Execute o comando a seguir para localizar a senha para o usuário `kubeadmin`.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

A saída de exemplo a seguir mostra que a senha estará em `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Você pode encontrar a URL do console de cluster executando o comando a seguir, que será semelhante a `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Inicie a URL do console em um navegador e faça logon usando as credenciais `kubeadmin`.

![Tela de logon do Red Hat OpenShift no Azure](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instalar a CLI do OpenShift

Quando você estiver conectado ao console Web do OpenShift, clique no **?** no canto superior direito e então em **Ferramentas de Linha de Comando**. Baixe a versão apropriada para seu computador.

![Tela de logon do Red Hat OpenShift no Azure](media/aro4-download-cli.png)

Você também pode baixar a versão mais recente da CLI apropriada para seu computador de <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>.

Se você estiver executando os comandos na Azure Cloud Shell, baixe a CLI mais recente do OpenShift 4 para Linux.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Conectar-se usando a CLI do OpenShift

Recupere o endereço do servidor de API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Faça logon no servidor de API do cluster OpenShift usando o comando a seguir. Substitua **\<senha do kubeadmin>** pela senha que você acabou de recuperar.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:
> [!div class="checklist"]
> * Obter as credenciais `kubeadmin` para o cluster
> * Instalar a CLI do OpenShift
> * Conectar-se a um cluster do Red Hat OpenShift no Azure usando a CLI do OpenShift

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Excluir um cluster do Red Hat OpenShift no Azure](tutorial-delete-cluster.md)