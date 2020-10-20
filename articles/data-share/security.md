---
title: Visão geral de segurança para o Azure Data Share
description: Visão geral de segurança para o Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 5d815c27ecc7825f0bc1e6772654b094a799b63d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216548"
---
# <a name="security-overview-for-azure-data-share"></a>Visão geral de segurança para o Azure Data Share

Este artigo fornece uma visão geral de segurança do serviço de compartilhamento de dados do Azure.

## <a name="security-overview"></a>Visão geral da segurança

O Azure Data Share aproveita a segurança subjacente oferecida pelo Azure para proteger dados em repouso e em trânsito. Os dados são criptografados em repouso, quando isso é compatível com o armazenamento de dados subjacente. Os dados também são criptografados em trânsito. Os metadados sobre um compartilhamento de dados também são criptografados em repouso e em trânsito. 

Controles de acesso podem ser definidos no nível do recurso do Azure Data Share para garantir que sejam acessados por pessoas autorizadas. 

O compartilhamento de dados do Azure aproveita a identidade gerenciada (anteriormente conhecida como MSI) para acessar os armazenamentos de dados que estão sendo usados para o compartilhamento de dados. Não há troca de credenciais entre um provedor de dados e um consumidor de dados. Para obter mais informações sobre identidade gerenciada, consulte [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Para obter mais informações sobre funções e permissões necessárias para compartilhar dados, consulte [funções e requisitos](concepts-roles-permissions.md).

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).