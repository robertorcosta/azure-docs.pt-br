---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678479"
---
Antes de iniciar a configuração, instale e importe os módulos necessários. Você precisa de privilégios de administrador para instalar módulos no PowerShell.

1. Instale e importe o módulo Az.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instale e importe o módulo Az.Peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Verifique se os módulos importados corretamente usando este comando:
    ```powershell
    Get-Module
    ```
1. Conecte à sua conta do Azure usando este comando:
    ```powershell
    Connect-AzAccount
    ```
1. Verifique as assinaturas da conta e selecione a assinatura na qual deseja criar um peering.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Se você ainda não tem um grupo de recursos, você deve criar um antes de criar um peering. Faça isso ao executar o seguinte comando:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Se você ainda não associou seu ASN e sua assinatura, siga os passos em [Associate Peer ASN](../howto-subscription-association-powershell.md). Esta ação é necessária para solicitar um peering.

> [!NOTE]
> A localização de um grupo de recursos é independente do local onde você escolhe configurar um peering.
&nbsp;
