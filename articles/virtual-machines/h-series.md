---
title: Série H-máquinas virtuais do Azure
description: Especificações para as VMs da série H.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: a71b7b7de6f6039106b43576847675f48de803c8
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088066"
---
# <a name="h-series"></a>Série H

As VMs da série H são otimizadas para aplicativos orientados por altas frequências de CPU ou grandes requisitos de memória por núcleo. As VMs da série H apresentam 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, até 14 GB de RAM por núcleo de CPU e sem hyperthreading. A série H apresenta 56 GB/s Mellanox FDR InfiniBand em uma configuração de árvore de Fat sem bloqueio para desempenho consistente de RDMA. As VMs da série H dão suporte ao Intel MPI 5. x e ao MS-MPI.

ACU: 290-300

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

Migração ao Vivo: sem suporte

Atualizações de preservação de memória: sem suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GB) | Discos de dados máximos | Máximo de NICs Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> para aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Imagens de so com suporte (Linux)
 
O Azure Marketplace tem muitas distribuições do Linux que dão suporte à conectividade RDMA:
  
* **HPC baseado em CentOS** – para VMs não habilitadas para Sr-IOV, versão 6,5 do HPC baseada em CentOS ou uma versão posterior, até 7,5 são adequadas. Para VMs da série H, as versões 7,1 a 7,5 são recomendadas. OS drivers RDMA e o Intel MPI 5.1 são instalados na VM.
  Para VMs SR-IOV, o CentOS-HPC 7,6 vem otimizado e pré-carregado com os drivers RDMA e vários pacotes MPI instalados.
  Para outras imagens de VM RHEL/CentOS, adicione a extensão InfiniBandLinux para habilitar a InfiniBand. Esta extensão de VM do Linux instala drivers Mellanox OFED (em VMs SR-IOV) para conectividade RDMA. O cmdlet do PowerShell a seguir instala a versão mais recente (versão 1,0) da extensão InfiniBandDriverLinux em uma VM compatível com RDMA existente. A VM compatível com RDMA é denominada *myVM* e é implantada no grupo de recursos chamado *MyResource* Group na região *oeste dos EUA* da seguinte maneira:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Como alternativa, as extensões de VM podem ser incluídas em modelos de Azure Resource Manager para facilitar a implantação com o seguinte elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  O comando a seguir instala a extensão mais recente da versão 1,0 do InfiniBandDriverLinux em todas as VMs compatíveis com RDMA em um conjunto de dimensionamento de máquinas virtuais existente chamado *myVMSS* implantado no grupo de recursos chamado *MyResource*Group:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Nas imagens do HPC baseado em CentOS, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers RDMA do Linux são distribuídos como um pacote RPM, e as atualizações de driver podem não funcionar se o kernel for atualizado.
  >
  

* **SuSE Linux Enterprise Server** -SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15. Os drivers RDMA são instalados e os pacotes do Intel MPI são distribuídos na VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -ubuntu Server 16, 4 LTS, 18, 4 LTS. Configure os drivers RDMA na VM e registre-se na Intel para baixar o Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Para obter mais detalhes sobre como habilitar o InfiniBand, configurando o MPI, consulte [habilitar InfiniBand](/workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
