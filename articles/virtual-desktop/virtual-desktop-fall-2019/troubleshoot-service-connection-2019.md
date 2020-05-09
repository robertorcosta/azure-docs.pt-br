---
title: Solucionar problemas de conexão de serviço área de trabalho virtual do Windows-Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de locatário de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 01aff34839cc7385834468a08f30696efe84561f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614766"
---
# <a name="windows-virtual-desktop-service-connections"></a>Conexões do serviço de área de trabalho virtual do Windows

>[!IMPORTANT]
>Esse conteúdo se aplica à versão 2019 do outono que não dá suporte a Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver tentando gerenciar Azure Resource Manager objetos da área de trabalho virtual do Windows introduzidos na atualização do Spring 2020, consulte [Este artigo](../troubleshoot-service-connection.md).

Use este artigo para resolver problemas com conexões de cliente de área de trabalho virtual do Windows.

## <a name="provide-feedback"></a>Envie comentários

Você pode nos enviar comentários e discutir o serviço de área de trabalho virtual do Windows com a equipe de produto e outros membros ativos da Comunidade na [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O usuário se conecta, mas nada é exibido (sem feed)

Um usuário pode iniciar Área de Trabalho Remota clientes e é capaz de autenticar, no entanto, o usuário não vê nenhum ícone no feed de descoberta da Web.

Confirme se o usuário que relata os problemas foi atribuído a grupos de aplicativos usando esta linha de comando:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme se o usuário está fazendo logon com as credenciais corretas.

Se o cliente Web estiver sendo usado, confirme se não há problemas de credenciais em cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Máquinas virtuais de várias sessões do Windows 10 Enterprise não respondem

Se uma máquina virtual não estiver respondendo e você não puder acessá-la por meio de RDP, você precisará solucioná-la com o recurso de diagnóstico verificando o status do host.

Para verificar o status do host, execute este cmdlet:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Se o status do host `NoHeartBeat`for, isso significa que a VM não está respondendo e o agente não pode se comunicar com o serviço de área de trabalho virtual do Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Há algumas coisas que você pode fazer para corrigir o status noheartbeat.

### <a name="update-fslogix"></a>Atualizar FSLogix

Se o seu FSLogix não estiver atualizado, especialmente se for a versão 2.9.7205.27375 do frxdrvvt. sys, isso poderá causar um deadlock. Certifique-se de [atualizar o FSLogix para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Desabilitar BgTaskRegistrationMaintenanceTask

Se a atualização do FSLogix não funcionar, o problema poderá ser que um componente BiSrv esteja esgotando os recursos do sistema durante uma tarefa de manutenção semanal. Desabilite temporariamente a tarefa de manutenção desabilitando o BgTaskRegistrationMaintenanceTask com um destes dois métodos:

- Vá para o menu iniciar e procure **Agendador de tarefas**. Navegue até **Agendador de tarefas biblioteca** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Procure uma tarefa chamada **BgTaskRegistrationMaintenanceTask**. Quando encontrá-lo, clique nele com o botão direito do mouse e selecione **desabilitar** no menu suspenso.
- Abra um menu de linha de comando como administrador e execute o seguinte comando:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview-2019.md).
- Para solucionar problemas ao criar um pool de locatários e de host em um ambiente de área de trabalho virtual do Windows, confira [criação de locatário e pool de hosts](troubleshoot-set-up-issues-2019.md).
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration-2019.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho virtual do Windows, consulte [PowerShell da área de trabalho virtual do Windows](troubleshoot-powershell-2019.md).
- Para percorrer um tutorial de solução de problemas, consulte [tutorial: solucionar problemas de implantações de modelo do Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
