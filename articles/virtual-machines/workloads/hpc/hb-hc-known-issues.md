---
title: Problemas conhecidos com VMs série HB e HC - Azure Virtual Machines | Microsoft Docs
description: Conheça problemas conhecidos com tamanhos de VM da série HB no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707789"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problemas comuns com VMs série HB e série HC

Este artigo fornece os problemas e soluções mais comuns ao usar VMs série HB e SÉRIE HC.

## <a name="dram-on-hb-series"></a>DRAM na série HB

As VMs da série HB só podem expor 228 GB de RAM a VMs convidados neste momento. Isso se deve a uma limitação conhecida do hipervisor Azure para evitar que páginas sejam atribuídas à DRAM local dos domínios NUMA (domínios NUMA) da AMD CCX reservadas para o VM convidado.

## <a name="accelerated-networking"></a>Rede Acelerada

O Azure Accelerated Networking não está habilitado neste momento, mas irá à medida que avançamos durante o período de visualização. Notificaremos os clientes quando esse recurso for suportado.

## <a name="qp0-access-restriction"></a>restrição de acesso qp0

Para evitar acesso de hardware de baixo nível que pode resultar em vulnerabilidades de segurança, o Queue Pair 0 não está acessível às VMs convidadas. Isso só deve afetar ações tipicamente associadas à administração da NIC ConnectX-5 e executar alguns diagnósticos da InfiniBand, como o ibdiagnet, mas não os aplicativos do usuário final em si.

## <a name="ud-transport"></a>Transporte ud

No lançamento, as séries HB e HC não suportam transporte dinamicamente conectado (DCT). O suporte ao DCT será implementado ao longo do tempo. Os transportes de Conexão Confiável (RC) e Datagram (UD) não confiável são suportados.

## <a name="gss-proxy"></a>GSS Proxy

O GSS Proxy tem um bug conhecido no CentOS/RHEL 7.5 que pode se manifestar como uma penalidade significativa de desempenho e responsividade quando usado com NFS. Isso pode ser mitigado com:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Limpeza de cache

Em sistemas HPC, muitas vezes é útil limpar a memória depois que um trabalho é concluído antes que o próximo usuário seja atribuído ao mesmo nó. Depois de executar aplicativos no Linux, você pode descobrir que sua memória disponível reduz enquanto sua memória buffer aumenta, apesar de não executar nenhum aplicativo.

![Captura de tela do prompt de comando](./media/known-issues/cache-cleaning-1.png)

O `numactl -H` uso mostrará quais NUMAnode(s) a memória está tamponada com (possivelmente todas). No Linux, os usuários podem limpar os caches de três maneiras para devolver a memória tampão ou armazenada em cache para 'gratuito'. Você precisa ser raiz ou ter permissões de sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Captura de tela do prompt de comando](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avisos do kernel

Você pode ver as seguintes mensagens de aviso do kernel ao inicializar uma VM da série HB no Linux.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Você pode ignorar este aviso. Isso se deve a uma limitação conhecida do hipervisor Azure que será abordada ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [computação de alto desempenho](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
