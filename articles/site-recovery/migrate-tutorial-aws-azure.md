---
title: Migrar VMs do AWS para o serviço Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar VMs do Windows em execução no AWS (Amazon Web Services) para o Azure usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281286"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrar das VMs do AWS (Amazon Web Services) para o Azure

Este artigo descreve as opções para migrar instâncias de AWS (Amazon Web Services) para o Azure.

## <a name="migrate-with-azure-migrate"></a>Migrar com as Migrações para Azure

Recomendamos que você migre instâncias da AWS para o Azure usando o serviço [Migrações para Azure](../migrate/migrate-services-overview.md). As Migrações para Azure fornecem um hub centralizado para a avaliação e a migração de computadores locais para o Azure usando as Migrações para Azure, outros serviços do Azure e ferramentas de terceiros.

[Saiba como](../migrate/tutorial-migrate-aws-virtual-machines.md) migrar instâncias da AWS com as Migrações para Azure. 


## <a name="migrate-with-site-recovery"></a>Migrar com o Site Recovery

O Site Recovery deve ser usado somente para recuperação de desastre, e não para migração.

Se já estiver usando o Azure Site Recovery e quiser dar continuidade a seu uso para a migração da AWS, siga as mesmas etapas que você usaria para configurar a [recuperação de desastre de computadores físicos](physical-azure-disaster-recovery.md).


> [!NOTE]
> Quando você executa um failover para recuperação de desastre, a última etapa é confirmar o failover. Quando você migra instâncias da AWS, a opção **Confirmar** não é relevante. Em vez disso, selecione a opção **Concluir Migração**. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Veja perguntas comuns](../migrate/resources-faq.md) sobre as Migrações para Azure.
