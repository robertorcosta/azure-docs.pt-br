---
title: Formatos de conteúdo suportados
description: Conheça os formatos de conteúdo suportados pelo Azure Container Registry, incluindo imagens de contêiner compatíveis com Docker, gráficos Helm, imagens OCI e artefatos OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247001"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formatos de conteúdo com suporte no Registro de Contêiner do Azure

Use um repositório privado no Registro de Contêiner do Azure para gerenciar um dos seguintes formatos de conteúdo. 

## <a name="docker-compatible-container-images"></a>Imagens de contêiner compatíveis com Docker

Os seguintes formatos de imagem de contêiner Docker são suportados:

* [Manifesto de imagem de Docker V2, Esquema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto de imagem de Docker V2, Esquema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - inclui Listas de Manifestos que permitem que os registros armazenem imagens multiplataforma sob uma única referência "image:tag"

## <a name="oci-images"></a>Imagens OCI

O Registro de Contêineres do Azure suporta imagens que atendem à especificação de formato de imagem da Open [Container Initiative (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md) Os formatos de embalagem incluem [O Formato de Imagem singularidade (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Artefatos OCI

O Azure Container Registry suporta a [Especificação de Distribuição OCI](https://github.com/opencontainers/distribution-spec), uma especificação neutra em nuvem e neutra do fornecedor para armazenar, compartilhar, proteger e implantar imagens de contêineres e outros tipos de conteúdo (artefatos). A especificação permite que um registro armazene uma ampla gama de artefatos, além de imagens de contêineres. Você usa ferramentas apropriadas ao artefato para empurrar e puxar artefatos. Por exemplo, consulte [Empurre e puxe um artefato OCI usando um registro de contêiner Azure](container-registry-oci-artifacts.md).

Para saber mais sobre os artefatos OCI, consulte o repo [do Registro OCI como Armazenamento (ORAS)](https://github.com/deislabs/oras) e o repo [de artefatos OCI](https://github.com/opencontainers/artifacts) no GitHub.

## <a name="helm-charts"></a>Gráficos Helm

O Azure Container Registry pode hospedar repositórios para [gráficos Helm](https://helm.sh/), um formato de embalagem usado para gerenciar e implantar aplicativos rapidamente para Kubernetes. [A](https://docs.helm.sh/using_helm/#installing-helm) versão 2 do cliente helm (2.11.0 ou posterior) é suportada.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [efetuar push e efetuar pull](container-registry-get-started-docker-cli.md) de imagens com Registro de Contêiner do Azure.

* Use [tarefas do ACR](container-registry-tasks-overview.md) para compilar e testar imagens de contêiner. 

* Use o [Moby BuildKit](https://github.com/moby/buildkit) para compilar e empacotar contêineres no formato de OCI.

* Configure um [repositório Helm](container-registry-helm-repos.md) hospedado no Registro de Contêiner do Azure. 


