---
title: Criar ou modificar um peering exchange usando powershell
titleSuffix: Azure
description: Criar ou modificar um peering exchange usando powershell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678648"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Criar ou modificar um peering exchange usando powershell

Este artigo descreve como criar um peering do Microsoft Exchange usando cmdlets PowerShell e o modelo de implantação do Gerenciador de Recursos. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode completar este guia usando o [portal](howto-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Revise os [pré-requisitos](prerequisites.md) e o [passo a passo do Exchange](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Se você já tiver peerings do Exchange com a Microsoft que não foram convertidos em recursos do Azure, consulte [Converter um recurso legacy Exchange para um recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhe com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Criar e prover um peering exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Obtenha a lista de locais de peering suportados para peering do Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um peering de troca
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Obter peering exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um peering de troca
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Deprovisionum peering exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Crie ou modifique um peering direto usando o PowerShell](howto-direct-powershell.md)
* [Converta um peering direct legado para um recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, consulte [perguntas frequentes sobre a Internet](faqs.md).
