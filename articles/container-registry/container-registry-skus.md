---
title: Recursos e camadas de serviço do registro
description: Saiba mais sobre os recursos e limites nas camadas de serviço Básico, Standard e Premium (SKUs) do Registro de Contêiner do Azure.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: e2a5ad52775e9000aa0beb0a926d809da1c5a0e0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048467"
---
# <a name="azure-container-registry-service-tiers"></a>Criar camadas de serviço do Registro de Contêiner do Azure

O Registro de Contêiner do Azure está disponível em várias camadas de serviço (também conhecidas como SKUs). Essas camadas fornecem preços previsíveis e várias opções de alinhamento aos padrões de capacidade e uso de seu registro de Docker privado no Azure.

| Camada | Descrição |
| --- | ----------- |
| **Basic** | Um ponto de entrada de otimização de custo para desenvolvedores aprendendo sobre o Registro de Contêiner do Azure. Os registros Básicos têm os mesmos recursos de programação que os Standard e Premium (como a [integração de autenticação do Azure Active Directory](container-registry-authentication.md#individual-login-with-azure-ad), [exclusão de imagens][container-registry-delete]e [webhooks][container-registry-webhook]). No entanto, o armazenamento incluído e a taxa de transferência de imagem são mais apropriados para cenários de uso mais baixos. |
| **Standard** | Os registros Standard oferecem os mesmos recursos do Básico, com maior armazenamento incluído e taxa de transferência de imagem. Registros Standard devem atender às necessidades da maioria dos cenários de produção. |
| **Premium** | Os registros Premium fornecem a maior quantidade de armazenamento incluído e operações simultâneas, permitindo cenários de alto volume. Além de uma taxa de transferência de imagens mais alta, o Premium adiciona recursos como a [replicação geográfica][container-registry-geo-replication] para gerenciar um único registro em várias regiões, [confiança de conteúdo](container-registry-content-trust.md) para a assinatura de marca de imagem e [link privado com pontos de extremidade privados](container-registry-private-link.md) para restringir o acesso ao registro. |

Todas as camadas Básica, Standard e Premium fornecem os mesmos recursos de programação. Todos eles também se beneficiam do [armazenamento de imagem][container-registry-storage] gerenciados inteiramente pelo Azure. A escolha de uma camada de nível mais alto oferece mais desempenho e escala. Com várias camadas de serviço, pode começar com o Básico e depois mudar para o Padrão e o Premium à medida que crescer o uso do registro.

## <a name="service-tier-features-and-limits"></a>Recursos e limites de camada de serviço

A tabela a seguir detalha os recursos e limites de registro das camadas de serviço Basic, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Alterando camadas

Você pode alterar a camada de serviço do registro com a CLI do Azure ou no portal do Azure. Você pode mudar livremente de camada, contanto que ela tenha a capacidade de armazenamento máxima necessária. 

### <a name="azure-cli"></a>CLI do Azure

Para mudar de camada de serviço na CLI do Azure, use o comando [az acr update][az-acr-update]. Por exemplo, para alternar para o Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **Visão geral** do registro de contêiner no portal do Azure, selecione **Atualizar** e selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure][update-registry-sku]

## <a name="pricing"></a>Preços

Para obter informações sobre preços em cada uma das camadas de serviço do registro de contêiner do Azure, consulte os [preços do Registro de Contêiner][container-registry-pricing].

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