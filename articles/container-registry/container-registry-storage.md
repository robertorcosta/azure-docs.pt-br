---
title: Armazenamento de imagens de contêiner
description: Detalhes sobre como as imagens de contêiner do Docker são armazenadas no Registro de Contêiner do Azure, incluindo segurança, redundância e capacidade.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: b738556e5a4f764cd47c72d964ee188d1344b336
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683408"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagens de contêiner no Registro de Contêiner do Azure

Cada registro de contêiner do Azure [Básico, Standard e Premium](container-registry-skus.md) beneficia-se de recursos avançados de armazenamento do Azure, como criptografia em repouso para segurança de dados de imagem e redundância geográfica para proteção de dados de imagem. As seções a seguir descrevem os recursos e os limites do armazenamento de imagens no ACR (Registro de Contêiner do Azure).

## <a name="encryption-at-rest"></a>Criptografia em repouso

Todas as imagens de contêiner no registro são criptografadas em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e descriptografa-a dinamicamente quando você ou seus aplicativos e serviços obtêm a imagem.

## <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

O Azure usa um esquema de armazenamento com redundância geográfica para proteger contra perda de imagens de contêiner. O Registro de Contêiner do Azure replica automaticamente as imagens de contêiner para vários datacenters geograficamente distantes, evitando a perda no caso de uma falha de armazenamento regional.

## <a name="geo-replication"></a>Replicação geográfica

Para cenários que exigem ainda mais garantia de alta disponibilidade, utilize o recurso de [replicação geográfica](container-registry-geo-replication.md) dos registros Premium. A replicação geográfica ajuda a proteger contra a perda de acesso ao registro no caso de uma falha regional *total* e não apenas uma falha de armazenamento. A replicação geográfica também oferece outros benefícios, como armazenamento de imagens de proximidade de rede para efetuar pushes e pulls mais rápido em cenários de implantação ou desenvolvimento distribuído.

## <a name="image-limits"></a>Limites de imagem

A tabela a seguir descreve os limites de armazenamento e imagem de contêiner em vigor para os registros de contêiner do Azure.

| Recurso | Limite |
| -------- | :---- |
| Repositórios | Sem limite |
| Imagens | Sem limite |
| Camadas | Sem limite |
| Marcas | Sem limite|
| Armazenamento | 5 TB |

Números muito altos de repositórios e marcas podem afetar o desempenho do registro. Exclua periodicamente repositórios, marcas e imagens não utilizados como parte da rotina de manutenção de registro. Recursos de registro excluídos, como repositórios, imagens e marcas *não poderão* ser recuperados após a exclusão. Para obter mais informações sobre a exclusão de recursos de registro, consulte [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Custo de armazenamento

Para obter detalhes completos sobre preços, consulte [Preços de Registro de Contêiner do Azure][pricing].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre registros de contêiner Básico, Standard e Premium, consulte [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
