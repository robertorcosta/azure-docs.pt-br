---
title: Provisionar um pool personalizado a partir de uma imagem gerenciada - Azure Batch | Microsoft Docs
description: Crie um pool de lotes a partir de um recurso de imagem gerenciado para provisionar nós de computação com o software e os dados para sua aplicação.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020141"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Use uma imagem gerenciada para criar um pool de máquinas virtuais

Para criar uma imagem personalizada para as máquinas virtuais (VMs) do pool batch, você pode usar a [Galeria de Imagens Compartilhadas](batch-sig-images.md)ou um recurso *de imagem gerenciado.*

> [!TIP]
> Na maioria dos casos, você deve criar imagens personalizadas usando a Galeria de Imagens Compartilhadas. Usando a Galeria de Imagens Compartilhadas, você pode fornecer pools mais rapidamente, dimensionar quantidades maiores de VMs e ter maior confiabilidade ao provisionar VMs. Para saber mais, consulte [Use a Galeria de Imagens Compartilhadas para criar um pool personalizado](batch-sig-images.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerenciada**. Para criar um pool de máquinas virtuais usando uma imagem personalizada, você precisa ter ou criar um recurso de imagem gerenciada na mesma assinatura e região do Azure que a conta do Lote. A imagem deve ser criada de instantâneos de disco do sistema operacional da VM e, opcionalmente, dos discos de dados anexados. Para obter mais informações e etapas para preparar uma imagem gerenciada, veja a seção a seguir.
  - Use uma imagem personalizada exclusiva para cada pool que criar.
  - Para criar um pool com a imagem usando as APIs de Lote, especifique a **ID de recurso** da imagem, que tem a forma `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Para usar o portal, use o **nome** da imagem.  
  - O recurso de imagem gerenciada deve existir pelo tempo de vida do pool para permitir a expansão e pode ser removido após a exclusão do pool.

- **Autenticação do Azure Active Directory (AAD).** A API do cliente de Lote deve usar a autenticação do AAD. O suporte ao Lote do Azure para AAD está documentado em [Autenticar soluções do serviço de Lote com o Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

No Azure, você pode preparar uma imagem gerenciada a partir de:

- Instantâneos do sistema operacional e discos de dados de um Azure VM
- Uma VM Azure generalizada com discos gerenciados
- Um VHD generalizado no local carregado na nuvem

Para dimensionar os pools de lote de forma confiável com uma imagem personalizada, é recomendável criar uma imagem gerenciada usando *apenas* o primeiro método: usando instantâneos de discos da VM. Consulte as etapas a seguir para preparar uma VM, gerar um instantâneo e criar uma imagem do instantâneo.

### <a name="prepare-a-vm"></a>Preparar uma VM

Se você estiver criando uma nova VM para a imagem, use uma imagem do Azure Marketplace de primeira parte suportada pelo Batch como a imagem base para sua imagem gerenciada. Apenas imagens de primeira parte podem ser usadas como uma imagem base. Para obter uma lista completa de referências de imagem do Azure Marketplace suportadas pelo Azure Batch, consulte a operação [SKUs do agente de nó lista.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Você não pode usar uma imagem de terceiros que tenham licenças adicionais e termos de compra como sua imagem de base. Para obter informações sobre essas imagens do Marketplace, consulte as diretrizes par máquinas virtuais [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) ou [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
).

- Certifique-se de que a VM seja criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.
- Não instale extensões do Azure, tais como a extensão de Script personalizado, na VM. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a o pool do Lote.
- Ao usar discos de dados conectados, você precisa montar e formatar os discos de dentro de uma VM para usá-los.
- Verifique se a imagem do sistema operacional base que você forneceu usa unidade temporária padrão. O agente do nó de Lote no momento espera unidade temporária padrão.
- Quando a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale o software necessário ou copie os dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo da VM

Um instantâneo é uma cópia completa somente leitura de um VHD. Para criar um instantâneo de discos de dados e do SO de uma VM, você pode usar o portal do Azure ou as ferramentas de linha de comando. Para etapas e opções para criar um instantâneo, veja as diretrizes para VMs [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem de um ou mais instantâneos

Para criar uma imagem gerenciada de um instantâneo, use ferramentas de linha de comando do Azure como o comando [az image create](/cli/azure/image). Você pode criar uma imagem especificando um instantâneo do disco do sistema operacional e, opcionalmente, um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um pool de uma imagem personalizada no portal

Após ter salvo sua imagem personalizada e saber o nome ou a ID do recurso, crie um pool do Lote dessa imagem. As etapas a seguir mostram como criar um pool do portal do Azure.

> [!NOTE]
> Se você estiver criando o pool usando uma das APIs de Lote, verifique se a identidade que você usa para autenticação do AAD tem permissões para o recurso de imagem. Consulte [Autenticar soluções do serviço do Lote no Active Directory](batch-aad-auth.md).
>
> O recurso para a imagem gerenciada deve existir durante toda a vida útil do pool. Se o recurso subjacente for excluído, o pool não poderá ser dimensionado.

1. Navegue até sua conta do Lote no portal do Azure. Esta conta deve estar na mesma assinatura e na mesma região que o grupo de recursos que contém a imagem personalizada.
2. Na janela **Configurações** à esquerda, selecione o item de menu **Pools**.
3. Na janela **Pools**, selecione o comando **Adicionar**.
4. Na janela **Adicionar Pool**, selecione **Imagem Personalizada (Windows/Linux)** na lista suspensa **Tipo de Imagem**. Da lista suspensa **Imagem personalizada de VM**, selecione o nome da imagem (forma abreviada da ID de recurso).
5. Selecione o **Editor/Oferta/SKU** correto para sua imagem personalizada.
6. Especifique as configurações restantes necessárias, incluindo o tamanho do **nó,** **os nós dedicados ao destino**e os **nós de baixa prioridade,** bem como as configurações opcionais desejadas.

    Por exemplo, para uma imagem personalizada do Microsoft Windows Server Datacenter 2016, a janela **Adicionar Pool** aparece como mostrado abaixo:

    ![Adicionar pool de uma imagem personalizada do Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para verificar se um pool existente é baseado em uma imagem personalizada, consulte a propriedade **Sistema Operacional** na seção de resumo de recursos da folha **Pool**. Se o pool tiver sido criado de uma imagem personalizada, ela estará definida como **Imagem de VM personalizada**.

Todas as imagens personalizadas associadas a um pool são exibidas na janela **Propriedades** do pool.

## <a name="considerations-for-large-pools"></a>Considerações sobre pools grandes

Se você planeja criar um pool com centenas de VMs ou mais usando uma imagem personalizada, é importante seguir as diretrizes anteriores para usar uma imagem criada de um instantâneo de VM.

Observe também as seguintes considerações:

- **Limites de tamanho** – o Lote limita o tamanho do pool a 2.500 nós de computação dedicados, ou 1.000 nós de baixa prioridade, quando você usa uma imagem personalizada.

  Se você usar a mesma imagem (ou várias imagens com base no mesmo instantâneo subjacente) para criar vários pools, os nós de computação totais nos pools não poderão exceder os limites anteriores. Não recomendamos o uso de uma imagem ou do respectivo instantâneo subjacente para mais de um único pool.

  Os limites poderão ser reduzidos se você configurar o pool com [pools NAT de entrada](pool-endpoint-configuration.md).

- **Tempo limite de redimensionamento** – se o pool contém um número fixo de nós (não dimensiona automaticamente), aumente propriedade resizeTimeout do pool para um valor como 20 ou 30 minutos. Se o seu pool não alcançar seu tamanho de destino dentro do período de tempo limite, execute outra [operação de redimensionamento](/rest/api/batchservice/pool/resize).

  Se você planejar um pool com mais de 300 nós de computação, você poderá precisar redimensionar o pool várias vezes para alcançar o tamanho de destino.
  
Usando a [Galeria de Imagens Compartilhadas,](batch-sig-images.md)você pode criar piscinas maiores com suas imagens personalizadas, juntamente com mais réplicas de imagem compartilhada. Usando Imagens Compartilhadas, o tempo que leva para a piscina atingir o estado estável é até 25% mais rápido, e a latência ociosa da VM é até 30% menor.

## <a name="considerations-for-using-packer"></a>Considerações para usar packer

A criação de um recurso de imagem gerenciado diretamente com o Packer só pode ser feita com contas em lote do modo de assinatura do usuário. Para contas de modo de serviço em lote, você precisa criar um VHD primeiro e, em seguida, importar o VHD para um recurso de imagem gerenciado. Dependendo do modo de alocação de pool (assinatura do usuário ou serviço em lote), suas etapas para criar um recurso de imagem gerenciado variam.

Certifique-se de que o recurso usado para criar a imagem gerenciada exista para as vidas de qualquer pool que referenciaa a imagem personalizada. Caso contrário, pode resultar em falhas de alocação de pool e/ou redimensionamento.

Se a imagem ou o recurso subjacente for removido, `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`você pode obter um erro semelhante a: . Se você tiver esse erro, certifique-se de que o recurso subjacente não foi removido.

Para obter mais informações sobre como usar o Packer para criar uma VM, consulte [Construir uma imagem Linux com o Packer](../virtual-machines/linux/build-image-with-packer.md) ou construir uma imagem do Windows com o [Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Próximas etapas

Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
