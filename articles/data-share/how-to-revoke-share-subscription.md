---
title: Revogar uma assinatura de ações no Azure Data Share
description: Saiba como revogar uma assinatura de compartilhamento de um destinatário usando o Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476382"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Como revogar a assinatura de ações de um consumidor no Azure Data Share

Este artigo explica como revogar uma assinatura de compartilhamento de um ou mais de seus consumidores usando o Azure Data Share. Isso impede que um consumidor acione mais instantâneos. Se o consumidor ainda não tiver acionado um instantâneo, ele nunca receberá os dados uma vez que a assinatura de compartilhamento seja revogada. Se eles já acionaram um instantâneo, os dados mais recentes que eles têm permanecerão em sua conta.

## <a name="navigate-to-a-sent-data-share"></a>Navegue para um compartilhamento de dados enviado

No Azure Data Share, navegue até o seu compartilhamento enviado e selecione a guia **Compartilhar Assinaturas.**

![Revogar assinatura de compartilhamento](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Verifique as caixas ao lado dos destinatários cujas assinaturas compartilhadas você gostaria de excluir e clique em **Revogar**. O consumidor não receberá mais atualizações de seus dados.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [monitorar seus compartilhamentos de dados.](how-to-monitor.md)