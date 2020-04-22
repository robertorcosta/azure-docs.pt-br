---
title: Converta um peering direct legado para um recurso do Azure usando o portal Azure
titleSuffix: Azure
description: Converta um peering direct legado para um recurso do Azure usando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678839"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converta um peering direct legado para um recurso do Azure usando o portal Azure

Este artigo descreve como converter um legado existente o peering direto a um recurso do Azure usando o portal Azure.

Se preferir, você pode completar este guia usando [o PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise os [pré-requisitos](prerequisites.md) e o [passo a passo do peering direto](walkthrough-direct-all.md) antes de iniciar a configuração.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Converta um legado de peering direto para um recurso do Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Converta um legado de peering direto

Você pode converter conexões de peering legadousando o recurso **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Inicie o recurso e configure configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configure conexões e envie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifique o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [perguntas frequentes sobre a Internet](faqs.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um peering direto usando o portal](howto-direct-portal.md)
