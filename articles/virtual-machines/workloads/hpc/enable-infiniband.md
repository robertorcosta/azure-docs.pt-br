---
title: Habilitar inifinBand com SR-IOV - Azure Virtual Machines | Microsoft Docs
description: Saiba como ativar o InfiniBand com o SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196776"
---
# <a name="enable-infiniband-with-sr-iov"></a>Habilitar infiniBand com SR-IOV

As séries Azure NC, ND e H das VMs são todas apoiadas por uma rede InfiniBand dedicada. Todos os tamanhos habilitados para RDMA são capazes de aproveitar essa rede usando o Intel MPI. Algumas séries de VM expandiram o suporte para todas as implementações de MPI e verbos RDMA através do SR-IOV. VMs capazes de RDMA incluem VMs [otimizadas para GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) e [HPC (High-Performance Compute).](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)

## <a name="choose-your-installation-path"></a>Escolha seu caminho de instalação

Para começar, a opção mais simples é usar uma imagem de plataforma pré-configurada para InfiniBand, quando disponível:

- **HPC IaaS VMs** – Para começar com VMs IaaS para HPC, a solução mais simples é usar a [imagem do Sistema Operacional CentOS-HPC 7.6 VM](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), que já está configurada com a InfiniBand. Como essa imagem já está configurada com o InfiniBand, você não precisa configurá-la manualmente. Para versões compatíveis do Windows, consulte [instâncias compatíveis com Windows RDMA](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **VMs GPU IaaS** – Nenhuma imagem da plataforma está atualmente pré-configurada para VMs otimizadas para GPU, exceto para [a imagem do Sistema Operacional CentOS-HPC 7.6 VM](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Para configurar uma imagem personalizada com o InfiniBand, consulte [Instale manualmente O Mellanox OFED](#manually-install-mellanox-ofed).

Se você estiver usando uma imagem VM personalizada ou uma VM [otimizada para GPU,](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) você deve configurá-la com a InfiniBand adicionando a extensão InfiniBandDriverLinux ou InfiniBandDriverWindows VM à sua implantação. Aprenda a usar essas extensões de VM com [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Instale manualmente o Mellanox OFED

Para configurar manualmente o InfiniBand com o SR-IOV, use as seguintes etapas. O exemplo nessas etapas mostra sintaxe para RHEL/CentOS, mas as etapas são gerais e podem ser usadas para qualquer sistema operacional compatível, como o Ubuntu (16.04, 18.04 19.04) e o SLES (12 SP4 e 15). Os drivers de caixa de entrada também funcionam, mas os drivers Mellanox OpenFabrics fornecem mais recursos.

Para obter mais informações sobre as distribuições suportadas para o driver Mellanox, consulte os mais recentes [drivers Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26). Para obter mais informações sobre o driver Mellanox OpenFabrics, consulte o [guia do usuário Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Veja o exemplo a seguir de como configurar o InfiniBand no Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Para Windows, baixe e instale o [Mellanox OFED para drivers Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Ativar IP sobre InfiniBand

Use os seguintes comandos para ativar o IP sobre a InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
