---
title: Revogar uma assinatura de compartilhamento no compartilhamento de dados do Azure
description: Saiba como revogar uma assinatura de compartilhamento de um destinatário usando o compartilhamento de dados do Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: c1c049d467cdf07962719f0dc257acbf47631aaf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119677"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Como revogar a assinatura de compartilhamento de um consumidor no compartilhamento de dados do Azure

Este artigo explica como revogar uma assinatura de compartilhamento de um ou mais dos seus consumidores usando o compartilhamento de dados do Azure. Isso impede que um consumidor acione mais instantâneos. Se o consumidor ainda não tiver disparado um instantâneo, ele nunca receberá os dados depois que a assinatura de compartilhamento for revogada. Se eles tiverem disparado um instantâneo anteriormente, os dados mais recentes que eles permanecerão em sua conta.

## <a name="navigate-to-a-sent-data-share"></a>Navegar até um compartilhamento de dados enviado

No compartilhamento de dados do Azure, navegue até o compartilhamento enviado e selecione a guia **compartilhar assinaturas** .

![Revogar assinatura de compartilhamento](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Marque as caixas ao lado dos destinatários cujas assinaturas de compartilhamento você gostaria de excluir e, em seguida, clique em **revogar**. O consumidor não receberá mais atualizações de seus dados.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [monitorar seus compartilhamentos de dados](how-to-monitor.md).