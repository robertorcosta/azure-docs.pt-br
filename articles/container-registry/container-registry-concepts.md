---
title: About repositories & images
description: Introduction to key concepts of Azure container registries, repositories, and container images.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455280"
---
# <a name="about-registries-repositories-and-images"></a>About registries, repositories, and images

This article introduces the key concepts of container registries, repositories, and container images and related artifacts. 

## <a name="registry"></a>Registro

Um registro de *contêiner* é um serviço que armazena e distribui imagens de contêiner. Docker Hub is a public container registry that supports the open source community and serves as a general catalog of images. Azure Container Registry provides users with direct control of their images, with integrated authentication, [geo-replication](container-registry-geo-replication.md) supporting global distribution and reliability for network-close deployments, [virtual network and firewall configuration](container-registry-vnet.md), [tag locking](container-registry-image-lock.md), and many other enhanced features. 

In addition to Docker container images, Azure Container Registry supports related [content artifacts](container-registry-image-formats.md) including Open Container Initiative (OCI) image formats.

## <a name="content-addressable-elements-of-an-artifact"></a>Content addressable elements of an artifact

The address of an artifact in an Azure container registry includes the following elements. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** - The fully qualified name of the registry host. The registry host in an Azure container registry is in the format *myregistry*.azurecr.io (all lowercase). You must specify the loginUrl when using Docker or other client tools to pull or push artifacts to an Azure container registry. 
* **namespace** - Slash-delimited logical grouping of related images or artifacts - for example, for a workgroup or app
* **artifact** - The name of a repository for a particular image or artifact
* **tag** - A specific version of an image or artifact stored in a repository


For example, the full name of an image in an Azure container registry might look like:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

See the following sections for details about these elements.

## <a name="repository-name"></a>Nome do repositório

Container registries manage *repositories*, collections of container images or other artifacts with the same name, but different tags. Por exemplo, as três imagens a seguir estão no repositório "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Também podem incluir nomes de repositório [namespaces](container-registry-best-practices.md#repository-namespaces). Namespaces allow you to group images using forward slash-delimited repository names, for example:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Imagem

A container image or other artifact within a registry is associated with one or more tags, has one or more layers, and is identified by a manifest. Understanding how these components relate to each other can help you manage your registry effectively.

### <a name="tag"></a>Marca

The *tag* for an image or other artifact specifies its version. A single artifact within a repository can be assigned one or many tags, and may also be "untagged." That is, you can delete all tags from an image, while the image's data (its layers) remain in the registry.

O repositório (ou repositório e namespace) mais uma tag define o nome de uma imagem. Você pode empurrar e puxar uma imagem especificando seu nome na operação push ou pull.

How you tag container images is guided by your scenarios to develop or deploy them. For example, stable tags are recommended for maintaining your base images, and unique tags for deploying images. For more information, see [Recommendations for tagging and versioning container images](container-registry-image-tag-version.md).

### <a name="layer"></a>Camada

Container images are made up of one or more *layers*, each corresponding to a line in the Dockerfile that defines the image. As imagens em um registro compartilham camadas comuns, aumentando a eficiência do armazenamento. Por exemplo, várias imagens em repositórios diferentes podem compartilhar a mesma camada base de Linux Alpine, mas apenas uma cópia dessa camada é armazenada no registro.

Também é o compartilhamento de camada otimiza a distribuição de camada para nós com várias camadas comuns de compartilhamento de imagens. Por exemplo, se uma imagem já está em um nó inclui a camada de Linux Alpine como sua base, o pull subsequentes de uma imagem diferente referenciando a mesma camada não transfere a camada para o nó. Em vez disso, ele faz referência à camada já existente no nó.

To provide secure isolation and protection from potential layer manipulation, layers are not shared across registries.

### <a name="manifest"></a>Manifesto

Each container image or artifact pushed to a container registry is associated with a *manifest*. O manifesto, gerado pelo registro quando a imagem é enviada, identifica de forma exclusiva a imagem e especifica suas camadas. You can list the manifests for a repository with the Azure CLI command [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

For example, list the manifests for the "acr-helloworld" repository:

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
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Resumo do manifesto

Manifestos são identificados por um único hash SHA-256, ou *manifesto digest*. Each image or artifact--whether tagged or not--is identified by its digest. O valor de resumo é exclusivo, mesmo se os dados da camada da imagem forem idênticos aos de outra imagem. Esse mecanismo é o que permite que você envie repetidamente imagens com tags idênticas para um registro. Por exemplo, você pode enviar repetidamente `myimage:latest` para o seu registro sem erro, porque cada imagem é identificada pelo seu resumo único.

Você pode receber uma imagem de um registro, especificando seu resumo na operação de pull. Alguns sistemas podem ser configurados para extrair por digest, pois ela garante que a versão da imagem que está sendo extraída, mesmo se uma imagem de forma idêntica marcada subsequentemente é enviada por push ao registro.

For example, pull an image from the "acr-helloworld" repository by manifest digest:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se você enviar repetidamente imagens modificadas com marcas idênticas, poderá criar imagens órfãs - imagens sem tag, mas que ainda consomem espaço no registro. Imagens não marcadas não são mostradas na CLI do Azure ou no portal do Azure quando você lista ou exibe imagens por marca. No entanto, suas camadas ainda existem e consomem espaço no seu registro. Deleting an untagged image frees registry space when the manifest is the only one, or the last one, pointing to a particular layer. For information about freeing space used by untagged images, see [Delete container images in Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Próximos passos

Learn more about [image storage](container-registry-storage.md) and [supported content formats](container-registry-image-formats.md) in Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


