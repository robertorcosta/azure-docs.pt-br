---
title: Como migrar o Azure API Management entre regiões
description: Aprenda a migrar uma instância de Gerenciamento de API de uma região para outra.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073474"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Como migrar o Azure API Management entre regiões
Para migrar instâncias de gerenciamento de API de uma região do Azure para outra, você pode usar o recurso [de backup e restauração.](api-management-howto-disaster-recovery-backup-restore.md) Você deve escolher o mesmo nível de preços de gerenciamento de API nas regiões de origem e destino. 

> [!NOTE]
> O backup e a restauração não funcionarão enquanto migram entre diferentes tipos de nuvem. Para isso, você precisará exportar o recurso [como modelo](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates). Em seguida, adapte o modelo exportado para a região azure alvo e recrie o recurso. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Opção 1: Use um nome de instância de gerenciamento de API diferente

1. Na região-alvo, crie uma nova instância de gerenciamento de API com o mesmo nível de preço da instância de gerenciamento de API de origem. A nova instância deve ter um nome diferente. 
1. Faça backup da instância de gerenciamento de API existente em uma conta de armazenamento.
1. Restaure o backup criado na Etapa 2 para a nova instância de gerenciamento de API criada na nova região na Etapa 1.
1. Se você tiver um domínio personalizado apontando para a instância de gerenciamento de API da região de origem, atualize o CNAME de domínio personalizado para apontar para a nova instância de gerenciamento de API. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Opção 2: Use o mesmo nome de instância de gerenciamento de API

> [!NOTE]
> Essa opção resultará em tempo de inatividade durante a migração.

1. Fazer backup da instância de gerenciamento de API na região de origem para uma conta de armazenamento.
1. Exclua o gerenciamento de API na região de origem. 
1. Crie uma nova instância de gerenciamento de API na região-alvo com o mesmo nome da região de origem.
1. Restaure o backup criado na Etapa 1 para a nova instância de gerenciamento de API na região de destino.  


## <a name="next-steps"></a><a name="next-steps"> </a>Próximas etapas
* Para obter mais informações sobre o recurso de backup e restauração, veja [como implementar a recuperação de desastres](api-management-howto-disaster-recovery-backup-restore.md).
* Para obter informações sobre os recursos migratórios do Azure, consulte [a orientação de migração entre as regiões do Azure](https://github.com/Azure/Azure-Migration-Guidance).