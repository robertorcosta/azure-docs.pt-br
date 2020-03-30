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
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774218"
---
Antes de iniciar a configuração, instale e importe os módulos necessários. Você precisará de privilégios de administrador para instalar módulos no PowerShell.

1. Instalar e importar módulo Az
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instalar e importar módulo Az.Peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Verifique se os módulos são importados com multa usando o comando abaixo.
    ```powershell
    Get-Module
    ```
1. Faça login na sua conta do Azure usando o seguinte comando.
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
> Se você ainda não associou seu ASN e assinatura, siga as etapas do [Associate Peer ASN](../howto-subscription-association-powershell.md). Isso é necessário para solicitar um peering.

> [!NOTE]
> A localização do grupo de recursos é independente do local onde você escolhe configurar um peering.
&nbsp;
