---
title: (PRETERIDO) Tutorial do Serviço de Contêiner do Azure – preparar o ACR
description: Tutorial do Serviço de Contêiner do Azure – preparar o ACR
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 087530fd3834c4ec4620c087134bee0ed26bb6c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399789"
---
# <a name="deprecated-deploy-and-use-azure-container-registry"></a>(PRETERIDO) Implantar e usar o Registro de Contêiner do Azure

> [!TIP]
> Para a versão atualizada deste tutorial que usa o Serviço de Kubernetes do Azure, confira [Tutorial: Implantar e usar o Registro de Contêiner do Azure](../../aks/tutorial-kubernetes-prepare-acr.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

O ACR (Registro de Contêiner do Azure) é um registro privado baseado no Azure, para imagens de contêiner do Docker. Este tutorial, parte dois de sete, demonstra a implantação de uma instância de Registro de Contêiner do Azure e envia por push uma imagem de contêiner. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantando uma instância de ACR (Registro de Contêiner do Azure)
> * Marcando uma imagem de contêiner para ACR
> * Carregando da imagem para ACR

Nos próximos tutoriais, essa instância do ACR será integrada a um cluster do Kubernetes do Serviço de Contêiner do Azure. 

## <a name="before-you-begin"></a>Antes de começar

No [tutorial anterior](./container-service-tutorial-kubernetes-prepare-app.md), uma imagem de contêiner foi criada para um aplicativo de Votação do Azure simples. Se você não tiver criado a imagem do aplicativo de Votação do Azure, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md).

Este tutorial exige que você esteja executando a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Ao implantar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Neste exemplo, criaremos um grupo de recursos `myResourceGroup` na região `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Crie um Registro de Contêiner do Azure com o comando [az acr create](/cli/azure/acr#az-acr-create). O nome de um registro de contêiner **deve ser exclusivo**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Durante o restante deste tutorial, utilizamos `<acrname>` como um espaço reservado para o nome do registro de contêiner.

## <a name="container-registry-login"></a>Logon no registro de contêiner

Use o comando [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) para fazer logon na instância do ACR. Você precisa fornecer o nome exclusivo fornecido para o registro de contêiner quando ele foi criado.

```azurecli
az acr login --name <acrName>
```

O comando retorna uma mensagem de 'Logon bem-sucedido' quando é concluído.

## <a name="tag-container-images"></a>Marcar imagens de contêiner

Para consultar uma lista de imagens atuais, utilize o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Saída:

```output
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Cada imagem de contêiner precisa ser marcada com o nome do registro loginServer. Essa marca é usada para roteamento ao enviar imagens de contêiner por push a um registro da imagem.

Para obter o nome de loginServer, execute o comando a seguir.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Agora, marque a imagem `azure-vote-front` com o loginServer do registro de contêiner. Além disso, adicione `:v1` ao final do nome da imagem. Esta marcação indica a versão da imagem.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Depois de marcada, execute [docker images](https://docs.docker.com/engine/reference/commandline/images/) para verificar a operação.

```bash
docker images
```

Saída:

```output
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Envie a imagem `azure-vote-front` por push ao registro. 

Usando o exemplo a seguir, substitua o nome do loginServer do ACR pelo loginServer do seu ambiente.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Isso leva alguns minutos para ser concluído.

## <a name="list-images-in-registry"></a>Lista de imagens no registro

Para retornar uma lista de imagens que foram enviadas por push ao Registro de Contêiner do Azure, use o comando [az acr repository list](/cli/azure/acr/repository#az-acr-repository-list). Atualize o comando com o nome da instância do ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```output
Result
----------------
azure-vote-front
```

E, em seguida, para ver as marcas de uma imagem específica, use o comando [az acr repository show-tags](/cli/azure/acr/repository).

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Saída:

```output
Result
--------
v1
```

Na conclusão do tutorial, a imagem de contêiner foi armazenada em uma instância privada de Registro de Contêiner do Azure. Essa imagem será implantada do ACR para um cluster Kubernetes em tutoriais subsequentes.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um Registro de Contêiner do Azure foi preparado para ser usado em um cluster do Kubernetes do ACS. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantando uma instância de Registro de Contêiner do Azure
> * Marcando uma imagem de contêiner para ACR
> * Carregando a imagem para ACR

Avance para o próximo tutorial para saber mais sobre a implantação de um cluster do Kubernetes no Azure.

> [!div class="nextstepaction"]
> [Implantar um cluster do Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
