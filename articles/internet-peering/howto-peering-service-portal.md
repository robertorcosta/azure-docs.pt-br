---
title: Habilite o Serviço de Peering do Azure em um peering direto usando o portal Azure
titleSuffix: Azure
description: Habilite o Serviço de Peering do Azure em um peering direto usando o portal Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687033"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Habilite o Serviço de Peering do Azure em um peering direto usando o portal Azure

Este artigo descreve como ativar o Azure [Peering Service](overview-peering-service.md) em um peering direto usando o portal Azure.

Se preferir, você pode completar este guia usando [o PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise os [pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um peering direto em sua assinatura para o qual você deseja ativar o Peering Service. Se você não tiver um, converta um peering direct legado ou crie um novo peering direto:
    * Para converter um peering direct legado, siga as instruções em [Converter um correspondente legado direto a um recurso do Azure usando o portal](howto-legacy-direct-portal.md).
    * Para criar um novo peering direto, siga as instruções em [Criar ou modificar um peering direto usando o portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Habilitar o Serviço de Emparelhamento em um Emparelhamento direto

### <a name="view-direct-peering"></a><a name= get></a>Ver peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Habilite o peering direto para o serviço de peering

Depois de abrir um peering direto na etapa anterior, habilite-o para serviço de peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modifique uma conexão de peering direto

Para modificar as configurações de conexão, consulte a seção "Modificar um peering direto" em [Criar ou modificar um peering direto usando o portal](howto-direct-portal.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o peering do Exchange usando o portal](howto-exchange-portal.md)
* [Converta um peering exchange legado para um recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para perguntas frequentes, consulte o [FAQ do Serviço de Peering](service-faqs.md).