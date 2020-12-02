---
title: Problemas com um disco do sistema operacional completo em uma máquina virtual Linux
description: Como resolver problemas com um disco do sistema operacional completo em uma máquina virtual Linux
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523378"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Problemas com um disco do sistema operacional completo em uma máquina virtual Linux

Quando o disco do sistema operacional de uma VM (máquina virtual) Linux fica cheio, isso pode causar problemas com a operação apropriada da VM.

## <a name="symptom"></a>Sintoma

Ao tentar criar um novo arquivo, você recebe esta mensagem:

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Em seguida, vários daemons indicam que não são capazes de criar arquivos temporários durante a sessão de inicialização.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Causa

Há várias razões pelas quais essa mensagem de erro pode ocorrer:

1. O disco pode estar cheio.
1. O sistema de arquivos pode estar sem iNodes.
1. Um disco de dados pode ser montado em um diretório existente, fazendo com que os arquivos sejam ocultados.
1. Arquivos que são abertos por um processo e, em seguida, excluídos.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e acessar uma VM de reparo.
1. Liberar espaço em disco.
1. Recompilar a VM.

> [!NOTE]
> Se encontrar esse erro, o SO convidado não está funcionando. Solucione esse problema no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1 a 3 dos comandos de reparo da VM](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
1. Usando o SSH, conecte-se à VM de reparo.

### <a name="free-up-space-on-the-disk"></a>Liberar espaço no disco

Como resolver o problema:

- Redimensione o disco até 1 TB se ele ainda não estiver no tamanho máximo de 1 TB.
- Libere espaço em disco.

1. Verifique se o disco está cheio. Se o tamanho do disco estiver abaixo de 1 TB, expanda-o para um máximo de 1 TB [usando CLI do Azure](../linux/expand-disks.md).
1. Se o disco já for de 1 TB, será necessário liberar espaço em disco.
1. Depois que o redimensionamento e a limpeza forem concluídos, continue a recompilar a VM.

### <a name="rebuild-the-vm"></a>Recompilar a VM

Use a [etapa 5 dos comandos de Reparo da VM](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para recompilar a VM.
