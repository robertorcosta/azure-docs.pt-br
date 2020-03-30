---
title: Criar ou modificar um emparelhamento do Exchange usando o portal
titleSuffix: Azure
description: Criar ou modificar um emparelhamento do Exchange usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774569"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Criar ou modificar um emparelhamento do Exchange usando o portal

Este artigo descreve como criar um Microsoft Exchange espiando usando o portal. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode completar este guia usando o [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise [pré-requisitos](prerequisites.md) e [faça o passo a passo do Exchange](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Caso você já tenha peerings do Exchange com a Microsoft, que não são convertidos em recursos do Azure, consulte [Converter um recurso legacy Exchange para o Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Criar e prover um peering exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Faça login no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um peering de troca

Você pode criar uma nova solicitação de peering usando o recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Inicie o recurso e configure configurações básicas
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

* [Criar ou modificar um Emparelhamento direto usando o portal](howto-direct-portal.md)
* [Converter um Emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre peering na Internet](faqs.md)
