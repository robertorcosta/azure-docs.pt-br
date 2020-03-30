---
title: Habilitar a replicação para VMs Azure criptografados na recuperação do site do Azure
description: Este artigo descreve como configurar a replicação para VMs com a chave gerenciada pelo cliente (CMK) habilitada para discos de uma região do Azure para outra usando o Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897956"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replicar máquinas com discos habilitados para CMK (Customer-Managed Keys, chaves gerenciadas pelo cliente)

Este artigo descreve como replicar VMs do Azure com discos gerenciados pelo cliente (CMK) habilitados, de uma região do Azure para outra.

## <a name="prerequisite"></a>Pré-requisito
Você deve criar os conjuntos de criptografia de disco na região de destino para a assinatura de destino antes de habilitar a replicação para suas máquinas virtuais que possuem discos gerenciados habilitados para CMK.

## <a name="enable-replication"></a>Habilitar a replicação

Para este exemplo, a região principal do Azure é o leste da Ásia, e a região secundária é o Sudeste Asiático.

1. No cofre, selecione **+Replicar**.
2. Observe os seguintes campos.
    - **Fonte**: o ponto de origem das VMs que, neste caso, é o **Azure**.
    - **Localização da fonte**: A região do Azure onde você deseja proteger suas máquinas virtuais. Para este exemplo, a localização de origem é "Ásia Oriental".
    - **Modelo de implantação**: O modelo de implantação do Azure das máquinas de origem.
    - **Assinatura de origem**: a assinatura à qual suas máquinas virtuais de origem pertencem. Pode ser qualquer assinatura que esteja no mesmo inquilino do Azure Active Directory que o cofre de serviços de recuperação.
    - **Grupo de recursos**: o grupo de recursos ao qual pertencem as máquinas virtuais de origem. Todas as VMs do grupo de recursos selecionados estão listadas para proteção na próxima etapa.

3. Em **Máquinas** > Virtuais**Selecione máquinas virtuais,** selecione cada VM que deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, selecione **OK**.

4. Em **Configurações,** você pode configurar as seguintes configurações do site de destino.

    - **Localização do destino**: O local onde os dados da máquina virtual de origem serão replicados. A Recuperação do Site fornece uma lista de regiões de destino adequadas com base na localização da máquina selecionada. Recomendamos que você use o mesmo local que a localização do cofre do Recovery Services.
    - **Assinatura de destino**: A assinatura de destino usada para recuperação de desastres. Por padrão, a assinatura de destino será igual à assinatura de origem.
    - **Grupo de recursos de destino**: o grupo de recursos ao qual pertencem todas as máquinas virtuais replicadas. Por padrão, a Recuperação do Site cria um novo grupo de recursos na região de destino. O nome `asr` fica com o sufixo. Se um grupo de recursos já existe que foi criado pelo Azure Site Recovery, ele será reutilizado. Você também pode optar por personalizá-lo, como mostrado na seção a seguir. A localização do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região onde as máquinas virtuais de origem estão hospedadas.
    - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região de destino. O nome `asr` fica com o sufixo. É mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
    - **Contas de armazenamento de destino (se sua VM de origem não usar discos gerenciados)**: Por padrão, o Site Recovery cria uma nova conta de armazenamento de destino imitando a configuração de armazenamento vm de origem. Se uma conta de armazenamento já existe, ela é reutilizada.
    - **Réplica de discos gerenciados (se sua VM de origem usar discos gerenciados)**: O Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem do mesmo tipo de armazenamento (padrão ou premium) que os discos gerenciados da VM de origem.
    - **Contas de armazenamento em cache**: A Recuperação do Site precisa de uma conta de armazenamento extra chamada armazenamento de *cache* na região de origem. Todas as alterações nas VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache. Eles são então replicados para o local alvo.
    - **Conjunto de disponibilidade**: Por padrão, a Recuperação do Site cria um novo conjunto de disponibilidade na região-alvo. O nome `asr` tem o sufixo. Se um conjunto de disponibilidade criado pelo Site Recovery já existir, ele será reutilizado.
    - **Conjuntos de criptografia de disco (DES)**: A recuperação do site precisa que os conjuntos de criptografia de disco sejam usados para réplicas e discos gerenciados por destino. Você deve pré-criar O DES na assinatura de destino e na região de destino antes de habilitar a replicação. Por padrão, um DES não é selecionado. Você deve clicar em 'Personalizar' para escolher um DES por disco de origem.
    - **Política de replicação**: Define as configurações para o histórico de retenção de ponto de recuperação e a freqüência de instantâneo consistente com o aplicativo. Por padrão, o Site Recovery cria uma nova política de replicação com configurações padrão de *24 horas* para retenção de ponto de recuperação e *60 minutos* para freqüência de instantâneo consistente com o aplicativo.

    ![Habilitar a replicação para máquina com discos habilitados para CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Siga essas etapas para modificar as configurações de destino padrão de recuperação do site.

1. Selecione **Personalizar** ao lado de "Assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de assinaturas que estão disponíveis no inquilino azure AD.

2. Selecione **Personalizar** ao lado de "Conjunto de recursos, configurações de rede, armazenamento e disponibilidade" para modificar as seguintes configurações padrão:
    - Para **o grupo de recursos Target,** selecione o grupo de recursos da lista de grupos de recursos no local de destino da assinatura.
    - Para **rede virtual Target,** selecione a rede a partir de uma lista de redes virtuais no local de destino.
    - Para **o conjunto Disponibilidade,** você pode adicionar configurações de configuração de disponibilidade à VM, se elas fizerem parte de um conjunto de disponibilidade na região de origem.
    - Para **contas de armazenamento de**destino, selecione a conta a ser usada.

3. Selecione **Personalizar** ao lado de "Configurações de criptografia de armazenamento" para selecionar o DES de destino para cada disco gerenciado de origem gerenciado pelo cliente (CMK). No momento da seleção, você também poderá ver com qual cofre de chave alvo o DES está associado.

4. Selecione **Criar recurso de destino** > **Habilitar a replicação**.
5. Depois que as VMs estiverem habilitadas para replicação, você pode verificar o estado de saúde das VMs em **itens replicados**.

![Habilitar a replicação para máquina com discos habilitados para CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para ser atualizado, sem progresso aparente. Clique **em Atualizar** para obter o status mais recente.

## <a name="faqs"></a>Perguntas frequentes

* Habilitei o CMK em um item replicado existente, como posso garantir que o CMK seja aplicado na região alvo também?

    Você pode descobrir o nome do disco gerenciado da réplica (criado pelo Azure Site Recovery na região de destino) e anexar DES a este disco de réplica. No entanto, você não poderá ver os detalhes do DES na lâmina discos uma vez que você anexá-lo. Alternativamente, você pode optar por desativar a replicação da VM e ativá-la novamente. Ele garantirá que você veja os detalhes do DES e do cofre da chave na lâmina Discos para o item replicado.

* Adicionei um novo disco habilitado para CMK ao item replicado. Como posso replicar este disco com o Azure Site Recovery?

    A adição de um novo disco habilitado para CMK a um item replicado existente não é suportada. Desative a replicação e habilite a replicação novamente para a máquina virtual.

