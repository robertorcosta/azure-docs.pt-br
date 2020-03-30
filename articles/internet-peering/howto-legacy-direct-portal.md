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
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775050"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Converter um Emparelhamento direto herdado para o recurso do Azure usando o portal

Este artigo descreve como converter um legado existente O peering direto ao recurso do Azure usando o portal.

Se preferir, você pode completar este guia usando o [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise [pré-requisitos](prerequisites.md) e [passe-passo do peering direto](walkthrough-direct-all.md) antes de iniciar a configuração.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Converter o peering direto legado para o recurso do Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Faça login no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Converter o legado de peering direto

Você pode converter conexões de peering legados usando o recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Inicie o recurso e configure configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configure conexões e envie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifique o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre peering na Internet](faqs.md)

## <a name="next-steps"></a>Próximas etapas

* [Crie ou modifique um peering direto usando o portal](howto-direct-portal.md).
