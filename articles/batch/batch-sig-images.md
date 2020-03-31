---
title: Use a Galeria de Imagens Compartilhadas para criar um pool personalizado - Azure Batch | Microsoft Docs
description: Crie um pool de lotes com a Galeria de Imagens Compartilhadas para provisionar imagens personalizadas para calcular nós que contenham o software e os dados necessários para o seu aplicativo. Imagens personalizadas são uma maneira eficiente para configurar nós de computação para executar suas cargas de trabalho do Lote.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 2cff6a0e48fc7bf58a642f509fcda6b114e002ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022929"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Use a Galeria de Imagens Compartilhadas para criar um pool personalizado

Ao criar um pool no Lote do Azure usando a Configuração de Máquina Virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais com uma imagem suportada do Azure Marketplace ou criar uma imagem personalizada com a [Galeria de Imagens Compartilhadas](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Benefícios da Galeria de Imagens Compartilhadas

Quando você usa a Galeria de Imagens Compartilhadas para sua imagem personalizada, você tem controle sobre o tipo e configuração do sistema operacional, bem como o tipo de discos de dados. Sua Imagem Compartilhada pode incluir aplicativos e dados de referência que ficam disponíveis em todos os nós de pool de lote assim que forem provisionados.

Você também pode ter várias versões de uma imagem conforme necessário para o seu ambiente. Quando você usa uma versão de imagem para criar uma VM, a versão de imagem é usada para criar novos discos para a VM.

O uso de uma imagem compartilhada economiza tempo na preparação dos nós computacionais do pool para executar sua carga de trabalho em lote. É possível usar uma imagem do Azure Marketplace e instalar software em cada nó de computação após o provisionamento, mas usar uma imagem compartilhada é tipicamente mais eficiente. Além disso, você pode especificar várias réplicas para a Imagem Compartilhada para que, ao criar pools com muitas VMs (mais de 600 VMs), economize tempo na criação de pools.

Usar uma imagem compartilhada configurada para o seu cenário pode fornecer várias vantagens:

* **Use as mesmas imagens em todas as regiões.** Você pode criar réplicas de imagem compartilhada em diferentes regiões para que todos os seus pools utilizem a mesma imagem.
* **Configure o sistema operacional (OS).** Você pode personalizar a configuração do disco do sistema operacional da imagem.
* **Pré-instalação aplicativos.** A pré-instalação de aplicativos no disco do SISTEMA OPERACIONAL é mais eficiente e menos propensa a erros do que a instalação de aplicativos depois de provisionar os nós de computação com uma tarefa inicial.
* **Copie grandes quantidades de dados uma vez.** Faça com que os dados estáticos façam parte da imagem compartilhada gerenciada copiando-as para os discos de dados de uma imagem gerenciada. Isso só precisa ser feito uma vez e disponibiliza dados para cada nó do pool.
* **Cultivar piscinas para tamanhos maiores.** Com a Galeria de Imagens Compartilhadas, você pode criar piscinas maiores com suas imagens personalizadas, juntamente com mais réplicas de imagem compartilhada.
* **Melhor desempenho do que imagem personalizada.** Usando Imagens Compartilhadas, o tempo que leva para a piscina atingir o estado estável é até 25% mais rápido, e a latência ociosa da VM é até 30% menor.
* **Versão de imagem e agrupamento para um gerenciamento mais fácil.** A definição de agrupamento de imagens contém informações sobre por que a imagem foi criada, para que é o SO e informações sobre o uso da imagem. O agrupamento de imagens permite um gerenciamento mais fácil de imagens. Para obter mais informações, consulte [definições de imagem](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta do Lote do Azure.** Para criar uma conta batch, consulte o Batch quickstarts using the [Azure portal](quick-create-portal.md) ou [Azure CLI](quick-create-cli.md).

* **Uma imagem compartilhada da Galeria de Imagens**. Para criar uma imagem compartilhada, você precisa ter ou criar um recurso de imagem gerenciado. A imagem deve ser criada de instantâneos de disco do sistema operacional da VM e, opcionalmente, dos discos de dados anexados. Para obter mais informações, consulte [Preparar uma imagem gerenciada](#prepare-a-managed-image).

> [!NOTE]
> Sua Imagem Compartilhada deve estar na mesma assinatura da conta Batch. Sua Imagem Compartilhada pode estar em diferentes regiões, desde que tenha réplicas na mesma região que sua conta Batch.

## <a name="prepare-a-managed-image"></a>Prepare uma imagem gerenciada

No Azure, você pode preparar uma imagem gerenciada a partir de:

* Instantâneos do sistema operacional e discos de dados de um Azure VM
* Uma VM Azure generalizada com discos gerenciados
* Um VHD generalizado no local carregado na nuvem

Para dimensionar os pools de lote de forma confiável com uma imagem personalizada, é recomendável criar uma imagem gerenciada usando *apenas* o primeiro método: usando instantâneos de discos da VM. Consulte as etapas a seguir para preparar uma VM, gerar um instantâneo e criar uma imagem do instantâneo.

### <a name="prepare-a-vm"></a>Preparar uma VM

Se você estiver criando uma nova VM para a imagem, use uma imagem do Azure Marketplace de primeira parte suportada pelo Batch como a imagem base para sua imagem gerenciada. Apenas imagens de primeira parte podem ser usadas como uma imagem base. Para obter uma lista completa de referências de imagem do Azure Marketplace suportadas pelo Azure Batch, consulte a operação [SKUs do agente de nó lista.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Você não pode usar uma imagem de terceiros que tenham licenças adicionais e termos de compra como sua imagem de base. Para obter informações sobre essas imagens do Marketplace, consulte as diretrizes par máquinas virtuais [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) ou [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
).

* Certifique-se de que a VM seja criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.
* Não instale extensões do Azure, tais como a extensão de Script personalizado, na VM. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a o pool do Lote.
* Ao usar discos de dados conectados, você precisa montar e formatar os discos de dentro de uma VM para usá-los.
* Verifique se a imagem do sistema operacional base que você forneceu usa unidade temporária padrão. O agente do nó de Lote no momento espera unidade temporária padrão.
* Quando a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale o software necessário ou copie os dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo da VM

Um instantâneo é uma cópia completa somente leitura de um VHD. Para criar um instantâneo de discos de dados e do SO de uma VM, você pode usar o portal do Azure ou as ferramentas de linha de comando. Para etapas e opções para criar um instantâneo, veja as diretrizes para VMs [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem de um ou mais instantâneos

Para criar uma imagem gerenciada de um instantâneo, use ferramentas de linha de comando do Azure como o comando [az image create](/cli/azure/image). Crie uma imagem especificando um instantâneo de disco do SISTEMA OPERACIONAL e, opcionalmente, um ou mais instantâneos de disco de dados.

### <a name="create-a-shared-image-gallery"></a>Criar uma Galeria de Imagens Compartilhadas

Depois de criar com sucesso sua imagem gerenciada, você precisa criar uma Galeria de Imagens Compartilhadas para disponibilizar sua imagem personalizada. Para aprender como criar uma Galeria de Imagens Compartilhadas para suas imagens, consulte [Criar uma Galeria de Imagens Compartilhadas com o Azure CLI](../virtual-machines/linux/shared-images.md) ou Criar uma Galeria de [Imagens Compartilhadas usando o portal Azure](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Crie um pool a partir de uma imagem compartilhada usando o Cli do Azure

Para criar um pool a partir de sua Imagem `az batch pool create` Compartilhada usando o Azure CLI, use o comando. Especifique o ID `--image` de imagem compartilhada no campo. Certifique-se de que o tipo de Sistema operacional e SKU correspondem às versões especificadas por`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Crie um pool a partir de uma imagem compartilhada usando C #

Alternativamente, você pode criar um pool a partir de uma imagem compartilhada usando o C# SDK.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Crie um pool a partir de uma imagem compartilhada usando o portal Azure

Use as etapas a seguir para criar um pool a partir de uma imagem compartilhada no portal Azure.

1. Abra o [portal Azure.](https://portal.azure.com)
1. Vá para **contas de lote** e selecione sua conta.
1. Selecione **Pools** e, em seguida, **Adicione** para criar um novo pool.
1. Na seção **Tipo de imagem,** selecione **Galeria de Imagens Compartilhadas**.
1. Complete as seções restantes com informações sobre sua imagem gerenciada.
1. Selecione **OK**.

![Crie um pool com uma imagem compartilhada com o portal.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerações sobre pools grandes

Se você planeja criar um pool com centenas ou milhares de VMs ou mais usando uma Imagem Compartilhada, use a seguinte orientação.

* **Números de réplicas da Galeria de Imagens Compartilhadas.**  Para cada pool com até 600 instâncias, recomendamos que você mantenha pelo menos uma réplica. Por exemplo, se você está criando um pool com 3000 VMs, você deve manter pelo menos 5 réplicas da sua imagem. Sempre sugerimos manter mais réplicas do que os requisitos mínimos para um melhor desempenho.

* **Redimensione o tempo limite.** Se o seu pool contiver um número fixo de nós `resizeTimeout` (se não for em escala automática), aumente a propriedade da piscina dependendo do tamanho da piscina. Para cada 1000 VMs, o tempo limite recomendado de redimensionamento é de pelo menos 15 minutos. Por exemplo, o tempo limite recomendado para uma piscina com 2000 VMs é de pelo menos 30 minutos.

## <a name="next-steps"></a>Próximas etapas

* Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
