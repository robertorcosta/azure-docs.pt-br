---
title: Criar pool de hosts da área de trabalho virtual do Windows PowerShell – Azure
description: Como criar um pool de hosts na área de trabalho virtual do Windows com cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 474eb4f5247aeb77edce0ebfde1611bf2deef493
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930396"
---
# <a name="create-a-host-pool-with-powershell"></a>Criar um pool de host com o PowerShell

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows, a versão 2019 sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de hosts pode ser associado a vários grupos do RemoteApp, um grupo de aplicativos da área de trabalho e vários hosts de sessão.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já seguiu as instruções em [Configurar o módulo do PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usar o cliente do PowerShell para criar um pool de hosts

Execute o seguinte cmdlet para entrar no ambiente da Área de Trabalho Virtual do Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

Esse cmdlet criará o pool de hosts, o espaço de trabalho e o grupo de aplicativos de desktop. Além disso, ele registrará o grupo de aplicativos da área de trabalho no espaço de trabalho. Você pode criar um espaço de trabalho com este cmdlet ou usar um espaço de trabalho existente. 

Execute o próximo cmdlet para criar um token de registro para autorizar um host de sessão a ingressar no pool de hosts e salvá-lo em um novo arquivo no computador local. Você pode especificar por quanto tempo o token de registro é válido usando o parâmetro-ExpirationHours.

>[!NOTE]
>A data de validade do token não pode ser inferior a uma hora e não mais de um mês. Se você definir *-ExpirationTime* fora desse limite, o cmdlet não criará o token.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Por exemplo, se você quiser criar um token que expire em duas horas, execute este cmdlet: 

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ')) 
```

Depois disso, execute este cmdlet para adicionar Azure Active Directory usuários ao grupo de aplicativos de área de trabalho padrão para o pool de hosts.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

Execute este próximo cmdlet para adicionar Azure Active Directory grupos de usuários ao grupo de aplicativos de área de trabalho padrão para o pool de hosts:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Execute o cmdlet a seguir para exportar o token de registro para uma variável, que será usada posteriormente no [registro das máquinas virtuais no pool de hosts da área de trabalho virtual do Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para o pool de hosts

Agora você pode criar uma máquina virtual do Azure que pode ser unida ao seu pool de hosts de área de trabalho virtual do Windows.

Você pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual por meio de uma imagem da galeria do Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual com base em uma imagem gerenciada](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual com base em uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se você estiver implantando uma máquina virtual usando o Windows 7 como o sistema operacional do host, o processo de criação e implantação será um pouco diferente. Para obter mais detalhes, consulte [implantar uma máquina virtual do Windows 7 na área de trabalho virtual do Windows](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Depois de criar as máquinas virtuais do host de sessão, [aplique uma licença do Windows a uma VM host de sessão](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar suas máquinas virtuais Windows ou Windows Server sem pagar por outra licença. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparar as máquinas virtuais para instalações do agente de área de trabalho virtual do Windows

Você precisa fazer o seguinte para preparar suas máquinas virtuais antes de instalar os agentes de área de trabalho virtual do Windows e registrar as máquinas virtuais em seu pool de hosts de área de trabalho virtual do Windows:

- Você deve ingressar no domínio no computador. Isso permite que os usuários de área de trabalho virtual do Windows sejam mapeados de sua conta de Azure Active Directory para sua conta do Active Directory e tenham acesso permitido à máquina virtual com êxito.
- Você deve instalar a função de Host da Sessão da Área de Trabalho Remota (RDSH) se a máquina virtual estiver executando um sistema operacional Windows Server. A função de RDSH permite que os agentes de área de trabalho virtual do Windows sejam instalados corretamente.

Para ingressar no domínio com êxito, execute as ações a seguir em cada máquina virtual:

1. [Conecte-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, inicie o **painel de controle** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar configurações**e, em seguida, selecione **alterar...**
4. Selecione **domínio** e, em seguida, insira o domínio Active Directory na rede virtual.
5. Autentique com uma conta de domínio que tenha privilégios para computadores de ingresso no domínio.

    >[!NOTE]
    > Se você estiver unindo suas VMs em um ambiente do Azure Active Directory Domain Services (Azure AD DS), verifique se o usuário de ingresso no domínio também é um membro do [grupo de Administradores do AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrar as máquinas virtuais no pool de hosts da área de trabalho virtual do Windows

Registrar as máquinas virtuais em um pool de hosts da área de trabalho virtual do Windows é tão simples quanto instalar os agentes de área de trabalho virtual do Windows.

Para registrar os agentes de área de trabalho virtual do Windows, faça o seguinte em cada máquina virtual:

1. [Conecte-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Baixe e instale o agente de área de trabalho virtual do Windows.
   - Baixe o [agente de área de trabalho virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Execute o instalador. Quando o instalador solicitar o token de registro, insira o valor obtido do cmdlet **Export-RdsRegistrationInfo** .
3. Baixe e instale o carregador de janelas do agente de área de trabalho virtual do Windows.
   - Baixe o [carregador de janelas do agente de área de trabalho virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](../security-center/security-center-just-in-time.md). Também recomendamos que você não atribua suas VMs a um IP público.

## <a name="next-steps"></a>Próximas etapas

Agora que você já fez um pool de hosts, você pode preenchê-lo com RemoteApps. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, confira o tutorial Gerenciar grupos de aplicativos.

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](./manage-app-groups.md)
