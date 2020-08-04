---
title: Habilitar InifinBand em VMs HPC – máquinas virtuais do Azure | Microsoft Docs
description: Saiba como habilitar o InfiniBand em VMs do Azure HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 88f1c120ac4578e077e1c51f59bcaf53b1de2083
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87538890"
---
# <a name="enable-infiniband"></a>Habilitar InfiniBand

As VMs da série [a](../../sizes-hpc.md) e da série [N](../../sizes-gpu.md) [compatíveis com RDMA](../../sizes-hpc.md#rdma-capable-instances) se comunicam pela rede InfiniBand de baixa latência e alta largura de banda. O recurso RDMA em tal interconexão é essencial para aumentar a escalabilidade e o desempenho de cargas de trabalho do HPC e do ia do nó distribuído. As VMs da série H e da série N habilitadas para InfiniBand estão conectadas em uma árvore Fat sem bloqueio com um design de pouco diâmetro para o desempenho de RDMA otimizado e consistente.

Há várias maneiras de habilitar o InfiniBand nos tamanhos de VM compatíveis.

## <a name="vm-images-with-infiniband-drivers"></a>Imagens de VM com drivers InfiniBand
Confira [imagens de VM](configure.md#vm-images) para obter uma lista de imagens de VM com suporte no Marketplace, que são pré-carregadas com drivers InfiniBand (para VMs Sr-IOV ou não Sr-IOV) ou podem ser configuradas com os drivers apropriados.
Para [VMs compatíveis](../../sizes-hpc.md#rdma-capable-instances)com o RDMA de Sr-IOV, a [versão 7,6 do CentOS-HPC ou uma versão posterior](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) de imagens de VM no Marketplace são a maneira mais fácil de começar.
As imagens de VM Ubuntu podem ser configuradas com os drivers corretos para as VMs de SR-IOV e não habilitadas para SR-IOV usando as [instruções aqui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).

## <a name="infiniband-driver-vm-extensions"></a>Extensões de VM do driver InfiniBand
No Linux, a [extensão de VM InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) pode ser usada para instalar os drivers Mellanox ofed e habilitar o InfiniBand nas VMs das séries H e N habilitadas para Sr-iov.

No Windows, a [extensão de VM InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) instala drivers diretos de rede do Windows (em VMs que não são de Sr-IOV) ou drivers Mellanox ofed (em VMs Sr-IOV) para conectividade RDMA. Em determinadas implantações de instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente. Observe que a extensão de VM HpcVmDrivers está sendo preterida; Ele não será atualizado.

Para adicionar a extensão de VM a uma VM, use cmdlets do [Azure PowerShell](/powershell/azure/). Para obter mais informações, consulte [Recursos e extensões da máquina virtual](../../extensions/overview.md). Também é possível trabalhar com extensões de VMs implantadas no [modelo de implantação clássico](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Instalação manual
Os [drivers Mellanox OpenFabrics (ofed)](https://www.mellanox.com/products/InfiniBand-VPI-Software) podem ser instalados manualmente nas VMs das séries [H](../../sizes-hpc.md) e série [N](../../sizes-gpu.md) [habilitadas para Sr-IOV](../../sizes-hpc.md#rdma-capable-instances) .

### <a name="linux"></a>Linux
Os [drivers do ofed para Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) podem ser instalados com o exemplo abaixo. Embora o exemplo aqui seja para RHEL/CentOS, mas as etapas são gerais e podem ser usadas para qualquer sistema operacional Linux compatível, como Ubuntu (16, 4, 18, 4 19, 4, 20, 4) e SLES (12 SP4 e 15). Os drivers de caixa de entrada também funcionam, mas os drivers Mellanox OFED fornecem mais recursos.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optinally verify checksum
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Para o Windows, baixe e instale os [drivers MELLANOX ofed for Windows](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>Habilitar IP sobre InfiniBand (IB)
Se você estiver planejando executar trabalhos MPI, normalmente não precisará de IPoIB. A biblioteca MPI usará a interface de verbos para comunicação de IB (a menos que você use explicitamente o canal TCP/IP da biblioteca MPI). Mas se você tiver um aplicativo que usa TCP/IP para comunicação e quiser executar sobre o IB, você poderá usar o IPoIB sobre a interface IB. Use os comandos a seguir (para RHEL/CentOS) para habilitar o IP sobre InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como instalar várias [bibliotecas MPI com suporte](setup-mpi.md) e sua configuração ideal nas VMs.
- Examine as visão geral da série [HB](hb-series-overview.md) e [HC-Series](hc-series-overview.md) para saber mais sobre como configurar de forma ideal as cargas de trabalho para desempenho e escalabilidade.
- Leia sobre os comunicados mais recentes e alguns exemplos e resultados do HPC nos [Blogs da comunidade de computação técnica do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma exibição de arquitetura de nível superior da execução de cargas de trabalho do HPC, consulte [computação de alto desempenho (HPC) no Azure](/azure/architecture/topics/high-performance-computing/).
