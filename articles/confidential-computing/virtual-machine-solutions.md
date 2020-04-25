---
title: Soluções de computação confidencial do Azure em máquinas virtuais
description: Saiba mais sobre as soluções de computação confidencial do Azure em máquinas virtuais.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 8db477f87f344b28e8941e185c70c81d9b860a72
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149470"
---
# <a name="solutions-on-azure-virtual-machines"></a>Soluções em máquinas virtuais do Azure

Este artigo aborda informações sobre a implantação de VMs (máquinas virtuais) de computação confidencial do Azure que executam processadores Intel apoiados pelo Intel SGX (Intel [software Extension Guard](https://software.intel.com/sgx) ). 

## <a name="azure-confidential-computing-vm-sizes"></a>Tamanhos de VM de computação confidencial do Azure

As máquinas virtuais de computação confidencial do Azure são projetadas para proteger a confidencialidade e a integridade de seus dados e código enquanto eles são processados na nuvem 

[Série DCsv2](../virtual-machines/dcv2-series.md) As VMs são a família de tamanho de computação confidencial mais recente e mais recente. Essas VMs dão suporte a uma grande variedade de recursos de implantação, têm 2x o cache de página enclave (EPC) e uma seleção maior de tamanhos em comparação com nossas VMs da série DC. As VMs da [série DC](../virtual-machines/sizes-previous-gen.md#preview-dc-series) estão atualmente em visualização e serão preteridas e não incluídas na disponibilidade geral.

Inicie a implantação de uma VM da série DCsv2 por meio do Microsoft Commercial Marketplace seguindo o [tutorial de início rápido](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Tamanhos e regiões disponíveis atuais

Para obter uma lista de todos os tamanhos de VM de computação confidencial disponíveis em regiões disponíveis e zonas de disponibilidade, execute o seguinte comando no [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest):

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

A partir de abril de 2020, essas SKUs estão disponíveis nas seguintes regiões e zonas de disponibilidade:

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

Para obter uma exibição mais detalhada dos tamanhos acima, execute o seguinte comando:

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>Considerações de implantação

Siga um tutorial de início rápido para implantar uma máquina virtual da série DCsv2 em menos de 10 minutos. 

- **Assinatura do Azure** – para implantar uma instância de VM de computação confidencial, considere uma assinatura paga conforme o uso ou outra opção de compra. Se você estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), não terá uma cota para a quantidade apropriada de núcleos de computação do Azure.

- **Preços e disponibilidade regional** -encontre os preços para VMs da série DCsv2 na [página de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para ver a disponibilidade nas regiões do Azure.


- **Cota de núcleos** – Talvez seja preciso aumentar a cota de núcleos em sua assinatura do Azure, saindo do valor padrão. Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho de VM, incluindo a série DCsv2. Para solicitar um aumento de cota, [abra uma solicitação](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) de atendimento ao cliente online sem encargos. Observe que os limites padrão podem variar dependendo da sua categoria de assinatura.

  > [!NOTE]
  > Entre em contato com o Suporte do Azure se precisar de capacidade em larga escala. Cotas do Azure são limites de crédito, não garantias de capacidade. Independentemente de sua cota, você é cobrado apenas pelo núcleos utilizados.
  
- **Redimensionamento** – devido a seu hardware especializado, você só pode redimensionar instâncias de computação confidencial dentro da mesma família de tamanho. Por exemplo, você só pode redimensionar uma VM da série DCsv2 de um tamanho da série DCsv2 para outro. Não há suporte para o redimensionamento de um tamanho de computação não confidencial para um tamanho de computação confidencial.  

- **Imagem** – para fornecer o suporte Intel SGX (software Guard Extension) em instâncias de computação confidencial, todas as implantações precisam ser executadas em imagens de geração 2. A computação confidencial do Azure dá suporte a cargas de trabalho em execução no Ubuntu 18, 4 Gen 2, Ubuntu 16, 4 Gen 2 e Windows Server 2016 Gen 2. Leia sobre o [suporte para VMs de geração 2 no Azure](../virtual-machines/linux/generation-2.md) para saber mais sobre cenários com e sem suporte. 

- **Armazenamento** – computação confidencial do Azure discos de dados de máquina virtual e nossos discos de sistema operacional efêmero estão em discos de NVMe. As instâncias dão suporte apenas a discos SSD Premium e SSD Standard, não SSD Ultra ou HDD Standard. O tamanho da máquina virtual **DC8_v2** não dá suporte ao armazenamento Premium. 

- **Criptografia de disco** – as instâncias de computação confidenciais não dão suporte à criptografia de disco no momento. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações sobre alta disponibilidade e recuperação de desastres

Ao usar máquinas virtuais no Azure, você é responsável por implementar uma solução de alta disponibilidade e recuperação de desastres para evitar qualquer tempo de inatividade. 

A computação confidencial do Azure não dá suporte à redundância de zona por meio de Zonas de Disponibilidade no momento. Para obter a maior disponibilidade e redundância para computação confidencial, use [conjuntos de disponibilidade](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy). Devido a restrições de hardware, os conjuntos de disponibilidade para instâncias de computação confidencial só podem ter um máximo de 10 domínios de atualização. 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Implantando por meio de um modelo de Azure Resource Manager 

O Azure Resource Manager é p serviço de implantação e gerenciamento do Azure. Ele fornece uma camada de gerenciamento que lhe permite criar, atualizar e excluir recursos em sua assinatura do Azure. Use recursos de gerenciamento, como controle de acesso, bloqueios e marcas, para proteger e organizar seus recursos após a implantação.

Para saber mais sobre os modelos do Azure Resource Manager, confira [Visão geral de implantação de modelo](../azure-resource-manager/templates/overview.md).

Para implantar uma VM da série DCsv2 em um modelo do ARM, você utilizará o [recurso de máquina virtual](../virtual-machines/windows/template-description.md). Você precisa garantir que você especifique as propriedades corretas para **vmSize** e para seu **imageReference**.

### <a name="vm-size"></a>Tamanho da VM

Especifique um dos seguintes tamanhos no modelo do ARM no recurso de máquina virtual. Essa cadeia de caracteres é colocada como **vmSize** em **Propriedades**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Imagem do so Gen2

Em **Propriedades**, você também precisará fazer referência a uma imagem em **storageProfile**. Use *apenas uma* das imagens a seguir para seu **imageReference**.

```json
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Próximas etapas 

Neste artigo, você aprendeu sobre as qualificações e configurações necessárias ao criar uma máquina virtual de computação confidencial. Agora você pode ir para o Azure Marketplace para implantar uma VM da série DCsv2.

[!div class=”nextstepaction”]
[Implantar uma máquina virtual da série DCsv2 no Azure Marketplace](quick-create-marketplace.md)