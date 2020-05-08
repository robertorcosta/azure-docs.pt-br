---
title: Solucionar problemas de conexão de serviço área de trabalho virtual do Windows-Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de locatário de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612632"
---
# <a name="windows-virtual-desktop-service-connections"></a>Conexões do serviço de área de trabalho virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows, a versão 2019 sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Use este artigo para resolver problemas com conexões de cliente de área de trabalho virtual do Windows.

## <a name="provide-feedback"></a>Envie comentários

Você pode nos enviar comentários e discutir o serviço de área de trabalho virtual do Windows com a equipe de produto e outros membros ativos da Comunidade na [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O usuário se conecta, mas nada é exibido (sem feed)

Um usuário pode iniciar Área de Trabalho Remota clientes e é capaz de autenticar, no entanto, o usuário não vê nenhum ícone no feed de descoberta da Web.

Confirme se o usuário que relata os problemas foi atribuído a grupos de aplicativos usando esta linha de comando:

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

Confirme se o usuário está entrando com as credenciais corretas.

Se o cliente Web estiver sendo usado, confirme se não há problemas de credenciais em cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Máquinas virtuais de várias sessões do Windows 10 Enterprise não respondem

Se uma máquina virtual não estiver respondendo e você não puder acessá-la por meio de RDP, você precisará solucioná-la com o recurso de diagnóstico verificando o status do host.

Para verificar o status do host, execute este cmdlet:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

Se o status do host `NoHeartBeat`for, isso significa que a VM não está respondendo e o agente não pode se comunicar com o serviço de área de trabalho virtual do Windows.

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

Há algumas coisas que você pode fazer para corrigir o status noheartbeat.

### <a name="update-fslogix"></a>Atualizar FSLogix

Se o seu FSLogix não estiver atualizado, especialmente se for a versão 2.9.7205.27375 do frxdrvvt. sys, isso poderá causar um deadlock. Certifique-se de [atualizar o FSLogix para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=2084562).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas ao criar um ambiente de área de trabalho virtual do Windows e um pool de hosts em um ambiente de área de trabalho virtual do Windows, consulte [criação de pool de hosts e](troubleshoot-set-up-issues.md)
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho virtual do Windows, consulte [PowerShell da área de trabalho virtual do Windows](troubleshoot-powershell.md).
- Para percorrer um tutorial de solução de problemas, consulte [tutorial: solucionar problemas de implantações de modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
