---
title: Delete image resources
description: Details on how to effectively manage registry size by deleting container image data using Azure CLI commands.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8d20bf2be1d472855c3e67dd79ea1725c152e3d2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455274"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Delete container images in Azure Container Registry using the Azure CLI

Para manter o tamanho do registro do contêiner do Azure, exclua periodicamente os dados da imagem obsoletos. Embora algumas imagens de contêiner implantadas na produção possam exigir armazenamento de longo prazo, outras normalmente podem ser excluídas mais rapidamente. Por exemplo, em um cenário de criação e teste automatizado, o registro pode ser preenchido rapidamente com imagens que talvez nunca sejam implementadas e podem ser eliminadas logo após a conclusão da passagem de teste e de compilação.

Como você pode excluir dados de imagem de várias maneiras diferentes, é importante entender como cada operação de exclusão afeta o uso do armazenamento. This article covers several methods for deleting image data:

* Exclua um [repositório](#delete-repository): Exclui todas as imagens e todas as camadas exclusivas do repositório.
* Excluir pela [tag](#delete-by-tag): exclui uma imagem, a tag, todas as camadas exclusivas mencionadas pela imagem e todas as outras tags associadas à imagem.
* Exclua [digest manifesto](#delete-by-manifest-digest): exclui uma imagem, todas as camadas exclusivas referenciadas pela imagem e todas as marcas associadas à imagem.

Sample scripts are provided to help automate delete operations.

For an introduction to these concepts, see [About registries, repositories, and images](container-registry-concepts.md).

## <a name="delete-repository"></a>Excluir repositório

A exclusão de um repositório exclui todas as imagens no repositório, incluindo todas as tags, camadas exclusivas e manifestos. When you delete a repository, you recover the storage space used by the images that reference unique layers in that repository.

O seguinte comando da CLI do Azure exclui o repositório "acr-helloworld" e todas as tags e manifestos no repositório. If layers referenced by the deleted manifests are not referenced by any other images in the registry, their layer data is also deleted, recovering the storage space.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Excluir por marca

Você pode excluir imagens individuais de um repositório especificando o nome e a marca do repositório na operação de exclusão. Quando você exclui por marca, recupera o espaço de armazenamento usado por qualquer camada exclusiva na imagem (camadas não compartilhadas por outras imagens no registro).

To delete by tag, use [az acr repository delete][az-acr-repository-delete] and specify the image name in the `--image` parameter. Todas as camadas exclusivas da imagem e quaisquer outras tags associadas à imagem são excluídas.

Por exemplo, excluindo a imagem "acr-helloworld: latest" do registro "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> A exclusão de *pela tag* não deve ser confundida com a exclusão de uma tag (desmarcação). You can delete a tag with the Azure CLI command [az acr repository untag][az-acr-repository-untag]. No space is freed when you untag an image because its [manifest](container-registry-concepts.md#manifest) and layer data remain in the registry. Somente a referência de marca em si é excluída.

## <a name="delete-by-manifest-digest"></a>Excluir pelo manifesto digest

Um [resumo do manifesto](container-registry-concepts.md#manifest-digest) pode ser associado a uma, nenhuma ou várias tags. Quando você exclui por digest, todas as tags referenciadas pelo manifesto são excluídas, assim como os dados da camada para qualquer camada exclusiva da imagem. Compartilhado a camada de dados não são excluídos.

Para excluir, digest, a primeira lista o manifesto resumos para o repositório que contém as imagens que você deseja excluir. Por exemplo:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Next, specify the digest you wish to delete in the [az acr repository delete][az-acr-repository-delete] command. O formato do comando é:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Por exemplo, para excluir o último manifesto listado na saída anterior (com a tag "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

The `acr-helloworld:v2` image is deleted from the registry, as is any layer data unique to that image. Se um manifesto é associado com várias marcas, todas as marcas associadas também são excluídas.

## <a name="delete-digests-by-timestamp"></a>Delete digests by timestamp

To maintain the size of a repository or registry, you might need to periodically delete manifest digests older than a certain date.

The following Azure CLI command lists all manifest digest in a repository older than a specified timestamp, in ascending order. Substitua `<acrName>` e `<repositoryName>` por valores apropriados para o seu ambiente. The timestamp could be a full date-time expression or a date, as in this example.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

After identifying stale manifest digests, you can run the following Bash script to delete manifest digests older than a specified timestamp. Ele requer a CLI do Azure e **xargs**. Por padrão, o script não realiza nenhuma exclusão. Altere o `ENABLE_DELETE` valor para `true` para ativar a exclusão da imagem.

> [!WARNING]
> Use the following sample script with caution--deleted image data is UNRECOVERABLE. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. Deleting the manifest digests will prevent those systems from pulling the images from your registry. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Excluir imagens sem marcas

Conforme mencionado na [manifesto digest](container-registry-concepts.md#manifest-digest) seção, enviar por push uma imagem modificada usando uma marca existente **untags** a imagem anteriormente enviada, resultando em uma imagem órfão (ou "pendentes"). A imagem anteriormente enviada de manifesto – e seus dados de camada-- permanece no registro. Considere a seguinte sequência de eventos:

1. Enviar imagem por push *acr helloworld* com marca **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos do repositório *acr helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modifique *acr helloworld* Dockerfile
1. Enviar imagem por push *acr helloworld* com marca **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos do repositório *acr helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

As you can see in the output of the last step in the sequence, there is now an orphaned manifest whose `"tags"` property is an empty list. Esse manifesto ainda existe no registro, junto com todos os dados de camada exclusivos que ele referencia. **Para excluir tais órfão imagens e seus dados de camada, você deve excluir digest manifesto**.

## <a name="delete-all-untagged-images"></a>Excluir todas as imagens não marcas

Você pode listar todas as imagens não identificadas em seu repositório usando o seguinte comando da CLI do Azure. Substitua `<acrName>` e `<repositoryName>` por valores apropriados para o seu ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Using this command in a script, you can delete all untagged images in a repository.

> [!WARNING]
> Use os scripts de exemplo a seguir com cuidado – excluído dados de imagem são IRRECUPERÁVEL. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. A exclusão de imagens não marcadas impedirá esses sistemas de puxar as imagens do seu registro. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md).

**CLI do Azure no Bash**

O script Bash a seguir exclui todas as imagens não marcadas de um repositório. Ele requer a CLI do Azure e **xargs**. Por padrão, o script não realiza nenhuma exclusão. Altere o `ENABLE_DELETE` valor para `true` para ativar a exclusão da imagem.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**CLI do Azure no PowerShell**

O seguinte script do PowerShell exclui todas as imagens não identificadas de um repositório. Ele requer o PowerShell e a CLI do Azure. Por padrão, o script não realiza nenhuma exclusão. Altere o `$enableDelete` valor para `$TRUE` para ativar a exclusão da imagem.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Limpar automaticamente as marcas e os manifestos (versão prévia)

As an alternative to scripting Azure CLI commands, run an on-demand or scheduled ACR task to delete all tags that are older than a certain duration or match a specified name filter. For more information, see [Automatically purge images from an Azure container registry](container-registry-auto-purge.md).

Optionally set a [retention policy](container-registry-retention-policy.md) for each registry, to manage untagged manifests. When you enable a retention policy, image manifests in the registry that don't have any associated tags, and the underlying layer data, are automatically deleted after a set period.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o armazenamento de imagens no Azure Container Registry, consulte [Armazenamento de imagens de contêiner no Registro de Contêiner do Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
