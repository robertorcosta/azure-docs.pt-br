---
title: Failover de Video Indexer e recuperação de desastre
titleSuffix: Azure Media Services
description: Saiba como fazer failover para uma conta de Video Indexer secundária se ocorrer uma falha de datacenter regional ou um desastre.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499611"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Failover de Video Indexer e recuperação de desastre

Os serviços de mídia do Azure Video Indexer não fornecem failover instantâneo do serviço se houver uma interrupção ou falha de datacenter regional. Este artigo explica como configurar seu ambiente para um failover a fim de garantir a disponibilidade ideal para aplicativos e o tempo de recuperação minimizado se ocorrer um desastre.

Recomendamos que você configure a BCDR (recuperação de desastre de continuidade de negócios) em pares regionais para se beneficiar das políticas de isolamento e disponibilidade do Azure. Para obter mais informações, consulte [Regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, Inscreva-se para a [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Failover para uma conta secundária

Para implementar o BCDR, você precisa ter duas contas de Video Indexer para lidar com a redundância.

1. Crie duas contas de Video Indexer conectadas ao Azure (consulte [criar uma conta de video indexer](connect-to-azure.md)). Crie uma conta para sua região primária e a outra para a região emparelhada do Azure.
1. Se houver uma falha em sua região primária, alterne para indexação usando a conta secundária.

> [!TIP]
> Você pode automatizar o BCDR Configurando alertas do log de atividades para notificações de integridade do serviço de acordo com a [criação de alertas do log de atividades em notificações de serviço](../../service-health/alerts-activity-log-service-notifications.md).

Para obter informações sobre como usar vários locatários, consulte [gerenciar vários locatários](manage-multiple-tenants.md). Para implementar o BCDR, escolha uma destas duas opções: [Video indexer conta por locatário](manage-multiple-tenants.md#video-indexer-account-per-tenant) ou [assinatura do Azure por locatário](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Próximas etapas

[Gerenciar uma conta de video indexer conectada ao Azure](manage-account-connected-to-azure.md).
