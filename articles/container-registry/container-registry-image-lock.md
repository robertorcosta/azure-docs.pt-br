---
title: Bloquear imagens
description: Defina atributos para uma imagem de contêiner ou repositório para que não possa ser excluído ou substituído em um registro de contêiner do Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659689"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloquear uma imagem de contêiner em um registro de contêiner Do Zure

Em um registro de contêiner do Azure, você pode bloquear uma versão de imagem ou um repositório para que ele não possa ser excluído ou atualizado. Para bloquear uma imagem ou um repositório, atualize seus atributos usando o comando Azure CLI [az acr repositório update][az-acr-repository-update]. 

Este artigo exige que você execute o Azure CLI no Azure Cloud Shell ou localmente (versão 2.0.55 ou posterior recomendada). Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli].

> [!IMPORTANT]
> Este artigo não se aplica ao bloqueio de um registro inteiro, por exemplo, `az lock` usando **Configurações > Bloqueios** no portal Azure ou comandos no CLI do Azure. Bloquear um recurso de registro não impede que você crie, atualize ou exclua dados em repositórios. O bloqueio de um registro afeta apenas as operações de gerenciamento, como adicionar ou excluir replicação ou excluir o próprio registro. Mais informações nos [recursos do Lock para evitar alterações inesperadas](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Cenários

Por padrão, uma imagem marcada no Registro de Contêineres do Azure é *mutável,* portanto, com as permissões apropriadas, você pode atualizar e empurrar repetidamente uma imagem com a mesma tag para um registro. As imagens do contêiner também podem ser [excluídas](container-registry-delete.md) conforme necessário. Esse comportamento é útil quando você desenvolve imagens e precisa manter um tamanho para o seu registro.

No entanto, quando você implanta uma imagem de contêiner para a produção, você pode precisar de uma imagem de contêiner *imutável.* Uma imagem imutável é aquela que você não pode excluir ou substituir acidentalmente.

Consulte [recomendações para marcar e versificar imagens de contêiner](container-registry-image-tag-version.md) para estratégias de marcação e imagens de versão em seu registro.

Use o comando [az acr repository update][az-acr-repository-update] para definir atributos de repositório para que você possa:

* Bloqueie uma versão de imagem ou um repositório inteiro

* Proteja uma versão de imagem ou repositório da exclusão, mas permita atualizações

* Impedir operações de leitura (puxar) em uma versão de imagem ou em um repositório inteiro

Veja as seções a seguir, por exemplo. 

## <a name="lock-an-image-or-repository"></a>Bloquear uma imagem ou repositório 

### <a name="show-the-current-repository-attributes"></a>Mostrar os atributos do repositório atual
Para ver os atributos atuais de um repositório, execute o seguinte comando [az acr repositório show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Mostrar os atributos de imagem atuais
Para ver os atributos atuais de uma tag, execute o seguinte comando [az acr repositório show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Bloquear uma imagem por tag

Para bloquear o *myrepo/myimage:tag* image in *myregistry*, execute o seguinte comando [de atualização do repositório az acr:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bloquear uma imagem por digestão manifesto

Para bloquear uma imagem *myrepo/myimage* identificada pelo manifest digest (hash SHA-256, representado como `sha256:...`), execute o seguinte comando. (Para encontrar o digerir manifesto associado a uma ou mais tags de imagem, execute o comando [az acr repository show-manifests.)][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Bloquear um repositório

Para bloquear o *repositório myrepo/myimage* e todas as imagens nele, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Proteja uma imagem ou repositório contra a exclusão

### <a name="protect-an-image-from-deletion"></a>Proteger uma imagem da exclusão

Para permitir que a imagem *myrepo/myimage:tag* seja atualizada, mas não excluída, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Proteja um repositório contra a exclusão

O comando a seguir define o repositório *myrepo/myimage* para que não possa ser excluído. Imagens individuais ainda podem ser atualizadas ou excluídas.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Impedir operações de leitura em uma imagem ou repositório

Para evitar operações de leitura (pull) na imagem *myrepo/myimage:tag,* execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Para evitar operações de leitura em todas as imagens no repositório *myrepo/myimage,* execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Desbloquear uma imagem ou repositório

Para restaurar o comportamento padrão da imagem *myrepo/myimage:tag* para que ela possa ser excluída e atualizada, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Para restaurar o comportamento padrão do repositório *myrepo/myimage* e todas as imagens para que possam ser excluídas e atualizadas, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o uso do comando [az acr repositório update][az-acr-repository-update] para evitar exclusão ou atualização de versões de imagem em um repositório. Para definir atributos adicionais, consulte a referência de comando de atualização do [repositório az acr.][az-acr-repository-update]

Para ver os atributos definidos para uma versão de imagem ou repositório, use o comando [az acr repository show.][az-acr-repository-show]

Para obter detalhes sobre operações de exclusão, consulte [Excluir imagens de contêiner no Registro de Contêineres do Azure][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

