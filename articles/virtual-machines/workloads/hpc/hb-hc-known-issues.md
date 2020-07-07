---
title: Problemas conhecidos com as VMs da série HB e HC-máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre problemas conhecidos com tamanhos de VM da série HB no Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707789"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problemas comuns com VMs série HB e série HC

Este artigo fornece os problemas e soluções mais comuns ao usar as VMs da série HB e HC.

## <a name="dram-on-hb-series"></a>DRAM na série HB

As VMs da série HB podem expor apenas 228 GB de RAM para VMs convidadas no momento. Isso ocorre devido a uma limitação conhecida do hipervisor do Azure para impedir que as páginas sejam atribuídas ao DRAM local do AMD CCX (domínios NUMA) reservado para a VM convidada.

## <a name="accelerated-networking"></a>Rede Acelerada

A rede acelerada do Azure não está habilitada no momento, mas ocorrerá à medida que progredirmos pelo período de visualização. Notificaremos os clientes quando esse recurso for suportado.

## <a name="qp0-access-restriction"></a>Restrição de acesso qp0

Para evitar o acesso de hardware de baixo nível que pode resultar em vulnerabilidades de segurança, o par de filas 0 não é acessível para VMs convidadas. Isso só deve afetar as ações normalmente associadas à administração da NIC ConnectX-5 e a execução de alguns diagnósticos InfiniBand como ibdiagnet, mas não os aplicativos do usuário final.

## <a name="ud-transport"></a>Transporte UD

Na inicialização, a série HB e HC não dão suporte ao transporte conectado dinamicamente (DCT). O suporte para DCT será implementado ao longo do tempo. Há suporte para Transportações de conexão confiável (RC) e datagrama não confiável (UD).

## <a name="gss-proxy"></a>Proxy GSS

O proxy GSS tem um bug conhecido no CentOS/RHEL 7,5 que pode ser manifestado como um desempenho significativo e uma penalidade de capacidade de resposta quando usado com NFS. Isso pode ser reduzido com:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Limpeza de cache

Em sistemas HPC, geralmente é útil limpar a memória após a conclusão de um trabalho antes que o próximo usuário receba o mesmo nó. Depois de executar aplicativos no Linux, você pode descobrir que a memória disponível é reduzida enquanto a memória do buffer aumenta, apesar de não executar nenhum aplicativo.

![Captura de tela do prompt de comando](./media/known-issues/cache-cleaning-1.png)

`numactl -H`O uso do mostrará a quais NUMAnode a memória é armazenada em buffer (possivelmente todos). No Linux, os usuários podem limpar os caches de três maneiras para retornar memória armazenada em buffer ou em cache para ' Free '. Você precisa ser root ou ter permissões sudo.

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

Você pode ignorar este aviso. Isso ocorre devido a uma limitação conhecida do hipervisor do Azure que será abordada ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a computação de alto desempenho](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
