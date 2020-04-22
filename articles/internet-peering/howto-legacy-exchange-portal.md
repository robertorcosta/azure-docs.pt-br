---
title: Converta um peering exchange legado para um recurso do Azure usando o portal Azure
titleSuffix: Azure
description: Converta um peering exchange legado para um recurso do Azure usando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678572"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converta um peering exchange legado para um recurso do Azure usando o portal Azure

Este artigo descreve como converter um exchange legado existente em um recurso do Azure usando o portal Azure.

Se preferir, você pode completar este guia usando [o PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise os [pré-requisitos](prerequisites.md) e o [passo a passo do Exchange](walkthrough-exchange-all.md) antes de iniciar a configuração.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Converta um peering exchange legado para um recurso do Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Converter peering de exchange legado

Você pode converter conexões de peering legadousando o recurso **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Inicie o recurso e configure configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configure conexões e envie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verificar peering exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [perguntas frequentes sobre a Internet](faqs.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um peering do Exchange usando o portal](howto-exchange-portal.md)
