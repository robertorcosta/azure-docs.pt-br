---
title: Gerenciar contas V2 do Azure Media Services | Microsoft Docs
description: Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Este artigo explica como gerenciar contas v2 do Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981942"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Gerenciar contas V2 do Azure Media Services

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Movimentação de uma conta de Serviços de Mídia entre assinaturas 

Se você precisar mover uma conta do Media Services para uma nova assinatura, você precisa primeiro mover todo o grupo de recursos que contém a conta de Serviços de Mídia para a nova assinatura. Você deve mover todos os recursos anexados: contas de armazenamento azure, perfis de CDN do Azure, etc. Para obter mais informações, consulte [Mover recursos para novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Como acontece com todos os recursos do Azure, as movimentações de grupos de recursos podem levar algum tempo para serem concluídas.

O Media Services v2 não suporta modelo de multi-locação. Se você precisar mover uma conta de Serviços de Mídia para uma assinatura em um novo inquilino, crie um novo aplicativo Azure Active Directory (Azure AD) no novo inquilino. Em seguida, mova sua conta para a assinatura no novo inquilino. Depois que o movimento do inquilino for concluído, você pode começar a usar um aplicativo Azure AD do novo inquilino para acessar a conta de Serviços de Mídia usando as APIs v2. 

> [!IMPORTANT]
> Você precisa redefinir as informações de autenticação do [Azure AD](media-services-portal-get-started-with-aad.md) para acessar a API v2 do Media Services.  
### <a name="considerations"></a>Considerações

* Crie backups de todos os dados em sua conta antes de migrar para uma assinatura diferente.
* Você precisa parar todos os pontos finais de streaming e recursos de transmissão ao vivo. Seus usuários não poderão acessar seu conteúdo durante a movimentação do grupo de recursos. 

> [!IMPORTANT]
> Não inicie o Ponto Final de Streaming até que o movimento seja concluído com sucesso.

### <a name="troubleshoot"></a>Solução de problemas 

Se uma conta de Serviços de Mídia ou uma conta azure de armazenamento associada for "desconectada" após a movimentação do grupo de recursos, tente girar as chaves da conta de armazenamento. Se a rotação das chaves da conta de armazenamento não resolver o status "desconectado" da conta Serviços de Mídia, faça um novo pedido de suporte no menu "Support + troublessolução" na conta Serviços de mídia.  
 
## <a name="next-steps"></a>Próximas etapas

[Criar uma conta](media-services-portal-create-account.md)
