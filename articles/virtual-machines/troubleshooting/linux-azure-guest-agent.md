---
title: Solucionando problemas do agente convidado do Linux do Azure
description: Solucionar problemas do agente convidado do Linux do Azure não está funcionando
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549858"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Solucionando problemas do agente convidado do Linux do Azure

O [agente convidado do Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) é um agente de VM (máquina virtual). Ele permite que a VM se comunique com o controlador de malha (o servidor físico subjacente no qual a VM está hospedada) no endereço IP 168.63.129.16. Esse endereço IP é um endereço IP público virtual que facilita a comunicação. Para obter mais informações, consulte [o que é o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="checking-agent-status-and-version"></a>Verificando o status e a versão do agente

Veja https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Solucionando problemas do agente de VM que está no status não pronto

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Etapa 1 verificar se o serviço do agente convidado do Linux do Azure está em execução

Dependendo do distribuição, o nome do serviço pode ser walinuxagent ou waagent:

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


Se você puder ver os serviços e eles estiverem em execução, reinicie o serviço para ver se o problema foi resolvido. Se os serviços forem interrompidos, inicie-os e aguarde alguns minutos. Em seguida, verifique se o **status do agente** está relatando como **pronto**. Para solucionar problemas desses problemas, entre em contato com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Etapa 2 verificar se a atualização automática está habilitada

Verifique essa configuração em/etc/waagent.conf:

```
AutoUpdate.Enabled=y
```

Para obter mais informações sobre como atualizar o agente Linux do Azure, consulte https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Etapa 3 verificar se a VM pode se conectar ao controlador de malha

Use uma ferramenta como a rotação para testar se a VM pode se conectar ao 168.63.129.16 nas portas 80, 32526 e 443. Se a VM não se conectar conforme o esperado, verifique se a comunicação de saída pelas portas 80, 443 e 32526 está aberta no firewall local na VM. Se esse endereço IP estiver bloqueado, o agente de VM poderá exibir um comportamento inesperado em vários cenários.

## <a name="advanced-troubleshooting"></a>Solução de problemas avançada

Os eventos para solução de problemas do agente convidado do Linux do Azure são registrados nos seguintes arquivos de log:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Não é possível conectar-se ao WireServer IP (IP do host) 

Você observa as seguintes entradas de erro em/var/log/waagent.log:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Analisa**

A VM não pode alcançar o IP WireServer no servidor host.

**Solução**

1. Como o IP WireServer não está acessível, conecte-se à VM usando SSH e tente acessar a seguinte URL da ondulação: http://168.63.129.16/?comp=versions 
1. Verifique se há problemas que possam ser causados por um firewall, um proxy ou outra fonte que possa estar bloqueando o acesso ao endereço IP 168.63.129.16.
1. Verifique se o Linux IPTables ou um firewall de terceiros está bloqueando o acesso às portas 80, 443 e 32526. Para obter mais informações sobre por que esse endereço não deve ser bloqueado, consulte [o que é o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="next-steps"></a>Próximas etapas

Para solucionar ainda mais o problema "o agente convidado do Windows Azure não está funcionando", [entre em contato com o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
