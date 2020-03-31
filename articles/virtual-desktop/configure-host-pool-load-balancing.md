---
title: Configurar o balanceamento de carga do Windows Virtual Desktop - Azure
description: Como configurar o método de balanceamento de carga para um ambiente de desktop virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128298"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurar o método de balanceamento de carga da Área de Trabalho Virtual do Windows

A configuração do método de balanceamento de carga para um pool de hostpermite que você ajuste o ambiente de desktop virtual do Windows para melhor atender às suas necessidades.

>[!NOTE]
> Isso não se aplica a um pool de host de desktop persistente porque os usuários sempre têm um mapeamento 1:1 para um host de sessão dentro do pool de host.

## <a name="configure-breadth-first-load-balancing"></a>Configurar o balanceamento de carga de largura-primeira

O balanceamento de carga de largura é a configuração padrão para novos pools de host não persistentes. O balanceamento de carga de largura-primeira distribui novas sessões de usuário em todos os hosts de sessão disponíveis no pool de host. Ao configurar o balanceamento de carga de largura-primeira, você pode definir um limite máximo de sessão por host de sessão no pool de host.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Para configurar um pool de host para executar o balanceamento de carga de largura-primeiro sem ajustar o limite máximo da sessão, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Para configurar um pool de host para executar o balanceamento de carga de largura-primeiro e para usar um novo limite máximo de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configure o balanceamento de carga de profundidade-primeiro

O balanceamento de carga em profundidade distribui novas sessões de usuário para um host de sessão disponível com o maior número de conexões, mas não atingiu seu limite máximo de limite de sessão. Ao configurar o balanceamento de carga em profundidade, você **deve** definir um limite máximo de sessão por host de sessão no pool de host.

Para configurar um pool de host para executar o balanceamento de carga em profundidade, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
