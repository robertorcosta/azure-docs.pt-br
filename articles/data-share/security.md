---
title: Visão geral de segurança para o Azure Data Share
description: Visão geral de segurança para o Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578086"
---
# <a name="security-overview-for-azure-data-share"></a>Visão geral de segurança para o Azure Data Share

Este artigo fornece uma visão geral de segurança do serviço de compartilhamento de dados do Azure.

## <a name="security-overview"></a>Visão geral da segurança

O Azure Data Share aproveita a segurança subjacente oferecida pelo Azure para proteger dados em repouso e em trânsito. Os dados são criptografados em repouso, quando isso é compatível com o armazenamento de dados subjacente. Os dados também são criptografados em trânsito usando o TLS 1,2. Os metadados sobre um compartilhamento de dados também são criptografados em repouso e em trânsito. O compartilhamento de dados do Azure não armazena o conteúdo dos dados do cliente que estão sendo compartilhados.

O compartilhamento de dados do Azure aproveita a identidade gerenciada (anteriormente conhecida como MSI) para acessar os armazenamentos de dados que estão sendo usados para o compartilhamento de dados. Não há troca de credenciais entre um provedor de dados e um consumidor de dados. Para obter mais informações sobre identidade gerenciada, consulte [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Para obter mais informações sobre funções e permissões necessárias para compartilhar dados, consulte [funções e requisitos](concepts-roles-permissions.md).

Os controles de acesso ao compartilhamento de dados do Azure podem ser definidos no nível de recurso de compartilhamento de dados para garantir que eles sejam acessados por aqueles autorizados. 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Compartilhar dados de ou para armazenamentos de dados com o firewall habilitado
Para compartilhar dados de ou para contas de armazenamento com o firewall ativado, você precisa habilitar a **permissão de serviços confiáveis da Microsoft** em sua conta de armazenamento. Consulte [configurar redes virtuais e firewalls de armazenamento do Azure](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) para obter detalhes.


## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).
