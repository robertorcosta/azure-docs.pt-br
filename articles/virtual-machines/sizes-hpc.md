---
title: Tamanhos Azure VM - HPC | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: b900a95df00ccdd0ad9b5bee3887364195c7d1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226727"
---
# <a name="high-performance-compute-vm-sizes"></a>Tamanhos de VM de computação de alto desempenho

As máquinas virtuais (VMs) da série Azure H foram projetadas para fornecer desempenho de classe de liderança, escalabilidade de MPI e eficiência de custo para uma variedade de cargas de trabalho HPC do mundo real.

[Série HBv2](hbv2-series.md) As VMs possuem 200 Gb/seg Mellanox HDR InfiniBand, enquanto as VMs hb e hc-series possuem 100 Gb/seg Mellanox EDR InfiniBand. Cada um desses tipos de VM está conectado em uma árvore de gordura sem bloqueio para um desempenho RDMA otimizado e consistente. As VMs HBv2 suportam o Roteamento Adaptativo e o Transporte Conectado Dinâmico (DCT, adicional aos transportes padrão RC e UD). Esses recursos melhoram o desempenho, a escalabilidade e a consistência do aplicativo, e o uso deles é fortemente recomendado.

[Série HB](hb-series.md) As VMs são otimizadas para aplicações impulsionadas pela largura de banda de memória, como dinâmica de fluidos, análise explícita de elementos finitos e modelagem meteorológica. Hb VMs possuem 60 núcleos de processador AMD EPYC 7551, 4 GB de RAM por núcleo de CPU e sem hiperthreading. A plataforma AMD EPYC fornece mais de 260 GB/seg de largura de banda de memória.

[Série HC](hc-series.md) As VMs são otimizadas para aplicações impulsionadas pela computação densa, como análise implícita de elementos finitos, dinâmica molecular e química computacional. Os VMs do HC possuem 44 núcleos de processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo de CPU e sem hiperthreading. A plataforma Intel Xeon Platinum suporta o rico ecossistema de ferramentas de software da Intel, como a Intel Math Kernel Library.

[Série H](h-series.md) As VMs são otimizadas para aplicativos impulsionados por altas frequências de CPU ou grandes requisitos de memória por núcleo. As VMs da série H possuem 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, 7 ou 14 GB de RAM por núcleo de CPU e sem hiperthreading. A série H possui 56 Gb/seg Mellanox FDR InfiniBand em uma configuração de árvore de gordura sem bloqueio para um desempenho RDMA consistente. As VMs da série H suportam Intel MPI 5.x e MS-MPI.

## <a name="deployment-considerations"></a>Considerações de implantação

- **Assinatura do Azure** – Para implantar um número maior de instâncias de computação intensiva, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

- **Preço e disponibilidade** – esses tamanhos de VM são oferecidos apenas no tipo de preço Standard. Confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade nas regiões do Azure.
- **Cota de núcleos** – Talvez seja preciso aumentar a cota de núcleos em sua assinatura do Azure, saindo do valor padrão. Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho de VM, incluindo a série de H. Para solicitar um aumento de cota, [abra uma solicitação de suporte ao cliente on-line](../azure-supportability/how-to-create-azure-support-request.md) gratuitamente. (Os limites padrão podem variar dependendo de sua categoria de assinatura.)

  > [!NOTE]
  > Entre em contato com o Suporte do Azure se precisar de capacidade em larga escala. Cotas do Azure são limites de crédito, não garantias de capacidade. Independentemente de sua cota, você é cobrado apenas pelo núcleos utilizados.
  
- **Rede virtual** – Não é necessário ter uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) do Azure para usar instâncias de computação intensiva. No entanto, para muitas implantações, é necessária pelo menos uma rede virtual do Azure baseada em nuvem ou uma conexão site a site se você precisar acessar recursos locais. Quando necessário, você precisará criar uma nova rede virtual para implantar as instâncias. Não há suporte para a adição de VMs de computação intensiva a uma rede virtual em um grupo de afinidades.
- **Redimensionamento** – devido ao hardware especializado, você só pode redimensionar instâncias de computação intensiva dentro da mesma família de tamanho (série H ou série A de computação intensiva). Por exemplo, somente é possível redimensionar uma VM da série H de um tamanho da série H para outro. Além disso, não há suporte para o redimensionamento de um tamanho sem computação intensiva para um tamanho de computação intensiva.  

> [!NOTE]
> As VMs A8 – A11 estão previstas para a aposentadoria em 3/2021. Para obter mais informações, consulte [o Guia de Migração do HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instâncias compatíveis com RDMA

Um subconjunto das instâncias comuso intensivo de computação (A8, A9, H16r, H16mr, HB e HC) possui uma interface de rede para conectividade RDMA (Remote Direct Memory Access, acesso à memória direta) remota. Os tamanhos selecionados da série N designados com 'r' como as configurações NC24rs (NC24rs_v2 e NC24rs_v3) também são capazes de RDMA. Essa interface é além do adaptador de rede do Azure padrão disponível para outros tamanhos de VM.

Essa interface permite que as instâncias capazes de RDMA se comuniquem por uma rede InfiniBand (IB), operando a taxas de EDR para HB, HC, taxas de FDR para máquinas virtuais n16r, H16mr e RDMA, e taxas de QDR para máquinas virtuais A8 e A9. Esses recursos RDMA podem melhorar a escalabilidade e o desempenho de determinados aplicativos MPI (Interface de Transmissão de Mensagens). Para obter mais informações sobre velocidade, consulte os detalhes nas tabelas desta página.

> [!NOTE]
> No Azure, o IP sobre o IB só é suportado nos VMs habilitados para SR-IOV (SR-IOV para InfiniBand, atualmente HB e HC). O RDMA sobre o IB é suportado para todas as instâncias capazes de RDMA.
- **Sistema operacional** - Windows Server 2016 em todas as VMs da série HPC acima. Windows Server 2012 R2, Windows Server 2012 também são suportados nas VMs habilitadas para NÃO-SR-IOV (excluindo HB e HC).

- **MPI** - Os tamanhos de VM habilitados para SR-IOV no Azure (HB, HC) permitem que quase qualquer sabor de MPI seja usado com Mellanox OFED.
Em VMs habilitados para NÃO-SR-IOV, implementações MPI suportadas usam a interface Microsoft Network Direct (ND) para se comunicar entre instâncias. Assim, apenas as versões Microsoft MPI (MS-MPI) 2012 R2 ou posterior e Intel MPI 5.x são suportadas. Versões posteriores (2017, 2018) da biblioteca de tempo de execução Intel MPI podem ou não ser compatíveis com os drivers RDMA do Azure.

- **Extensão InfiniBandDriverWindows VM** - Em VMs com capacidade para RDMA, adicione a extensão InfiniBandDriverWindows para ativar o InfiniBand. Esta extensão do Windows VM instala drivers Windows Network Direct (em VMs não-SR-IOV) ou drivers Mellanox OFED (em VMs SR-IOV) para conectividade RDMA.
Em certas implantações de instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente. Observe que a extensão VM hpcVmDrivers está sendo depreciada; ele não será atualizado. Para adicionar a extensão de VM a uma VM, use cmdlets do [Azure PowerShell](/powershell/azure/overview). 

  O comando a seguir instala a última versão 1.0 InfiniBandDriverWindows em um VM capaz de RDMA existente chamado *myVM* implantado no grupo de recursos chamado *myResourceGroup* na região *oeste dos EUA:*

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Alternativamente, as extensões VM podem ser incluídas nos modelos do Azure Resource Manager para facilitar a implantação, com o seguinte elemento JSON:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  O comando a seguir instala a última versão 1.0 InfiniBandDriverWindows em todas as VMs com capacidade para RDMA em um conjunto de escala de máquina virtual existente chamado *myVMSS* implantado no grupo de recursos chamado *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Para obter mais informações, consulte [Recursos e extensões da máquina virtual](./extensions/overview.md). Também é possível trabalhar com extensões de VMs implantadas no [modelo de implantação clássico](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Espaço de endereço de rede RDMA** - A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicativos MPI em instâncias implantadas em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.

## <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters de VMs do Windows HPC que podem se comunicar usando a rede RDMA, incluindo: 

- **Máquinas virtuais** - implante as VMs HPC compatíveis com RDMA no mesmo conjunto de disponibilidade (ao usar o modelo de implantação do Azure Resource Manager). Se você usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem. 

- **Conjuntos de escala de máquinavirtual** - Em um conjunto de escala de máquina virtual, certifique-se de limitar a implantação a um único grupo de colocação. Por exemplo, em um modelo do Resource Manager, defina a `singlePlacementGroup`propriedade como`true`. 

- **MPI entre máquinas virtuais** - Se a comunicação MPI for necessária entre máquinas virtuais (VMs), certifique-se de que as VMs estão no mesmo conjunto de disponibilidade ou no conjunto de mesma escala da máquina virtual.

- **Azure CycleCloud** - Crie um cluster HPC em [Azure CycleCloud](/azure/cyclecloud/) para executar tarefas MPI em nós do Windows.

- **Azure Batch** - Crie um pool do [Azure Batch](/azure/batch/) para executar cargas de trabalho MPI em nós de computação do Windows Server. Para obter mais informações, consulte [Usar instâncias habilitadas para RDMA ou habilitadas para GPU em Conjuntos de lotes](../batch/batch-pool-compute-intensive-sizes.md). Consulte também o [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseados em contêiner no lote.

- **O Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) inclui um ambiente de tempo de execução para o MS-MPI que usa a rede Azure RDMA quando implantado em VMs Linux com capacidade RDMA. Por exemplo, implantações, consulte [Configurar um cluster RDMA Linux com o HPC Pack para executar aplicativos MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Otimizado para computação](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Para que as listas de verificação usem as instâncias com uso intensivo de computação com o HPC Pack no Windows Server, consulte [Configurar um cluster RDMA Linux com o HPC Pack para executar aplicativos MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

- Para usar instâncias de computação intensiva para executar aplicativos MPI com o Lote do Azure, consulte [Usar tarefas de várias instâncias para executar aplicativos MPI (Interface de Transmissão de Mensagens) no Lote do Azure](../batch/batch-mpi.md).

- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.