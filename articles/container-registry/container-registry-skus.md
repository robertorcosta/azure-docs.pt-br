---
title: Service tiers and SKUs
description: Learn about the features and limits in the Basic, Standard, and Premium service tiers (SKUs) of Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456276"
---
# <a name="azure-container-registry-skus"></a>SKUs do Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções de alinhamento aos padrões de capacidade e uso de seu registro de Docker privado no Azure.

| SKU | Descrição |
| --- | ----------- |
| **Básico** | Um ponto de entrada de otimização de custo para desenvolvedores aprendendo sobre o Registro de Contêiner do Azure. Basic registries have the same programmatic capabilities as Standard and Premium (such as Azure Active Directory [authentication integration](container-registry-authentication.md#individual-login-with-azure-ad), [image deletion][container-registry-delete], and [webhooks][container-registry-webhook]). No entanto, o armazenamento incluído e a taxa de transferência de imagem são mais apropriados para cenários de uso mais baixos. |
| **Standard** | Os registros Standard oferecem os mesmos recursos do Básico, com maior armazenamento incluído e taxa de transferência de imagem. Registros Standard devem atender às necessidades da maioria dos cenários de produção. |
| **Premium** | Os registros Premium fornecem a maior quantidade de armazenamento incluído e operações simultâneas, permitindo cenários de alto volume. In addition to higher image throughput, Premium adds features such as [geo-replication][container-registry-geo-replication] for managing a single registry across multiple regions, [content trust](container-registry-content-trust.md) for image tag signing, [firewalls and virtual networks (preview)](container-registry-vnet.md) to restrict access to the registry. |

The Basic, Standard, and Premium SKUs all provide the same programmatic capabilities. They also all benefit from [image storage][container-registry-storage] managed entirely by Azure. A escolha de uma SKU de nível mais alto oferece mais desempenho e escala. Com várias camadas de serviço, pode começar com o Básico e depois mudar para o Padrão e o Premium à medida que crescer o uso do registro.

## <a name="sku-features-and-limits"></a>SKU features and limits

A tabela a seguir fornece detalhes sobre os recursos e os limites das camadas de serviço Básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Alterando SKUs

Você pode alterar a SKU do registro com o CLI do Azure ou no portal do Azure. You can move freely between SKUs as long as the SKU you're switching to has the required maximum storage capacity. 

### <a name="azure-cli"></a>Azure CLI

To move between SKUs in the Azure CLI, use the [az acr update][az-acr-update] command. Por exemplo, para alternar para o Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **Visão geral** do registro de contêiner no portal do Azure, selecione **Atualizar** e selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure][update-registry-sku]

## <a name="pricing"></a>Preços

Para saber mais sobre os preços de cada SKU de Registro de Contêiner do Azure, confira [Preços do registro de contêiner][container-registry-pricing].

Para obter detalhes sobre os preços das transferências de dados, confira [Detalhes de Preços de Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Próximos passos

**Roteiro do Registro de Contêiner do Azure**

Visite o [Roteiro do ACR][acr-roadmap] no GitHub para encontrar informações sobre os próximos recursos do serviço.

**UserVoice do Registro de Contêiner do Azure**

Envie e vote em sugestões de novos recursos no [UserVoice do ACR][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
