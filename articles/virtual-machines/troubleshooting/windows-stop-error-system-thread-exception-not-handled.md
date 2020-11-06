---
title: Erro de parada do Windows-exceção de thread do sistema 0x0000007E não tratada
description: Este artigo fornece etapas para resolver problemas em que o sistema operacional convidado encontra um problema e deseja reiniciar a VM do Azure. A mensagem irá declarar que "uma exceção de thread do sistema não foi tratada".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423881"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Erro de parada do Windows-exceção de thread do sistema 0x0000007E não tratada

Este artigo fornece etapas para resolver problemas em que o sistema operacional convidado encontra um problema e deseja reiniciar a VM do Azure. A mensagem irá declarar que "uma exceção de thread do sistema não foi tratada".

## <a name="symptoms"></a>Sintomas

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe janelas que precisam ser reiniciadas com a exceção de thread do sistema de código Stop **não tratada** ou o código de erro **0x0000007e**.

![A captura de tela mostra o Windows preso durante a inicialização com a mensagem: "seu PC encontrou um problema e precisa ser reiniciado. Vamos reiniciar para você. " Código de parada: "exceção de THREAD do sistema não tratada"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Causa

A causa não pode ser determinada até que um arquivo de despejo de memória seja analisado. Continue a coletar o arquivo de despejo de memória.

## <a name="solution"></a>Solução

### <a name="collect-the-memory-dump-file"></a>Coletar o arquivo de despejo de memória

Para resolver esse problema, primeiro você precisa coletar o arquivo de despejo de memória para a falha e, em seguida, contatar o suporte com o arquivo de despejo de memória. Para coletar o arquivo de despejo, siga estas etapas:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Anexar o disco do sistema operacional a uma nova VM de reparo

1. Siga [as etapas 1-3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
2. Conecte-se à VM de reparo usando Conexão de Área de Trabalho Remota.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localizar o arquivo de despejo e enviar um tíquete de suporte

1. Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra do driver atribuída ao disco do sistema operacional anexado for rotulada como *F* , você precisará ir para `F:\Windows` .
2. Localize o arquivo **Memory. dmp** e [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.
3. Se você estiver tendo problemas para localizar o arquivo **Memory. dmp** , siga o guia para [gerar um arquivo de despejo de memória usando chamadas de NMI (interrupção não mascaráveis)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Para obter mais informações sobre chamadas NMI, consulte as [chamadas de NMI (interrupção não mascarada) no guia do usuário do console serial](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Solucionar erros de inicialização da máquina virtual do Azure](./boot-error-troubleshoot.md)