---
title: Como monitorar o Compartilhamento de Dados do Azure
description: Saiba como monitorar o status do convite, compartilhar assinaturas e histórico de instantâneos no Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490518"
---
# <a name="monitor-azure-data-share"></a>Monitorar o Azure Data Share  

Este artigo explica como você pode monitorar seus compartilhamentos de dados usando o Azure Data Share. Como provedor de dados, você é capaz de monitorar vários aspectos de suas relações de compartilhamento de dados. Detalhes como se seus consumidores de dados aceitaram seu convite para o compartilhamento de dados, bem como se eles criaram uma assinatura de compartilhamento e começaram a usar seus dados estão todos disponíveis para monitorar. 

Como um consumidor de dados, você pode monitorar os instantâneos que foram acionados em sua assinatura do Azure. 

## <a name="monitor-invitation-status"></a>Monitore o status do convite

Veja o status de seus convites de compartilhamento de dados navegando para Os Convites de > ações enviadas. 

![Status do convite](./media/invitation-status.png "Status do convite") 

Há três estados em que seu convite pode estar:

* Pendente - O destinatário do compartilhamento de dados ainda não aceitou o convite.
* Aceito - O destinatário do compartilhamento de dados aceitou o convite.
* Rejeitado - O destinatário do compartilhamento de dados rejeitou o convite.

> [!IMPORTANT]
> Se você excluir um convite depois de já ter sido aceito, ele não é equivalente a revogar o acesso. Se você quiser impedir que instantâneos futuros sejam copiados para sua conta de armazenamento de consumidores de dados, você deve revogar o acesso através da guia *Compartilhar assinaturas.* 

## <a name="monitor-share-subscriptions"></a>Monitore assinaturas de compartilhamento

Veja o status de suas assinaturas de ações navegando para As Ações enviadas -> Shares Assinaturas. Isso lhe dará detalhes sobre assinaturas ativas criadas por seus consumidores de dados após aceitar seu convite. Você pode parar futuras atualizações para o seu consumidor de dados selecionando a assinatura de compartilhamento e selecionando *Revogar*. 

## <a name="snapshot-history"></a>Histórico de instantâneos 

Na guia histórico, você é capaz de visualizar os instantâneos que foram copiados para o inquilino do seu consumidor de dados. Você é capaz de monitorar a frequência e a duração de cada intervalo de instantâneo. 

![Histórico de instantâneos](./media/sent-shares.png "Histórico de instantâneos") 

Você pode ver mais detalhes sobre cada instantâneo executado clicando na data de início da execução. 

Até 30 dias de histórico de instantâneos é exibido por padrão. Se você precisar ver mais de 30 dias de histórico, navegue até as configurações de Diagnóstico > monitoramento e selecione **Adicionar configuração de diagnóstico**. Você será obrigado a selecionar uma conta de armazenamento para armazenar esses logs. 

![Histórico de instantâneos](./media/diagnostic-settings.png "Configurações de Diagnóstico") 

## <a name="next-steps"></a>Próximas etapas 

Saiba mais sobre [a terminologia do Azure Data Share](terminology.md)