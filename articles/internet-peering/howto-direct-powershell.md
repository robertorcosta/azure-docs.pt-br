---
title: Criar ou modificar um peering direto usando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um peering direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774231"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Criar ou modificar um peering direto usando o PowerShell

Este artigo descreve como criar um peering Microsoft Direct usando cmdlets PowerShell e o modelo de implantação do Gerenciador de Recursos. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode completar este guia usando o [portal](howto-direct-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise [pré-requisitos](prerequisites.md) e [passe-passo do peering direto](walkthrough-direct-all.md) antes de iniciar a configuração.
* Caso você já tenha o peering direto com a Microsoft, que não são convertidos em recursos do Azure, consulte Converter um recurso direct legacy [para o Recurso Do Azure usando o PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell
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

* [Criar ou modificar o peering do Exchange usando o PowerShell](howto-exchange-powershell.md).
* [Converta um peering exchange legado para o recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, visite [perguntas frequentes sobre peering na Internet](faqs.md)
