---
title: Personalizar propriedades RDP com PowerShell - Azure
description: Como personalizar propriedades RDP para Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128071"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalize propriedades do Protocolo de Desktop Remoto para um pool de host

A personalização das propriedades rdp (Remote Desktop Protocol, protocolo de desktop remoto) de um pool de host, como experiência de vários monitores e redirecionamento de áudio, permite que você ofereça uma experiência ideal para seus usuários com base em suas necessidades. Você pode personalizar propriedades RDP no Windows Virtual Desktop usando o parâmetro **-CustomRdpProperty** no **cmdlet Set-RdsHostPool.**

Consulte [as configurações de arquivo RDP suportadas](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) para obter uma lista completa de propriedades suportadas e seus valores padrão.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Propriedades RDP padrão

Por padrão, os arquivos RDP publicados contêm as seguintes propriedades:

|Propriedades de RDP | Desktops | RemoteApps |
|---|---| --- |
| Modo multi-monitor | habilitado | N/D |
| Redirecionamentos de unidade ativados | Unidades, área de transferência, impressoras, portas COM, dispositivos USB e smartcards| Unidades, área de transferência e impressoras |
| Modo de áudio remoto | Jogar localmente | Jogar localmente |

Todas as propriedades personalizadas definidas para o pool de host substituirão esses padrões.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicionar ou editar uma única propriedade RDP personalizada

Para adicionar ou editar uma única propriedade RDP personalizada, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Uma captura de tela do cmdlet PowerShell Get-RDSRemoteApp com nome e nome amigável destacado.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades RDP personalizadas

Para adicionar ou editar várias propriedades RDP personalizadas, execute os seguintes cmdlets do PowerShell, fornecendo as propriedades RDP personalizadas como uma seqüência separada de ponto e vírgula:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Uma captura de tela do cmdlet PowerShell Get-RDSRemoteApp com nome e nome amigável destacado.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades RDP personalizadas

Você pode redefinir propriedades RDP personalizadas individuais aos seus valores padrão seguindo as instruções em [Adicionar ou editar uma única propriedade RDP personalizada,](#add-or-edit-a-single-custom-rdp-property)ou você pode redefinir todas as propriedades RDP personalizadas para um pool de host, executando o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Uma captura de tela do cmdlet PowerShell Get-RDSRemoteApp com nome e nome amigável destacado.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou as propriedades RDP para um determinado pool de host, você pode fazer login em um cliente do Windows Virtual Desktop para testá-las como parte de uma sessão de usuário. Estes dois próximos How-tos lhe dirão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao Cliente de Área de Trabalho do Windows](connect-windows-7-and-10.md)
- [Conectar-se ao cliente Web](connect-web.md)
