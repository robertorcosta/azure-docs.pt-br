---
title: Armazenamento de imagens de contêiner
description: Detalhes sobre como as imagens de contêiner e outros artefatos são armazenados no registro de contêiner do Azure, incluindo segurança, redundância e capacidade.
ms.topic: article
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 4bdffd111273e00b796e45f4e09bfac9ba6713e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036003"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagens de contêiner no Registro de Contêiner do Azure

Cada registro de contêiner do Azure [Básico, Standard e Premium](container-registry-skus.md) beneficia-se de recursos avançados de armazenamento do Azure, como criptografia em repouso para segurança de dados de imagem e redundância geográfica para proteção de dados de imagem. As seções a seguir descrevem os recursos e os limites do armazenamento de imagens no ACR (Registro de Contêiner do Azure).

## <a name="encryption-at-rest"></a>Criptografia em repouso

Todas as imagens de contêiner e outros artefatos em seu registro são criptografados em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e descriptografa-a dinamicamente quando você ou seus aplicativos e serviços obtêm a imagem. Opcionalmente, aplique uma camada de criptografia extra a uma [chave gerenciada pelo cliente](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

Para registros de contêiner implantados na maioria das regiões, o Azure usa um esquema de armazenamento com redundância geográfica para ajudar a proteger contra perda de suas imagens de contêiner e outros artefatos. O registro de contêiner do Azure replica automaticamente suas imagens de contêiner para vários data centers distantes geograficamente, evitando a perda se ocorrer uma falha de armazenamento regional.

> [!IMPORTANT]
> * Se ocorrer uma falha de armazenamento regional, os dados do registro só poderão ser recuperados entrando em contato com o suporte do Azure. 
> * Devido aos requisitos de residência de dados no sul do Brasil e sudeste asiático, os dados do registro de contêiner do Azure nessas regiões são armazenados [somente em geografia local](https://azure.microsoft.com/global-infrastructure/geographies/). Para o Sudeste Asiático, todos os dados são armazenados em Cingapura. Para o sul do Brasil, todos os dados são armazenados no Brasil. Quando a região é perdida devido a um desastre significativo, a Microsoft não será capaz de recuperar os dados do registro de contêiner do Azure.

## <a name="geo-replication"></a>Replicação geográfica

Para cenários que exigem ainda mais garantia de alta disponibilidade, utilize o recurso de [replicação geográfica](container-registry-geo-replication.md) dos registros Premium. A replicação geográfica ajuda a proteger contra a perda de acesso ao registro no caso de uma falha regional *total* e não apenas uma falha de armazenamento. A replicação geográfica também oferece outros benefícios, como armazenamento de imagens de proximidade de rede para efetuar pushes e pulls mais rápido em cenários de implantação ou desenvolvimento distribuído.

## <a name="zone-redundancy"></a>Redundância de zona

Para criar um registro de contêiner do Azure resiliente e de alta disponibilidade, opcionalmente, habilite a [redundância de zona](zone-redundancy.md) em selecionar regiões do Azure. Um recurso da camada de serviço Premium, a redundância de zona usa [zonas de disponibilidade](../availability-zones/az-overview.md) do Azure para replicar o registro para um mínimo de três zonas separadas em cada região habilitada. Combine a replicação geográfica e a redundância de zona para aprimorar a confiabilidade e o desempenho de um registro. 

## <a name="scalable-storage"></a>Armazenamento escalonável

O registro de contêiner do Azure permite que você crie quantos repositórios, imagens, camadas ou marcas forem necessárias, até o limite de [armazenamento do registro](container-registry-skus.md#service-tier-features-and-limits). 

Números altos de repositórios e marcas podem afetar o desempenho do registro. Exclua periodicamente repositórios, marcas e imagens não utilizados como parte da rotina de manutenção do registro e, opcionalmente, defina uma [política de retenção](container-registry-retention-policy.md) para manifestos não marcados. Os recursos de registro excluídos, como repositórios, imagens e marcas, *não podem* ser recuperados após a exclusão. Para obter mais informações sobre a exclusão de recursos de registro, consulte [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Custo de armazenamento

Para obter detalhes completos sobre preços, consulte [Preços de Registro de Contêiner do Azure][pricing].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre registros de contêiner Básico, Standard e Premium, consulte [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
