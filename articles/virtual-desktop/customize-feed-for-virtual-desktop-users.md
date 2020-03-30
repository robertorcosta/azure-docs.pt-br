---
title: Personalizar o feed para usuários de Desktop Virtual do Windows - Azure
description: Como personalizar o feed para usuários do Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128090"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para usuários da Área de Trabalho Virtual do Windows

Você pode personalizar o feed para que os recursos de remoteApp e desktop remoto apareçam de forma reconhecível para seus usuários.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalize o nome de exibição para um RemoteApp

Você pode alterar o nome de exibição de um RemoteApp publicado definindo o nome amigável. Por padrão, o nome amigável é o mesmo que o nome do programa RemoteApp.

Para recuperar uma lista de RemoteApps publicados para um grupo de aplicativos, execute o seguinte cmdlet powershell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Uma captura de tela do cmdlet PowerShell Get-RDSRemoteApp com nome e nome amigável destacado.](media/get-rdsremoteapp.png)

Para atribuir um nome amigável a um RemoteApp, execute o seguinte cmdlet powershell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Uma captura de tela do PowerShell cmdlet Set-RDSRemoteApp com nome e novo friendlyname destacado.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalize o nome da tela para uma área de trabalho remota

Você pode alterar o nome de exibição de uma área de trabalho remota publicada definindo um nome amigável. Se você criou manualmente um pool de host e um grupo de aplicativos de desktop através do PowerShell, o nome amigo padrão é "Session Desktop". Se você criou um pool de host e um grupo de aplicativos de desktop através do modelo GitHub Azure Resource Manager ou da oferta do Azure Marketplace, o nome amigo padrão é o mesmo que o nome do pool de host.

Para recuperar o recurso de área de trabalho remota, execute o seguinte cmdlet powershell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Uma captura de tela do cmdlet PowerShell Get-RDSRemoteApp com nome e nome amigável destacado.](media/get-rdsremotedesktop.png)

Para atribuir um nome amigável ao recurso de desktop remoto, execute o seguinte cmdlet powershell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Uma captura de tela do PowerShell cmdlet Set-RDSRemoteApp com nome e novo friendlyname destacado.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou o feed para usuários, você pode fazer login em um cliente do Windows Virtual Desktop para testá-lo. Para isso, continue a conectar-se ao Windows Virtual Desktop How-tos:
    
 * [Conectar-se do Windows 10 ou Windows 7](connect-windows-7-and-10.md)
 * [Conectar-se de um navegador da Web](connect-web.md) 
