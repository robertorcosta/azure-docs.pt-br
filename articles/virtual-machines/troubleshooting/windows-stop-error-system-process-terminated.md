---
title: Processo do sistema de status de erro de parada do Windows encerrado
description: Este artigo fornece etapas para resolver problemas em que o sistema operacional encontra o erro de parada 0xC000021A, que impede a inicialização de uma máquina virtual do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b07033f96402edc24edd51de57661603e57472bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341752"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Erro de parada do Windows-o processo do sistema de status 0xC000021A foi encerrado

Este artigo fornece etapas para resolver problemas em que o sistema operacional (SO) encontra o erro de parada 0xC000021A, que mantém a inicialização de uma VM (máquina virtual) do Azure.

## <a name="symptom"></a>Sintoma

Quando você usa o [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para exibir a captura de tela da VM, a captura de tela exibe a mensagem informando que o sistema operacional encontrou um erro durante a inicialização, com a seguinte mensagem:

**Seu PC encontrou um problema e precisa ser reiniciado. Estamos apenas coletando algumas informações de erro e, em seguida, você pode reiniciar. (# #% concluído) Se você quiser saber mais, poderá pesquisar online mais tarde para este erro: 0xC000021a**.

  ![A Figura 1 exibe o código de erro #0xC000021A com a mensagem "o PC teve um problema e precisa ser reiniciado. Estamos apenas coletando algumas informações de erro e, em seguida, você pode reiniciar. ".](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Causa

O erro 0xC000021A significa **STATUS_SYSTEM_PROCESS_TERMINATED**.

Esse erro ocorre quando um processo crítico, como WinLogon (winlogon.exe) ou o subsistema de Run-Time do servidor cliente (csrss.exe) falha. Depois que o kernel detecta que qualquer um desses serviços foi interrompido, ele gera o erro **Stop 0xC000021A** . Esse erro pode ter várias causas, incluindo:

- Arquivos do sistema incompatíveis foram instalados.
- Falha em uma instalação de atualização do Service Pack ou do KB.
- Um programa de backup usado para restaurar um disco rígido não restaurou corretamente os arquivos que podem estar em uso.
- Um programa de terceiros incompatível foi instalado.

## <a name="solution"></a>Solução

### <a name="collect-the-memory-dump-file"></a>Coletar o arquivo de despejo de memória

Para resolver esse problema, será necessário analisar o despejo de memória. Colete o arquivo de despejo de memória para a falha e contate o suporte. Para coletar o arquivo de despejo, siga estas etapas:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Anexar o disco do sistema operacional a uma nova VM de reparo

1.  Use as [etapas 1 a 3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar uma VM de reparo.
2.  Usando **conexão de área de trabalho remota**, conecte-se à VM de reparo.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localizar o arquivo de despejo e enviar um tíquete de suporte

1.  Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra do driver atribuída ao disco do sistema operacional anexado for F, vá para F:\Windows.
2.  Localize o arquivo Memory. dmp e [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.
3.  Se você estiver tendo problemas para localizar o arquivo Memory. dmp, talvez queira usar [chamadas de NMI (interrupção não mascaráveis) no console serial](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Você pode seguir o guia para [gerar um arquivo de despejo de memória usando chamadas NMI aqui](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre solução de problemas, consulte [Solucionando problemas de erros de inicialização comuns](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-error-troubleshoot) ou [como solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-windows). Você também deve se familiarizar com [o uso do diagnóstico de inicialização para solucionar problemas de uma máquina virtual](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
- Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).
- Se você não puder se conectar à sua VM, consulte [solucionar problemas de conexões RDP para uma VM do Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
