---
title: Criar ou modificar um Emparelhamento direto usando o portal
titleSuffix: Azure
description: Criar ou modificar um Emparelhamento direto usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775323"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Criar ou modificar um Emparelhamento direto usando o portal

Este artigo descreve como criar um peering microsoft direct usando o portal. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode completar este guia usando o [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise [pré-requisitos](prerequisites.md) e [passe-passo do peering direto](walkthrough-direct-all.md) antes de iniciar a configuração.
* Caso você já tenha o peering direto com a Microsoft, que não são convertidos em recursos do Azure, consulte [Converter um recurso direct legacy para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Criar e prover um peering direto

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Faça login no portal e selecione sua assinatura
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

* [Crie ou modifique o peering do Exchange usando o portal](howto-exchange-portal.md).
* [Converta um peering exchange legado para o recurso Do Zure usando o portal](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre peering na Internet](faqs.md)
