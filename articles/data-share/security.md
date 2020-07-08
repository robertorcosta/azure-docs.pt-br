---
title: Visão geral de segurança para o compartilhamento de dados do Azure
description: Visão geral de segurança para o compartilhamento de dados do Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: e299f63777847c557fc13afb4d36d01c12b3b52a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84637045"
---
# <a name="security-overview-for-azure-data-share"></a>Visão geral de segurança para o compartilhamento de dados do Azure

Este artigo fornece uma visão geral de segurança do serviço de compartilhamento de dados do Azure.

## <a name="security-overview"></a>Visão geral da segurança

O Azure Data Share aproveita a segurança subjacente oferecida pelo Azure para proteger dados em repouso e em trânsito. Os dados são criptografados em repouso, quando isso é compatível com o armazenamento de dados subjacente. Os dados também são criptografados em trânsito. Os metadados sobre um compartilhamento de dados também são criptografados em repouso e em trânsito. 

Controles de acesso podem ser definidos no nível do recurso do Azure Data Share para garantir que sejam acessados por pessoas autorizadas. 

O compartilhamento de dados do Azure aproveita a identidade gerenciada (anteriormente conhecida como MSI) para acessar os armazenamentos de dados que estão sendo usados para o compartilhamento de dados. Não há troca de credenciais entre um provedor de dados e um consumidor de dados. Para obter mais informações sobre identidade gerenciada, consulte [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). Para obter mais informações sobre funções e permissões necessárias para compartilhar dados, consulte [funções e requisitos](concepts-roles-permissions.md).

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).




