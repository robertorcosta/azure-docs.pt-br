---
title: Converter um Emparelhamento direto herdado para o recurso do Azure usando o portal
titleSuffix: Azure
description: Converter um Emparelhamento direto herdado para o recurso do Azure usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775193"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal

Este artigo descreve como converter um exchange legado existente em recurso do Azure usando o portal.

Se preferir, você pode completar este guia usando o [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise [pré-requisitos](prerequisites.md) e [faça o passo a passo do Exchange](walkthrough-exchange-all.md) antes de iniciar a configuração.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Faça login no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Converter peering de exchange legado

Você pode converter conexões de peering legados usando o recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Inicie o recurso e configure configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configure conexões e envie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verificar peering exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre peering na Internet](faqs.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
