---
title: Níveis de serviço e SKUs
description: Conheça os recursos e limites nos níveis de serviço Básico, Padrão e Premium (SKUs) do Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456276"
---
# <a name="azure-container-registry-skus"></a>SKUs do Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções de alinhamento aos padrões de capacidade e uso de seu registro de Docker privado no Azure.

| SKU | Descrição |
| --- | ----------- |
| **Basic** | Um ponto de entrada de otimização de custo para desenvolvedores aprendendo sobre o Registro de Contêiner do Azure. Os registros básicos têm os mesmos recursos programáticos que o Standard e o Premium (como integração de [autenticação](container-registry-authentication.md#individual-login-with-azure-ad)do Azure Active Directory, [exclusão de imagens][container-registry-delete]e [webhooks).][container-registry-webhook] No entanto, o armazenamento incluído e a taxa de transferência de imagem são mais apropriados para cenários de uso mais baixos. |
| **Standard** | Os registros Standard oferecem os mesmos recursos do Básico, com maior armazenamento incluído e taxa de transferência de imagem. Registros Standard devem atender às necessidades da maioria dos cenários de produção. |
| **Premium** | Os registros Premium fornecem a maior quantidade de armazenamento incluído e operações simultâneas, permitindo cenários de alto volume. Além do maior throughput de imagem, o Premium adiciona recursos como [geo-replicação][container-registry-geo-replication] para o gerenciamento de um único registro em várias regiões, [confiança de conteúdo](container-registry-content-trust.md) para assinatura de tag de imagem, [firewalls e redes virtuais (visualização)](container-registry-vnet.md) para restringir o acesso ao registro. |

As SKUs Básicas, Padrão e Premium fornecem os mesmos recursos programáticos. Todos eles também se beneficiam do armazenamento de [imagens][container-registry-storage] gerenciado inteiramente pelo Azure. A escolha de uma SKU de nível mais alto oferece mais desempenho e escala. Com várias camadas de serviço, pode começar com o Básico e depois mudar para o Padrão e o Premium à medida que crescer o uso do registro.

## <a name="sku-features-and-limits"></a>Recursos e limites do SKU

A tabela a seguir fornece detalhes sobre os recursos e os limites das camadas de serviço Básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Alterando SKUs

Você pode alterar a SKU do registro com o CLI do Azure ou no portal do Azure. Você pode mover-se livremente entre SKUs, desde que o SKU para o seu lado tenha a capacidade máxima de armazenamento necessária. 

### <a name="azure-cli"></a>CLI do Azure

Para mover entre os SKUs do CLI do Azure, use o comando [az acr update][az-acr-update]. Por exemplo, para alternar para o Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **Visão geral** do registro de contêiner no portal do Azure, selecione **Atualizar** e selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure][update-registry-sku]

## <a name="pricing"></a>Preços

Para saber mais sobre os preços de cada SKU de Registro de Contêiner do Azure, confira [Preços do registro de contêiner][container-registry-pricing].

Para obter detalhes sobre os preços das transferências de dados, confira [Detalhes de Preços de Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Próximas etapas

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
