---
title: Criar ou modificar um emparelhamento direto usando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um emparelhamento direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680763"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Criar ou modificar um emparelhamento direto usando o PowerShell

Este artigo descreve como criar um emparelhamento direto da Microsoft usando cmdlets do PowerShell e o modelo de implantação Azure Resource Manager. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o [portal](howto-direct-portal.md)do Azure.

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [passo a passos de emparelhamento direto](walkthrough-direct-all.md) antes de começar a configuração.
* Se você já tiver conexões diretas de emparelhamento com a Microsoft que não são convertidas em recursos do Azure, consulte [converter um emparelhamento direto herdado para um recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Criar e provisionar um emparelhamento direto

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Obter a lista de locais de emparelhamento com suporte para emparelhamento direto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um emparelhamento direto
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar emparelhamento direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um emparelhamento direto
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionar um emparelhamento direto
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o emparelhamento do Exchange usando o PowerShell](howto-exchange-powershell.md)
* [Converter um emparelhamento do Exchange herdado para um recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, consulte [perguntas frequentes sobre emparelhamento da Internet](faqs.md).
