---
title: Tipo de atribuição pessoal da área de trabalho do Windows Virtual Desktop - Azure
description: Como configurar o tipo de atribuição para um pool de host de desktop pessoal do Windows Virtual Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128294"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configure o tipo de atribuição de pool de host de desktop pessoal

Você pode configurar o tipo de atribuição do seu pool de host de desktop pessoal para ajustar o ambiente de desktop virtual do Windows para atender melhor às suas necessidades. Neste tópico, mostraremos como configurar atribuição automática ou direta para seus usuários.

>[!NOTE]
> As instruções deste artigo só se aplicam a pools de host de desktop pessoais, não a pools de host saqueados, uma vez que os usuários em pools de host saqueados não são atribuídos a hosts de sessão específicos.

## <a name="configure-automatic-assignment"></a>Configurar atribuição automática

Atribuição automática é o tipo de atribuição padrão para novos pools de host de desktop pessoal criados no ambiente de desktop virtual do Windows. Atribuir automaticamente usuários não requer um host de sessão específico.

Para atribuir automaticamente aos usuários, primeiro atribua-os ao pool de host de desktop pessoal para que eles possam ver a área de trabalho em seu feed. Quando um usuário atribuído inicia a área de trabalho em seu feed, ele reivindicará um host de sessão disponível se ainda não estiver conectado ao pool de host, que completa o processo de atribuição.

Antes de iniciar, [baixe e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) se você ainda não tiver. 

> [!NOTE]
> Certifique-se de que instalou o módulo Windows Virtual Desktop PowerShell versão 1.0.1534.2001 ou posterior antes de seguir estas instruções.

Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Para configurar um pool de host para atribuir automaticamente os usuários às VMs, execute o seguinte cmdlet powershell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Para atribuir um usuário ao pool de host de desktop pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configurar atribuição direta

Ao contrário da atribuição automática, quando você usa atribuição direta, você deve atribuir o usuário ao pool de host de desktop pessoal e a um host de sessão específico antes que eles possam se conectar à sua área de trabalho pessoal. Se o usuário for atribuído apenas a um pool de host sem uma atribuição de host de sessão, ele não poderá acessar recursos.

Para configurar um pool de host para exigir a atribuição direta dos usuários aos hosts de sessão, execute o seguinte cmdlet powershell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Para atribuir um usuário ao pool de host de desktop pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Para atribuir um usuário a um host de sessão específico, execute o seguinte cmdlet powershell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o tipo de atribuição de desktop pessoal, você pode fazer login em um cliente do Windows Virtual Desktop para testá-lo como parte de uma sessão de usuário. Estes dois próximos How-tos lhe dirão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao Cliente de Área de Trabalho do Windows](connect-windows-7-and-10.md)
- [Conectar-se ao cliente Web](connect-web.md)
