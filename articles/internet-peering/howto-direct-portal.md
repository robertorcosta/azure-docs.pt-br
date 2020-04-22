---
title: Crie ou modifique um peering direto usando o portal Azure
titleSuffix: Azure
description: Crie ou modifique um peering direto usando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681042"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Crie ou modifique um peering direto usando o portal Azure

Este artigo descreve como criar um peering microsoft direct usando o portal Azure. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode completar este guia usando o Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise os [pré-requisitos](prerequisites.md) e o [passo a passo do peering direto](walkthrough-direct-all.md) antes de iniciar a configuração.
* Se você já tiver conexões de peering diretas com a Microsoft que não foram convertidas em recursos do Azure, consulte [Converter um correspondente legado de peering direto a um recurso do Azure usando o portal](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Criar e prover um peering direto

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Crie um peering direto

Você pode criar uma nova solicitação de peering usando o recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Inicie o recurso e configure configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configure conexões e envie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifique o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um peering direto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Deprovisionar um peering direto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o peering do Exchange usando o portal](howto-exchange-portal.md)
* [Converta um peering exchange legado para um recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [perguntas frequentes sobre a Internet](faqs.md).
