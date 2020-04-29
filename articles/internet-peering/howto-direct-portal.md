---
title: Criar ou modificar um emparelhamento direto usando o portal do Azure
titleSuffix: Azure
description: Criar ou modificar um emparelhamento direto usando o portal do Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681042"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Criar ou modificar um emparelhamento direto usando o portal do Azure

Este artigo descreve como criar um emparelhamento direto da Microsoft usando o portal do Azure. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [passo a passos de emparelhamento direto](walkthrough-direct-all.md) antes de começar a configuração.
* Se você já tiver conexões diretas de emparelhamento com a Microsoft que não são convertidas em recursos do Azure, consulte [converter um emparelhamento direto herdado para um recurso do Azure usando o portal](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Criar e provisionar um emparelhamento direto

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um emparelhamento direto

Você pode criar uma nova solicitação de emparelhamento usando o recurso de **emparelhamento** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Iniciar recurso e definir configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar emparelhamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um emparelhamento direto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionar um emparelhamento direto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para um recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [perguntas frequentes sobre emparelhamento da Internet](faqs.md).
