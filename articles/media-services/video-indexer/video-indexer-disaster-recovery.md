---
title: Failover do indexador de vídeo e recuperação de desastres
titleSuffix: Azure Media Services
description: Saiba como fazer o failover em uma conta secundária do Indexador de vídeo se ocorrer uma falha ou desastre no datacenter regional.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499611"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Failover do indexador de vídeo e recuperação de desastres

O Azure Media Services Video Indexer não fornece failover instantâneo do serviço se houver uma paralisação ou falha no data center regional. Este artigo explica como configurar seu ambiente para um failover para garantir a disponibilidade ideal para aplicativos e minimizar o tempo de recuperação se ocorrer um desastre.

Recomendamos que você configure a recuperação de desastres de continuidade de negócios (BCDR) entre pares regionais para se beneficiar das políticas de isolamento e disponibilidade do Azure. Para obter mais informações, consulte [Regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure. Se você ainda não tem uma assinatura do Azure, inscreva-se na [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Failover para uma conta secundária

Para implementar o BCDR, você precisa ter duas contas do Video Indexer para lidar com a redundância.

1. Crie duas contas de indexador de vídeo conectadas ao Azure (consulte [Criar uma conta do Indexador de vídeo](connect-to-azure.md)). Crie uma conta para sua região primária e outra para a região azul emparelhada.
1. Se houver uma falha na sua região primária, mude para indexação usando a conta secundária.

> [!TIP]
> Você pode automatizar o BCDR configurando alertas de registro de atividades para notificações de saúde do serviço conforme de acordo [com Criar alertas de registro de atividade suscitado suscitados por notificações de serviço](../../service-health/alerts-activity-log-service-notifications.md).

Para obter informações sobre o uso de vários inquilinos, consulte [Gerenciar vários inquilinos](manage-multiple-tenants.md). Para implementar o BCDR, escolha uma dessas duas opções: [conta do Indexador de vídeo por inquilino](manage-multiple-tenants.md#video-indexer-account-per-tenant) ou assinatura do [Azure por inquilino](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Próximas etapas

[Gerenciar uma conta do Indexador de vídeo conectada ao Azure](manage-account-connected-to-azure.md).
