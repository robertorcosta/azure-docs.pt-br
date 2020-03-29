---
title: Computação de Alto Desempenho - Máquinas Virtuais Azure | Microsoft Docs
description: Saiba mais sobre computação de alto desempenho no Azure.
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
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707816"
---
# <a name="optimization-for-linux"></a>Otimização para Linux

Este artigo mostra algumas técnicas-chave para otimizar sua imagem do sistema operacional. Saiba mais sobre [como ativar a InfiniBand](enable-infiniband.md) e otimizar as imagens do Sistema Operacional.

## <a name="update-lis"></a>Atualizar LIS

Se implantar usando uma imagem personalizada (por exemplo, um sistema operacional mais antigo como OCentOS/RHEL 7.4 ou 7.5), atualize lis na VM.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Recuperar a memória

Melhore a eficiência recuperando automaticamente a memória para evitar o acesso remoto à memória.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Para fazer isso persistir após as reinicializações da VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Desativar firewall e SELinux

Desativar firewall e SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Desativar a potência da cpu

Desabilite a potência da CPU.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [como ativar a InfiniBand](enable-infiniband.md) e otimizar imagens do Sistema Operacional.

* Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
