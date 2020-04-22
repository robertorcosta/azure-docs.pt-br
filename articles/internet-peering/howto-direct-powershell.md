---
title: Crie ou modifique um peering direto usando o PowerShell
titleSuffix: Azure
description: Crie ou modifique um peering direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680763"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Crie ou modifique um peering direto usando o PowerShell

Este artigo descreve como criar um peering microsoft direct usando cmdlets PowerShell e o modelo de implantação do Azure Resource Manager. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode completar este guia usando o [portal](howto-direct-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Revise os [pré-requisitos](prerequisites.md) e o [passo a passo do peering direto](walkthrough-direct-all.md) antes de iniciar a configuração.
* Se você já tiver conexões de peering diretas com a Microsoft que não foram convertidas em recursos do Azure, consulte [Converter um correspondente legado de peering direto a um recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhe com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Criar e prover um peering direto

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Obtenha a lista de locais de peering suportados para peering direto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Crie um peering direto
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifique o peering direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um peering direto
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Deprovisionar um peering direto
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o peering do Exchange usando o PowerShell](howto-exchange-powershell.md)
* [Converta um peering exchange legado para um recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, consulte [perguntas frequentes sobre a Internet](faqs.md).
