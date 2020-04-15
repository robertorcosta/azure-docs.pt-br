---
title: Série H - Azure Virtual Machines
description: Especificações para as VMs da série H.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: 6e0257f8a32d05f49dd67195f22d387319358ef2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314140"
---
# <a name="h-series"></a>Série H

As VMs da série H são otimizadas para aplicativos impulsionados por altas frequências de CPU ou grandes requisitos de memória por núcleo. As VMs da série H possuem 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, até 14 GB de RAM por núcleo de CPU e sem hiperthreading. A série H possui 56 Gb/seg Mellanox FDR InfiniBand em uma configuração de árvore de gordura sem bloqueio para um desempenho RDMA consistente. As VMs da série H suportam Intel MPI 5.x e MS-MPI.

ACU: 290-300

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Freqüência da CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho RDMA (Gb/s) | Suporte ao MPI | Armazenamento temporário (GB) | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | Max Ethernet NICs |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> Para aplicativos MPI, a rede de backend RDMA dedicada é habilitada pela rede FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Imagens de Sistema Operacional suportadas (Linux)
 
O Azure Marketplace tem muitas distribuições Linux que suportam a conectividade RDMA:
  
* **HPC baseado em CentOS** - Para VMs não-SR-IOV habilitados, a versão 6.5 HPC baseada no CentOS ou uma versão posterior, até 7.5 são adequadas. Para VMs da série H, as versões 7.1 a 7.5 são recomendadas. OS drivers RDMA e o Intel MPI 5.1 são instalados na VM.
  Para VMs SR-IOV, o CentOS-HPC 7.6 vem otimizado e pré-carregado com os drivers RDMA e vários pacotes MPI instalados.
  Para outras imagens RHEL/CentOS VM, adicione a extensão InfiniBandLinux para ativar o InfiniBand. Esta extensão Linux VM instala drivers Mellanox OFED (em VMs SR-IOV) para conectividade RDMA. O cmdlet powershell a seguir instala a versão mais recente (versão 1.0) da extensão InfiniBandDriverLinux em um VM compatível com RDMA existente. O VM capaz de RDMA é chamado *de myVM* e é implantado no grupo de recursos chamado *myResourceGroup* na região *oeste dos EUA* da seguinte forma:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternativamente, as extensões VM podem ser incluídas nos modelos do Azure Resource Manager para facilitar a implantação com o seguinte elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  O comando a seguir instala a última versão 1.0 InfiniBandDriverLinux em todas as VMs com capacidade para RDMA em um conjunto de escala de máquina virtual existente chamado *myVMSS* implantado no grupo de recursos chamado *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Nas imagens do HPC baseado em CentOS, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers RDMA do Linux são distribuídos como um pacote RPM, e as atualizações do driver podem não funcionar se o kernel for atualizado.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15. Os drivers RDMA são instalados e os pacotes do Intel MPI são distribuídos na VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Servidor Ubuntu 16.04 LTS, 18.04 LTS. Configure os drivers RDMA na VM e registre-se na Intel para baixar o Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Para obter mais detalhes sobre como ativar o InfiniBand, configurar o MPI, consulte [Ativar InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Otimizado para memória](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
