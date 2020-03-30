---
title: Suporte do Azure para VMs de geração 2
description: Visão geral do suporte do Azure para VMs de geração 2
services: virtual-machines-windows
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: ed7d62ef5c1c4837b3eb6d02f301085178fe5e0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528210"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Suporte para VMs de geração 2 no Azure

O suporte para máquinas virtuais de geração 2 (VMs) já está disponível no Azure. Você não pode mudar a geração de uma máquina virtual depois de criá-la, então revise as considerações nesta página antes de escolher uma geração.

As VMs da geração 2 suportam recursos-chave que não são suportados nas VMs da geração 1. Esses recursos incluem memória aumentada, Extensões intel de guarda de software (Intel SGX) e memória persistente virtualizada (vPMEM). As VMs da Geração 2 que estão rodando no local, têm alguns recursos que ainda não são suportados no Azure. Para obter mais informações, consulte a seção [Recursos e recursos.](#features-and-capabilities)

As VMs de geração 2 usam a nova arquitetura de inicialização baseada em UEFI em vez da arquitetura baseada em BIOS usada pela geração 1 de VMs. Em comparação com as VMs de geração 1, as VMs de geração 2 podem ter melhorado os tempos de inicialização e instalação. Para uma visão geral da geração 2 vms e algumas das diferenças entre a geração 1 e a geração 2, [veja Devo criar uma máquina virtual de geração 1 ou 2 em Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>Tamanhos de VM da geração 2

As VMs de geração 1 são suportadas por todos os tamanhos de VM no Azure (exceto para VMs da série Mv2). O Azure agora oferece suporte à geração 2 para as seguintes séries selecionadas da VM:

* [Série B](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Série DC](../dcv2-series.md)
* [Série DSv2](../dv2-dsv2-series.md) e [Série DSv3](../dv3-dsv3-series.md)
* [Série Esv3](../ev3-esv3-series.md)
* [Série Fsv2](../fsv2-series.md)
* [Série GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Série HB](../hb-series.md)
* [Série HC](../hc-series.md)
* [Série SL](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) e [Série Lsv2](../lsv2-series.md)
* [Série Mv2](../mv2-series.md)
* [Série Sncv2](../ncv2-series.md) e [Série NCv3](../ncv3-series.md)
* [Série ND](../nd-series.md)
* [Série NVv3](../nvv3-series.md)

> [!NOTE]
> O uso de imagens vm de geração 2 para VMs da série Mv2 está geralmente disponível, uma vez que a série Mv2 trabalha exclusivamente com imagens vm de geração 2. As imagens da Geração 1 VM não são suportadas em VMs da série MV2. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Imagens da Geração 2 da VM no Azure Marketplace

As VMs da geração 2 suportam as seguintes imagens do Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Servidor Ubuntu 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>No local vs. Azure geração 2 VMs

Atualmente, o Azure não suporta alguns dos recursos que o Hyper-V suporta no local para VMs de geração 2.

| Recurso da Geração 2                | Hyper-V local | Azure |
|-------------------------------------|---------------------|-------|
| Inicialização segura                         | :heavy_check_mark:  | :x:   |
| VM blindada                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| VBS (Virtualization-based security, segurança baseada em virtualização) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Recursos e funcionalidades

### <a name="generation-1-vs-generation-2-features"></a>Características da Geração 1 vs. Geração 2

| Recurso | Geração 1 | 2ª geração |
|---------|--------------|--------------|
| Inicialização             | PCAT                      | UEFI                               |
| Controladores de disco | IDE                       | SCSI                               |
| Tamanhos de VM         | Todos os tamanhos de VM | Apenas VMs que suportam armazenamento premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Capacidades da Geração 1 vs. Geração 2

| Recurso | Geração 1 | 2ª geração |
|------------|--------------|--------------|
| Disco do SISTEMA > 2 TB                    | :x:                | :heavy_check_mark: |
| Sistema operacional de disco/imagem/swap personalizado         | :heavy_check_mark: | :heavy_check_mark: |
| Suporte ao conjunto de escala de máquina virtual | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Backup/restauração                    | :heavy_check_mark: | :heavy_check_mark: |
| Galeria de imagens compartilhadas              | :heavy_check_mark: | :heavy_check_mark: |
| Criptografia de disco do Azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Criando uma vm de geração 2

### <a name="marketplace-image"></a>Imagem de mercado

No portal Azure ou Azure CLI, você pode criar VMs de geração 2 a partir de uma imagem do Marketplace que suporta inicialização uefi.

#### <a name="azure-portal"></a>Portal do Azure

Abaixo estão os passos para criar uma VM de geração 2 (Gen2) no portal Azure.

1. Entre no Portal do Azure em https://portal.azure.com.
1. Selecione **Criar um recurso**.
1. Clique **em Ver tudo** do Azure Marketplace à esquerda.
1. Selecione uma imagem que suporte o Gen2.
1. Clique em **Criar**.
1. Na guia **Avançado,** na seção **de geração VM,** selecione a opção **Gen 2.**
1. Na guia **Basics,** em Caso de **Detalhes,** vá para **Tamanho** e abra a **lâmina de tamanho DeSm.**
1. Selecione uma [VM de geração 2 suportada](#generation-2-vm-sizes).
1. Passe pelo fluxo de criação do [portal Azure](quick-create-portal.md) para terminar a criação da VM.

![Selecione Gen 1 ou Gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Você também pode usar o PowerShell para criar uma VM fazendo referência direta à geração 1 ou geração 2 SKU.

Por exemplo, use o seguinte cmdlet PowerShell para obter `WindowsServer` uma lista das SKUs na oferta.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Alternativamente, você pode usar o Azure CLI para ver quaisquer imagens disponíveis da geração 2, listadas pelo **Publisher**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Se você estiver criando uma VM com o Windows Server 2012 como o SO, então você selecionará o VM VM da geração 1 (BIOS) ou a geração 2 (UEFI), que se parece com isso:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Consulte a seção [Recursos e recursos](#features-and-capabilities) para obter uma lista atual de imagens de Marketplace suportadas.

### <a name="managed-image-or-managed-disk"></a>Imagem gerenciada ou disco gerenciado

Você pode criar uma VM de geração 2 a partir de uma imagem gerenciada ou disco gerenciado da mesma forma que você criaria uma geração 1 VM.

### <a name="virtual-machine-scale-sets"></a>conjuntos de escala de máquina virtual

Você também pode criar VMs de geração 2 usando conjuntos de escala de máquinas virtuais. Na CLI do Azure, use conjuntos de escala do Azure para criar VMs de geração 2.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

* **As VMs de geração 2 estão disponíveis em todas as regiões do Azure?**  
    Sim. Mas nem todos os [tamanhos de VM da geração 2](#generation-2-vm-sizes) estão disponíveis em todas as regiões. A disponibilidade da vm geração 2 depende da disponibilidade do tamanho da VM.

* **Existe uma diferença de preço entre as VMs de geração 1 e 2?**  
   Não.

* **Eu tenho um arquivo .vhd da minha vm geração 2 no local. Posso usar esse arquivo .vhd para criar uma VM de geração 2 no Azure?**
  Sim, você pode trazer o seu arquivo de geração 2 .vhd para o Azure e usá-lo para criar uma geração 2 VM. Use as seguintes etapas para fazê-lo:
    1. Carregue o .vhd para uma conta de armazenamento na mesma região onde você gostaria de criar sua VM.
    1. Crie um disco gerenciado a partir do arquivo .vhd. Defina a propriedade Hyper-V Generation como V2. Os seguintes comandos PowerShell definem a propriedade Hyper-V Generation ao criar disco gerenciado.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Uma vez que o disco esteja disponível, crie uma VM anexando este disco. A VM criada será uma VM de geração 2.
    Quando a vm da geração 2 é criada, você pode, opcionalmente, generalizar a imagem desta VM. Ao generalizar a imagem, você pode usá-la para criar várias VMs.

* **Como aumentar o tamanho do disco do SISTEMA OPERACIONAL?**  
  Discos de sO maiores que 2 TB são novos para a geração 2 VMs. Por padrão, os discos do SO são menores que 2 TB para vMs de geração 2. Você pode aumentar o tamanho do disco até um máximo recomendado de 4 TB. Use o Azure CLI ou o portal Azure para aumentar o tamanho do disco do SO. Para obter informações sobre como expandir discos de forma programática, consulte [Redimensionar um disco](expand-os-disk.md).

  Para aumentar o tamanho do disco do SISTEMA OPERACIONAL a partir do portal Azure:

  1. No portal Azure, acesse a página de propriedades da VM.
  1. Para desligar e desalocar a VM, selecione o botão **Parar.**
  1. Na seção **Discos,** selecione o disco do SISTEMA OPERACIONAL que deseja aumentar.
  1. Na seção **Discos,** selecione **Configuração**e atualize o **Tamanho** para o valor desejado.
  1. Volte para a página de propriedades VM e **Inicie a** VM.
  
  Você pode ver um aviso para discos de sistema operacional maiores que 2 TB. O aviso não se aplica às VMs da geração 2. No entanto, tamanhos de disco do SISTEMA OPERACIONAL maiores que 4 TB não são *recomendados.*

* **As VMs de geração 2 suportam rede acelerada?**  
    Sim. Para obter mais informações, consulte [Criar uma VM com rede acelerada](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **O VHDX é suportado na geração 2?**  
    Não, as VMs da geração 2 suportam apenas VHD.

* **As VMs da geração 2 suportam o armazenamento em disco ultra azure?**  
    Sim.

* **Posso migrar uma VM da geração 1 para a geração 2?**  
    Não, você não pode mudar a geração de uma VM depois de criá-la. Se você precisar alternar entre as gerações de VM, crie uma nova VM de uma geração diferente.

* **Por que meu tamanho de VM não está habilitado no seletor de tamanho quando tento criar uma VM Gen2?**

    Isso pode ser resolvido fazendo o seguinte:

    1. Verifique se a propriedade **de geração VM** está definida como **Gen 2** na guia **Avançado.**
    1. Verifique se você está procurando por um [tamanho de VM que suporte VMs Gen2](#generation-2-vm-sizes).

## <a name="next-steps"></a>Próximas etapas

* Conheça [as máquinas virtuais da geração 2 em Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Aprenda a [preparar um VHD](prepare-for-upload-vhd-image.md) para carregar de sistemas locais para o Azure.
