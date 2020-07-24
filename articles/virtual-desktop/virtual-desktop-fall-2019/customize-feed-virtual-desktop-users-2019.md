---
title: Personalizar feed para área de trabalho virtual do Windows usuários do outono 2019 – Azure
description: Como personalizar o feed para usuários da área de trabalho virtual do Windows com cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9c573fc1aa220b9301e51ef0ccb1190d9243250e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011219"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para usuários da Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Esse conteúdo se aplica à versão Outono 2019 que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager introduzidos na atualização Spring 2020, confira [este artigo](../customize-feed-for-virtual-desktop-users.md).

Você pode personalizar o feed para que os recursos do RemoteApp e da área de trabalho remota apareçam de maneira reconhecível para seus usuários.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizar o nome de exibição para um RemoteApp

Você pode alterar o nome de exibição de um RemoteApp publicado definindo o nome amigável. Por padrão, o nome amigável é igual ao nome do programa RemoteApp.

Para recuperar uma lista de RemoteApps publicados para um grupo de aplicativos, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](../media/get-rdsremoteapp.png)

Para atribuir um nome amigável a um RemoteApp, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com nome e novo FriendlyName realçado.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizar o nome de exibição para um Área de Trabalho Remota

Você pode alterar o nome de exibição de uma área de trabalho remota publicada definindo um nome amigável. Se você criou manualmente um pool de hosts e um grupo de aplicativos da área de trabalho por meio do PowerShell, o nome amigável padrão é "área de trabalho da sessão". Se você criou um pool de hosts e um grupo de aplicativos de área de trabalho por meio do modelo de Azure Resource Manager do GitHub ou da oferta do Azure Marketplace, o nome amigável padrão é o mesmo que o nome do pool de hosts.

Para recuperar o recurso de área de trabalho remota, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](../media/get-rdsremotedesktop.png)

Para atribuir um nome amigável ao recurso de área de trabalho remota, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com nome e novo FriendlyName realçado.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou o feed para os usuários, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-lo. Para fazer isso, vá para o How-tos da área de trabalho virtual do Windows:

 * [Conectar-se do Windows 10 ou Windows 7](connect-windows-7-10-2019.md)
 * [Conectar-se de um navegador da Web](connect-web-2019.md)
