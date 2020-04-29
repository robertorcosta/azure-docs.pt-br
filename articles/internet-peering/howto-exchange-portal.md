---
title: Criar ou modificar um emparelhamento do Exchange usando o portal do Azure
titleSuffix: Azure
description: Criar ou modificar um emparelhamento do Exchange usando o portal do Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680929"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Criar ou modificar um emparelhamento do Exchange usando o portal do Azure

Este artigo descreve como criar um emparelhamento do Microsoft Exchange usando o portal do Azure. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [passo a passos de emparelhamento do Exchange](walkthrough-exchange-all.md) antes de começar a configuração.
* Se você já tiver emparelhamentos do Exchange com a Microsoft que não são convertidos em recursos do Azure, consulte [converter um emparelhamento do Exchange herdado em um recurso do Azure usando o portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Criar e provisionar um emparelhamento do Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um emparelhamento do Exchange

Você pode criar uma nova solicitação de emparelhamento usando o recurso de **emparelhamento** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Iniciar o recurso e definir as configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verificar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Desprovisionar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md)
* [Converter um emparelhamento direto herdado para um recurso do Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [perguntas frequentes sobre emparelhamento da Internet](faqs.md).
