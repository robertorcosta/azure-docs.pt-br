---
title: Tamanhos de VM Linux do Azure-HPC
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho Linux no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 214ab48b6b0dca37eff3b3f155aaa92afc7fee16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034886"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tamanhos de máquina virtual de computação de alto desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Os tamanhos de VM habilitados para SR-IOV no Azure permitem que quase qualquer tipo de MPI seja usado.
Em VMs não habilitadas para SR-IOV, somente as versões Intel MPI 5. x têm suporte. Versões posteriores (2017, 2018) da biblioteca de tempo de execução do Intel MPI podem ou não ser compatíveis com os drivers RDMA do Linux do Azure.


### <a name="supported-os-images"></a>Imagens do sistema operacional com suporte
 
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
  
  O comando a seguir instala a extensão mais recente da versão 1,0 do InfiniBandDriverLinux em todas as VMs compatíveis com RDMA em um conjunto de dimensionamento de VM existente chamado *myVMSS* implantado no grupo de recursos chamado *MyResource*Group:
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Para obter mais detalhes sobre como habilitar o InfiniBand, configurando o MPI, consulte [habilitar InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters de VMs de HPC do Linux que podem se comunicar usando a rede RDMA, incluindo: 

* **Máquinas virtuais** - implante as VMs HPC compatíveis com RDMA no mesmo conjunto de disponibilidade (ao usar o modelo de implantação do Azure Resource Manager). Se você usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem. 

* **Conjuntos de dimensionamento de máquinas virtuais** – em um conjunto de dimensionamento de máquinas virtuais, certifique-se de limitar a implantação a um único grupo de posicionamento. Por exemplo, em um modelo do Resource Manager, defina a `singlePlacementGroup`propriedade como`true`. 

* **MPI entre máquinas virtuais** – se a comunicação MPI for necessária entre máquinas virtuais (VMS), verifique se as VMs estão no mesmo conjunto de disponibilidade ou na mesma configuração de conjunto de dimensionamento da máquina virtual.

* **Azure CycleCloud** - Crie um cluster HPC em [Azure CycleCloud](/azure/cyclecloud/) para executar tarefas MPI em nós do Linux.

* **Azure Batch** - Crie um pool do [Azure Batch](/azure/batch/) para executar cargas de trabalho MPI em nós de computação do Linux. Para obter mais informações, consulte [Usar instâncias habilitadas para RDMA ou habilitadas para GPU em Conjuntos de lotes](../../batch/batch-pool-compute-intensive-sizes.md). Consulte também o [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseados em contêiner no lote.

* **Pacote Microsoft HPC** - [Pacote HPC](https://docs.microsoft.com/powershell/high-performance-computing/overview) dá suporte a várias distribuições do Linux para execução em nós de computação implantados nas VMs do Azure compatíveis com RDMA, gerenciados por um nó principal do Windows Server. Para obter uma implementação de exemplo, consulte [Criar cluster RDMA do HPC Pack Linux no Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Considerações sobre rede
* Em VMs Linux não-IOV, habilitadas para RDMA no Azure, o eth1 é reservado para o tráfego de rede RDMA. Não altere nenhuma configuração de eth1 ou nenhuma informação no arquivo de configuração referente a essa rede.
* Em VMs habilitadas para SR-IOV (HB e HC-Series), ib0 é reservado para tráfego de rede RDMA.
* A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicativos MPI em instâncias implantadas em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.
* Dependendo da sua opção de ferramenta de gerenciamento de cluster, pode ser necessária uma configuração adicional do sistema para executar tarefas MPI. Por exemplo, em um cluster de VMs, talvez seja necessário estabelecer confiança entre os nós de cluster gerando chaves SSH ou estabelecendo logons SSH com senha.


## <a name="other-sizes"></a>Outros tamanhos
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como configurar, otimizar e dimensionar [cargas de trabalho do HPC](../workloads/hpc/configure.md) no Azure.
- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
