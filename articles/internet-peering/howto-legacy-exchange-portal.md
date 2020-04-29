---
title: Converter um emparelhamento do Exchange herdado em um recurso do Azure usando o portal do Azure
titleSuffix: Azure
description: Converter um emparelhamento do Exchange herdado em um recurso do Azure usando o portal do Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678572"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converter um emparelhamento do Exchange herdado em um recurso do Azure usando o portal do Azure

Este artigo descreve como converter um emparelhamento do Exchange herdado existente em um recurso do Azure usando o portal do Azure.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [passo a passos de emparelhamento do Exchange](walkthrough-exchange-all.md) antes de começar a configuração.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Converter um emparelhamento do Exchange herdado para um recurso do Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Converter o emparelhamento do Exchange herdado

Você pode converter conexões de emparelhamento herdadas usando o recurso de **emparelhamento** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Iniciar o recurso e definir as configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verificar o emparelhamento do Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [perguntas frequentes sobre emparelhamento da Internet](faqs.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
