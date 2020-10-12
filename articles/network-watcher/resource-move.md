---
title: Mover recursos do observador de rede do Azure | Microsoft Docs
description: Mover recursos do observador de rede do Azure entre regiões
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388354"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Movendo recursos do observador de rede do Azure entre regiões

## <a name="moving-the-network-watcher-resource"></a>Movendo o recurso do observador de rede
O recurso do observador de rede representa o serviço de back-end para o observador de rede e é totalmente gerenciado pelo Azure. Os clientes não precisam gerenciá-lo. Não há suporte para a operação de movimentação neste recurso.

## <a name="moving-child-resources-of-network-watcher"></a>Movendo recursos filho do observador de rede
A movimentação de recursos entre regiões não tem suporte no momento para nenhum recurso filho do `*networkWatcher*` tipo de recurso.

## <a name="next-steps"></a>Próximas etapas
* Ler a [visão geral do observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)
* Consulte as [perguntas frequentes sobre o observador de rede](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions)
