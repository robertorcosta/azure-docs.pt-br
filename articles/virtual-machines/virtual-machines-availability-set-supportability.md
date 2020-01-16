---
title: Capacidade de suporte de adição de VMs do Azure a um conjunto de disponibilidade existente | Microsoft Docs
description: Este artigo fornece uma matriz de suporte sobre qual série de VMs você pode misturar no mesmo conjunto de disponibilidade
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028413"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Capacidade de suporte de adição de VMs do Azure a um conjunto de disponibilidade existente

Ocasionalmente, você poderá encontrar limitações ao adicionar novas VMs (máquinas virtuais) a um conjunto de disponibilidade existente. O gráfico a seguir fornece detalhes sobre quais séries de VM podem ser combinadas no mesmo conjunto de disponibilidade.

Esta é a matriz de capacidade de suporte para combinação de diferentes tipos de VMs:

Série e conjunto de disponibilidade|Segunda VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primeira VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todas as outras séries não podem estar no mesmo conjunto de disponibilidade porque exigem um hardware específico.

O tamanho da VM A8/A9 não pode ser misturado devido ao requisito na rede de back-end RDMA dedicada.
