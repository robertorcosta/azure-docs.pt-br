---
title: Sobre repositórios & imagens
description: Introdução aos conceitos-chave de registros de contêineres do Azure, repositórios e imagens de contêineres.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: ea6e2577d3eee91626dd613617a0b79e4ff3d6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247053"
---
# <a name="about-registries-repositories-and-images"></a>Sobre registros, repositórios e imagens

Este artigo apresenta os principais conceitos de registros de contêineres, repositórios e imagens de contêineres e artefatos relacionados. 

## <a name="registry"></a>Registro

Um registro de *contêiner* é um serviço que armazena e distribui imagens de contêiner. Docker Hub é um registro público de contêineres que suporta a comunidade de código aberto e serve como um catálogo geral de imagens. O Azure Container Registry oferece aos usuários controle direto de suas imagens, com autenticação integrada, [geo-replicação suportando](container-registry-geo-replication.md) distribuição global e confiabilidade para implantações de fechamento de rede, [configuração de rede virtual e firewall,](container-registry-vnet.md)bloqueio de [marcas](container-registry-image-lock.md)e muitos outros recursos aprimorados. 

Além das imagens de contêiner do Docker, o Azure Container Registry suporta artefatos de conteúdo relacionados, incluindo [formatos](container-registry-image-formats.md) de imagem Open Container Initiative (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementos endereçados ao conteúdo de um artefato

O endereço de um artefato em um registro de contêiner do Azure inclui os seguintes elementos. 

`[loginUrl]/[namespace]/[artifact:][tag]`

* **loginUrl** - O nome totalmente qualificado do host de registro. O hospedeiro de registro em um registro de contêiner do Azure está no formato *myregistry*.azurecr.io (todas as minúsculas). Você deve especificar o loginUrl ao usar docker ou outras ferramentas cliente para puxar ou empurrar artefatos para um registro de contêiner do Azure. 
* **namespace** - Agrupamento lógico delimitado de slash de imagens ou artefatos relacionados - por exemplo, para um grupo de trabalho ou aplicativo
* **artefato** - O nome de um repositório para uma determinada imagem ou artefato
* **tag** - Uma versão específica de uma imagem ou artefato armazenado em um repositório


Por exemplo, o nome completo de uma imagem em um registro de contêiner do Azure pode parecer:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Consulte as seções a seguir para obter detalhes sobre esses elementos.

## <a name="repository-name"></a>Nome do repositório

Os registros de contêineres gerenciam *repositórios,* coleções de imagens de contêineres ou outros artefatos com o mesmo nome, mas marcas diferentes. Por exemplo, as três imagens a seguir estão no repositório "acr-helloworld":


- *acr-helloworld:mais recente*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Também podem incluir nomes de repositório [namespaces](container-registry-best-practices.md#repository-namespaces). Os namespaces permitem agrupar imagens usando nomes de repositórios delimitados por barra, por exemplo:

- *marketing/campanha10-18/web:v2*
- *marketing/campanha10-18/api:v3*
- *marketing/campanha10-18/e-mail-remetente:v2*
- *retornos de produtos/envio web:20180604*
- *retornos de produtos/herdador-integrador:20180715*

## <a name="image"></a>Imagem

Uma imagem de contêiner ou outro artefato dentro de um registro está associada a uma ou mais tags, tem uma ou mais camadas e é identificada por um manifesto. Entender como esses componentes se relacionam entre si pode ajudá-lo a gerenciar seu registro de forma eficaz.

### <a name="tag"></a>Marca

A *tag* para uma imagem ou outro artefato especifica sua versão. Um único artefato dentro de um repositório pode ser atribuído uma ou muitas tags, e também pode ser "demarcado". Ou seja, você pode excluir todas as tags de uma imagem, enquanto os dados da imagem (suas camadas) permanecem no registro.

O repositório (ou repositório e namespace) mais uma tag define o nome de uma imagem. Você pode empurrar e puxar uma imagem especificando seu nome na operação push ou pull.

Como você marca imagens de contêiner é guiado por seus cenários para desenvolvê-las ou implantá-las. Por exemplo, tags estáveis são recomendadas para manter suas imagens base e tags exclusivas para a implantação de imagens. Para obter mais informações, consulte [Recomendações para marcar e versiar imagens de contêineres](container-registry-image-tag-version.md).

### <a name="layer"></a>Camada

As imagens do contêiner são compostas por uma ou mais *camadas,* cada uma correspondendo a uma linha no Arquivo Docker que define a imagem. As imagens em um registro compartilham camadas comuns, aumentando a eficiência do armazenamento. Por exemplo, várias imagens em repositórios diferentes podem compartilhar a mesma camada base de Linux Alpine, mas apenas uma cópia dessa camada é armazenada no registro.

Também é o compartilhamento de camada otimiza a distribuição de camada para nós com várias camadas comuns de compartilhamento de imagens. Por exemplo, se uma imagem já está em um nó inclui a camada de Linux Alpine como sua base, o pull subsequentes de uma imagem diferente referenciando a mesma camada não transfere a camada para o nó. Em vez disso, ele faz referência à camada já existente no nó.

Para fornecer isolamento seguro e proteção contra possíveis manipulações de camadas, as camadas não são compartilhadas entre os registros.

### <a name="manifest"></a>Manifest

Cada imagem de contêiner ou artefato empurrado para um registro de contêiner está associado a um *manifesto*. O manifesto, gerado pelo registro quando a imagem é enviada, identifica de forma exclusiva a imagem e especifica suas camadas. Você pode listar os manifestos de um repositório com o comando da CLI do Azure [az acr repository show-manifests ][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, liste os manifestos para o repositório "acr-helloworld":

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
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

Manifestos são identificados por um único hash SHA-256, ou *manifesto digest*. Cada imagem ou artefato - marcado ou não - é identificado por sua digestão. O valor de resumo é exclusivo, mesmo se os dados da camada da imagem forem idênticos aos de outra imagem. Esse mecanismo é o que permite que você envie repetidamente imagens com tags idênticas para um registro. Por exemplo, você pode enviar repetidamente `myimage:latest` para o seu registro sem erro, porque cada imagem é identificada pelo seu resumo único.

Você pode receber uma imagem de um registro, especificando seu resumo na operação de pull. Alguns sistemas podem ser configurados para extrair por digest, pois ela garante que a versão da imagem que está sendo extraída, mesmo se uma imagem de forma idêntica marcada subsequentemente é enviada por push ao registro.

Por exemplo, puxe uma imagem do repositório "acr-helloworld" por digestão manifesto:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Se você enviar repetidamente imagens modificadas com marcas idênticas, poderá criar imagens órfãs - imagens sem tag, mas que ainda consomem espaço no registro. Imagens não marcadas não são mostradas na CLI do Azure ou no portal do Azure quando você lista ou exibe imagens por marca. No entanto, suas camadas ainda existem e consomem espaço no seu registro. Excluir uma imagem não marcada libera o espaço de registro quando o manifesto é o único, ou o último, apontando para uma camada específica. Para obter informações sobre a liberação do espaço usado por imagens não marcadas, consulte [Excluir imagens de contêiner no Registro de Contêineres do Azure](container-registry-delete.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [armazenamento de imagens](container-registry-storage.md) e [formatos de conteúdo suportados](container-registry-image-formats.md) no Registro de Contêineres do Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


