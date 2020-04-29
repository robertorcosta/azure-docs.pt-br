---
title: Converter um emparelhamento direto herdado para um recurso do Azure usando o portal do Azure
titleSuffix: Azure
description: Converter um emparelhamento direto herdado para um recurso do Azure usando o portal do Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678839"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converter um emparelhamento direto herdado para um recurso do Azure usando o portal do Azure

Este artigo descreve como converter um emparelhamento direto herdado existente para um recurso do Azure usando o portal do Azure.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [passo a passos de emparelhamento direto](walkthrough-direct-all.md) antes de começar a configuração.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Converter um emparelhamento direto herdado para um recurso do Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Converter um emparelhamento direto herdado

Você pode converter conexões de emparelhamento herdadas usando o recurso de **emparelhamento** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Iniciar o recurso e definir as configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar emparelhamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [perguntas frequentes sobre emparelhamento da Internet](faqs.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md)
