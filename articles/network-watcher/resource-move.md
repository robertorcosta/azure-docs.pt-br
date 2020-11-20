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
ms.openlocfilehash: 84764e73ec5b4ada8c204147def310326a3c7bdd
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948418"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Movendo recursos do observador de rede do Azure entre regiões

## <a name="moving-the-network-watcher-resource"></a>Movendo o recurso do observador de rede
O recurso do observador de rede representa o serviço de back-end para o observador de rede e é totalmente gerenciado pelo Azure. Os clientes não precisam gerenciá-lo. Não há suporte para a operação de movimentação neste recurso.

## <a name="moving-child-resources-of-network-watcher"></a>Movendo recursos filho do observador de rede
A movimentação de recursos entre regiões não tem suporte no momento para nenhum recurso filho do `*networkWatcher*` tipo de recurso.

## <a name="next-steps"></a>Próximas etapas
* Ler a [visão geral do observador de rede](./network-watcher-monitoring-overview.md)
* Consulte as [perguntas frequentes sobre o observador de rede](./frequently-asked-questions.md)