---
title: A VM não está respondendo enquanto aguarda o Gerenciador de sessão local
description: Este artigo fornece etapas para resolver problemas em que o sistema operacional convidado está preso aguardando o Gerenciador de sessão local concluir o processamento durante a inicialização de uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: f2f0177b5fe8bb97773d297319f6c9196d8178d2
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536227"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>A VM não está respondendo enquanto aguarda o Gerenciador de sessão local

Este artigo fornece etapas para resolver problemas em que o sistema operacional convidado está preso aguardando o Gerenciador de sessão local concluir o processamento durante a inicialização de uma VM (máquina virtual) do Azure.

## <a name="symptoms"></a>Sintomas

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe um prompt com a mensagem: " *aguarde o Gerenciador de sessão local* "

![A captura de tela mostra o sistema operacional convidado preso com a mensagem "Aguarde o Gerenciador de sessão local" no Windows Server 2012 R2.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Causa

Pode haver várias razões pelas quais uma máquina virtual está presa aguardando o Gerenciador de sessão local. Se a espera pelo Gerenciador de sessão local for um problema persistente, você precisará coletar um despejo de memória para análise.

## <a name="solution"></a>Solução

Em alguns casos, simplesmente aguardando tempo suficiente para que o processo seja concluído resolverá o problema. Se sua VM não estiver respondendo e permanecer na tela de espera por mais de uma hora, você deverá coletar um despejo de memória e entrar em contato com o suporte da Microsoft.

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
> [Solucionar erros de inicialização da máquina virtual do Azure](boot-error-troubleshoot.md)