---
title: Conexão de serviço de solução de problemas Windows Virtual Desktop - Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de inquilino do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127452"
---
# <a name="windows-virtual-desktop-service-connections"></a>Conexões de serviço de desktop virtual do Windows

Use este artigo para resolver problemas com conexões de clientes do Windows Virtual Desktop.

## <a name="provide-feedback"></a>Fornecer comentários

Você pode nos dar feedback e discutir o Serviço de Desktop Virtual do Windows com a equipe de produtos e outros membros ativos da comunidade na [Comunidade De Desktop Virtual](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)do Windows .

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O usuário se conecta, mas nada é exibido (sem feed)

Um usuário pode iniciar clientes de Área de Trabalho Remota e é capaz de autenticar, no entanto, o usuário não vê nenhum ícone no feed de descoberta da Web.

Confirme se o usuário que reporta os problemas foi atribuído a grupos de aplicativos usando esta linha de comando:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme se o usuário está fazendo login com as credenciais corretas.

Se o cliente web estiver sendo usado, confirme se não há problemas de credenciais armazenadas em cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>As máquinas virtuais multi-sessões do Windows 10 Enterprise não respondem

Se uma máquina virtual não estiver respondendo e você não puder acessá-la através de RDP, você precisará soluciocá-la com o recurso de diagnóstico, verificando o status do host.

Para verificar o status do host, execute este cmdlet:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Se o status `NoHeartBeat`do host for , isso significa que a VM não está respondendo e o agente não pode se comunicar com o serviço de desktop virtual do Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Existem algumas coisas que você pode fazer para corrigir o status NoHeartBeat.

### <a name="update-fslogix"></a>Atualizar FSlogix

Se o seu FSLogix não estiver atualizado, especialmente se for a versão 2.9.7205.27375 de frxdrvvt.sys, pode causar um impasse. Certifique-se de [atualizar o FSLogix para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Desativar BgTaskRegistrationMaintenanceTaskTask

Se a atualização do FSLogix não funcionar, o problema pode ser que um componente BiSrv esteja esgotando os recursos do sistema durante uma tarefa de manutenção semanal. Desative temporariamente a tarefa de manutenção desativando o BgTaskRegistrationMaintenanceTask com um desses dois métodos:

- Vá para o menu Iniciar e procure o **Agendador de tarefas**. Navegue até a **Biblioteca de Agendadores de Tarefas** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Procure uma tarefa chamada **BgTaskRegistrationMaintenanceTask**. Ao encontrá-lo, clique com o botão direito do mouse e **selecione Desativar** no menu suspenso.
- Abra um menu de linha de comando como administrador e execute o seguinte comando:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre a solução de problemas do Windows Virtual Desktop e as faixas de escalonamento, consulte [Visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de solução de problemas.
- Para solucionar problemas ao criar um inquilino e um pool de host em um ambiente de Desktop Virtual do Windows, consulte [Criação de inquilino e pool de host](troubleshoot-set-up-issues.md).
- Para solucionar problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte [Configuração da máquina virtual do host session](troubleshoot-vm-configuration.md).
- Para solucionar problemas ao usar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de solução de problemas, consulte Tutorial: Implantação de [modelo do Gerenciador de recursos de solução de problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
