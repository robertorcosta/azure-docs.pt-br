---
title: Tipo de atribuição da área de trabalho pessoal da Área de Trabalho Virtual do Windows – Azure
description: Como configurar o tipo de atribuição para um pool de hosts da área de trabalho pessoal da Área de Trabalho Virtual do Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158ac92a930b53e02ee81570c62711ca27dc4ae8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200385"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurar o tipo de atribuição do pool de hosts da área de trabalho pessoal

>[!IMPORTANT]
>Este conteúdo se aplica à atualização da Spring 2020 com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows na versão 2019, sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> A atualização 2020 da Área de Trabalho Virtual do Windows está em versão prévia pública no momento. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode configurar o tipo de atribuição de seu pool de hosts da área de trabalho pessoal para ajustar o ambiente da Área de Trabalho Virtual do Windows e atender melhor às suas necessidades. Neste tópico, mostraremos como configurar a atribuição automática ou direta para seus usuários.

>[!NOTE]
> As instruções deste artigo se aplicam somente a pools de hosts da área de trabalho pessoal, e não a pools de hosts em pool, já que os usuários em pools de hosts em pool não são atribuídos a hosts de sessão específicos.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já baixou e instalou o módulo do PowerShell de área de trabalho virtual do Windows. Se você ainda não fez isso, siga as instruções em [Configurar o módulo do PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Configurar a atribuição automática

A atribuição automática é o tipo de atribuição padrão para novos pools de hosts da área de trabalho pessoal criados em seu ambiente da Área de Trabalho Virtual do Windows. A atribuição automática de usuários não exige um host de sessão específico.

Para atribuir usuários automaticamente, primeiro atribua-os ao pool de hosts da área de trabalho pessoal para que eles possam ver a área de trabalho no próprio feed. Quando um usuário atribuído iniciar a área de trabalho no feed, ele solicitará um host de sessão disponível, se ainda não tiver se conectado ao pool de hosts, o que concluirá o processo de atribuição.

Para configurar um pool de hosts para atribuir usuários automaticamente a VMs, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Para atribuir um usuário ao pool de hosts da área de trabalho pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Configurar a atribuição direta

Ao contrário da atribuição automática, quando você usa a atribuição direta, deve atribuir o usuário ao pool de hosts da área de trabalho pessoal e a um host de sessão específico antes que ele possa se conectar à área de trabalho pessoal. Se o usuário for atribuído apenas a um pool de hosts sem uma atribuição de host de sessão, ele não conseguirá acessar os recursos.

Para configurar um pool de hosts para exigir a atribuição direta de usuários a hosts de sessão, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Para atribuir um usuário ao pool de hosts da área de trabalho pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Para atribuir um usuário a um host de sessão específico, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o tipo de atribuição da área de trabalho pessoal, pode entrar em um cliente da Área de Trabalho Virtual do Windows para testá-lo como parte de uma sessão de usuário. Os dois tutoriais a seguir mostrarão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao cliente da Área de Trabalho do Windows](connect-windows-7-and-10.md)
- [Conectar-se ao cliente Web](connect-web.md)
- [Conectar-se ao cliente Android](connect-android.md)
- [Conectar-se ao cliente iOS](connect-ios.md)
- [Conectar-se ao cliente macOS](connect-macos.md)