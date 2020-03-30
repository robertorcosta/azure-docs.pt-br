---
title: Criar o pool de hosts do Windows Virtual Desktop PowerShell - Azure
description: Como criar um pool de host sustadores no Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246952"
---
# <a name="create-a-host-pool-with-powershell"></a>Criar um pool de host com o PowerShell

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Use seu cliente PowerShell para criar um pool de host

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Execute o cmdlet a seguir para fazer login no ambiente de desktop virtual do Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Em seguida, execute este cmdlet para criar um novo pool de host satiscono no seu inquilino do Windows Virtual Desktop:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Execute o próximo cmdlet para criar um token de registro para autorizar um host de sessão a entrar no pool de host e salvá-lo em um novo arquivo em seu computador local. Você pode especificar quanto tempo o token de registro é válido usando o parâmetro -ExpirHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Depois disso, execute este cmdlet para adicionar usuários do Azure Active Directory ao grupo de aplicativos de desktop padrão para o pool de host.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

O **cmdlet Add-RdsAppGroupUser** não suporta a adição de grupos de segurança e adiciona apenas um usuário por vez ao grupo de aplicativos. Se você quiser adicionar vários usuários ao grupo do aplicativo, execute o cmdlet com os nomes principais de usuário apropriados.

Execute o cmdlet a seguir para exportar o token de registro para uma variável, que você usará mais tarde no [Registrar as máquinas virtuais para o pool de hosts do Windows Virtual Desktop](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Crie máquinas virtuais para o pool de host

Agora você pode criar uma máquina virtual do Azure que pode ser acompanhada ao seu pool de hosts do Windows Virtual Desktop.

Você pode criar uma máquina virtual de várias maneiras:

- [Crie uma máquina virtual a partir de uma imagem da Azure Gallery](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Crie uma máquina virtual a partir de uma imagem gerenciada](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Crie uma máquina virtual a partir de uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se você estiver implantando uma máquina virtual usando o Windows 7 como sistema operacional host, o processo de criação e implantação será um pouco diferente. Para obter mais detalhes, consulte [Implantar uma máquina virtual do Windows 7 no Windows Virtual Desktop](deploy-windows-7-virtual-machine.md).

Depois de criar suas máquinas virtuais host de sessão, [aplique uma licença do Windows a uma VM host de sessão](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar suas máquinas virtuais Windows ou Windows Server sem pagar por outra licença. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Prepare as máquinas virtuais para instalações de agentes de desktop virtuais do Windows

Você precisa fazer as seguintes coisas para preparar suas máquinas virtuais antes de instalar os agentes de desktop virtual do Windows e registrar as máquinas virtuais no seu pool de hosts do Windows Virtual Desktop:

- Você deve se juntar à máquina. Isso permite que os usuários de Desktop Virtual do Windows de entrada sejam mapeados de sua conta do Azure Active Directory para sua conta do Active Directory e tenham acesso com sucesso à máquina virtual.
- Você deve instalar a função RDSH (Remote Desktop Session Host, host de sessão de sessão remota se a máquina virtual estiver executando um sistema operacional do Windows Server). A função RDSH permite que os agentes do Windows Virtual Desktop instalem corretamente.

Para participar com sucesso do domínio, faça as seguintes coisas em cada máquina virtual:

1. [Conecte-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, inicie o **Painel de Controle** e selecione **Sistema**.
3. Selecione **Nome do computador,** **selecione Alterar configurações**e, em seguida, selecione **Alterar...**
4. Selecione **Domínio** e, em seguida, insira o domínio Active Directory na rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios para as máquinas de adesão ao domínio.

    >[!NOTE]
    > Se você estiver unindo suas VMs em um ambiente do Azure Active Directory Domain Services (Azure AD DS), verifique se o usuário de ingresso no domínio também é um membro do [grupo de Administradores do AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registre as máquinas virtuais no pool de hosts do Windows Virtual Desktop

Registrar as máquinas virtuais em um pool de hosts do Windows Virtual Desktop é tão simples quanto instalar os agentes do Windows Virtual Desktop.

Para registrar os agentes de desktop virtual do Windows, faça o seguinte em cada máquina virtual:

1. [Conecte-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Baixe e instale o Windows Virtual Desktop Agent.
   - Baixe o [Agente de Desktop Virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Clique com o botão direito do mouse no instalador baixado, selecione **Propriedades,** **selecione Desbloquear**e selecione **OK**. Isso permitirá que seu sistema confie no instalador.
   - Execute o instalador. Quando o instalador pedir o token de registro, digite o valor que você obteve do cmdlet **Export-RdsRegistrationInfo.**
3. Baixe e instale o Bootloader do agente de desktop virtual do Windows.
   - Baixe o [Bootloader do agente de desktop virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Clique com o botão direito do mouse no instalador baixado, selecione **Propriedades,** **selecione Desbloquear**e selecione **OK**. Isso permitirá que seu sistema confie no instalador.
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você fez uma piscina de host, você pode povoá-lo com RemoteApps. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, confira o tutorial Gerenciar grupos de aplicativos.

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](./manage-app-groups.md)
