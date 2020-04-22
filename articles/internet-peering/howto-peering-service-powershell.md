---
title: Habilite o serviço de peering do Azure em um peering direto usando o PowerShell
titleSuffix: Azure
description: Habilite o serviço de peering do Azure em um peering direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686991"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Habilite o serviço de peering do Azure em um peering direto usando o PowerShell

Este artigo descreve como habilitar o [Azure Peering Service](overview-peering-service.md) em um peering direto usando cmdlets PowerShell e o modelo de implantação do Azure Resource Manager.

Se preferir, você pode completar este guia usando o [portal](howto-peering-service-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Revise os [pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um peering direto em sua assinatura para o qual você deseja ativar o Peering Service. Se você não tiver um, converta um peering direct legado ou crie um novo peering direto:
    * Para converter um peering direct legado, siga as instruções em [Converter um correspondente legado direto a um recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md).
    * Para criar um novo peering direto, siga as instruções em [Criar ou modificar um peering direto usando o PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhe com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Habilitar o Serviço de Emparelhamento em um Emparelhamento direto

### <a name="view-direct-peering"></a><a name= get></a>Ver peering direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Habilite o peering direto para o serviço de peering

Depois de obter o peering direto na etapa anterior, habilite-o para serviço de peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modifique uma conexão de peering direto

Se você precisar modificar as configurações de conexão, consulte a seção "Modificar um peering direto" em [Criar ou modificar um peering direto usando o PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o peering do Exchange usando o PowerShell](howto-exchange-powershell.md)
* [Converta um peering exchange legado para um recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para perguntas frequentes, consulte o [FAQ do Serviço de Peering](service-faqs.md).