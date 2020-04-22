---
title: Criar ou modificar um peering exchange usando o portal Azure
titleSuffix: Azure
description: Criar ou modificar um peering exchange usando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680929"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Criar ou modificar um peering exchange usando o portal Azure

Este artigo descreve como criar um peering microsoft exchange usando o portal Azure. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode completar este guia usando [o PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise os [pré-requisitos](prerequisites.md) e o [passo a passo do Exchange](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Se você já tiver peerings do Exchange com a Microsoft que não foram convertidos em recursos do Azure, consulte [Converter um correspondente legado do Exchange para um recurso do Azure usando o portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Criar e prover um peering exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um peering de troca

Você pode criar uma nova solicitação de peering usando o recurso **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Inicie o recurso e configure configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configure conexões e envie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verifique um peering de troca
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um peering de troca
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Deprovisionum peering exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um peering direto usando o portal](howto-direct-portal.md)
* [Converta um legado de peering direto para um recurso do Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [perguntas frequentes sobre a Internet](faqs.md).
