---
title: Habilite o serviço de peering em um peering direto usando o PowerShell
titleSuffix: Azure
description: Habilite o serviço de peering em um peering direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774179"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Habilite o serviço de peering em um peering direto usando o PowerShell

Este artigo descreve como ativar [o Peering Service](overview-peering-service.md) em um peering direto usando cmdlets PowerShell e o modelo de implantação do Gerenciador de recursos.

Se preferir, você pode completar este guia usando o [portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise [os pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um peering direto em sua assinatura que deseja ativar o Peering Service. Se você não tiver um, converta um peering direct legado ou crie um novo peering direto.
    * Para converter um peering direct legado, siga as instruções em [Converter um correspondente legado direto ao recurso DoZure usando o PowerShell](howto-legacy-direct-powershell.md).
    * Para criar um novo peering direto, siga as instruções em [Criar ou modificar um peering direto usando o PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Habilitar o Serviço de Emparelhamento em um Emparelhamento direto

### <a name="view-direct-peering"></a><a name= get></a>Ver peering direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Habilite o peering direto para o serviço de peering

Depois de obter peering direto na etapa anterior, habilite-o para serviço de peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modifique uma conexão de peering direto

Se você precisar modificar as configurações de conexão, consulte Modificar uma seção **de peering direto** em [Criar ou modificar um peering direto usando o PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o peering do Exchange usando o PowerShell](howto-exchange-powershell.md)
* [Converta um peering exchange legado para o recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para perguntas frequentes, consulte [Peering Service FAQ](service-faqs.md).