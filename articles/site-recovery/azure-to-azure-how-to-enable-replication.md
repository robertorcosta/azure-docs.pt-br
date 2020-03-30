---
title: Configurar a replicação para VMs do Azure na recuperação do site do Azure
description: Saiba como configurar a replicação em outra região para VMs do Azure, usando o Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973700"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replicar VMs do Azure para outra região do Azure


Este artigo descreve como habilitar a replicação de VMs do Azure, de uma região do Azure para outra.

## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe que você se preparou para a implantação do Site Recovery, conforme descrito no [tutorial de recuperação de desastres do Azure para o Azure](azure-to-azure-tutorial-enable-replication.md).

Os pré-requisitos devem estar no lugar, e você deveria ter criado um cofre de Serviços de Recuperação.


## <a name="enable-replication"></a>Habilitar a replicação

Habilite a replicação. Este procedimento pressupõe que a região do Azure primária seja Leste da Ásia e a região secundária seja Sudeste da Ásia.

1. No cofre, clique em **+Replicar**.
2. Observe os seguintes campos:
   - **Fonte**: o ponto de origem das VMs que, neste caso, é o **Azure**.
   - **Localização da fonte:** A região azure de onde você deseja proteger suas VMs. Para esta ilustração, a localização da fonte é 'Leste da Ásia'
   - **Modelo de implantação**: o modelo de implantação do Azure de computadores de origem.
   - **Assinatura de origem**: A assinatura a que suas VMs de origem pertencem. Pode ser qualquer assinatura dentro do mesmo locatário do Azure Active Directory na qual existe o cofre dos serviços de recuperação.
   - **Grupo de recursos**: o grupo de recursos ao qual pertencem as máquinas virtuais de origem. Todas as máquinas virtuais do grupo de recursos selecionado são listadas para a proteção na próxima etapa.

     ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Em **Máquinas Virtuais > Selecionar máquinas virtuais,** clique e selecione cada VM que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.
    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Em **Configurações**, você também pode definir configurações do site de destino:

   - **Local de destino**: o local onde seus dados de máquina virtual de origem serão replicados. Dependendo de seu local de máquinas selecionadas, a recuperação de Site fornece a lista de regiões de destino adequado. É recomendável que você mantenha o local de destino o mesmo do local do cofre dos Serviços de Recuperação.
   - **Assinatura de destino**: a assinatura de destino usada para recuperação de desastres. Por padrão, a assinatura de destino será o mesma que a assinatura de origem.
   - **Grupo de recursos de destino**: o grupo de recursos ao qual pertencem todas as máquinas virtuais replicadas.
       - Por padrão, a Recuperação de Site cria um novo grupo de recursos na região de destino com um sufixo "asr" no nome.
       - Se o grupo de recursos criado pelo Site Recovery já existir, ele será reutilizado.
       - Você pode personalizar as configurações do grupo de recursos.
       - A localização do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região em que as VMs de origem estão hospedadas.
   - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região de destino com um sufixo "asr" no nome. Isso é mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
   - **Contas de armazenamento de destino (a VM de origem não usa discos gerenciados)**: Por padrão, o Site Recovery cria uma nova conta de armazenamento de destino que imita a configuração de armazenamento vm de origem. Caso a conta de armazenamento já exista, ela é reutilizada.
   - **Discos gerenciados por réplicas (a VM de origem usa discos gerenciados)**: A Recuperação do Site cria novos discos gerenciados por réplicas na região de destino para espelhar os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Padrão ou premium) que o disco gerenciado da VM de origem.
   - **Contas de armazenamento em cache**: o Site Recovery precisa de uma conta de armazenamento extra, chamada armazenamento em cache na região de origem. Todas as alterações ocorrendo nas máquinas virtuais de origem são controladas e enviadas para a conta de armazenamento do cache antes de replicar para o local de destino. Esta conta de armazenamento deve ser padrão.
   - **Conjuntos de disponibilidade de destino**: Por padrão, a Recuperação do Site cria um novo conjunto de disponibilidade na região-alvo com o sufixo "asr" no nome, para VMs que fazem parte de uma disponibilidade definida na região de origem. Se o conjunto de disponibilidade criado pelo Site Recovery já existir, ele será reutilizado.
   - **Zonas de disponibilidade de destino**: por padrão, o Site Recovery atribui o mesmo número da zona que a região de origem na região de destino se a região de destino oferecer suporte a zonas de disponibilidade.

     Se a região de destino não oferecer suporte a zonas de disponibilidade, as VMs de destino são configuradas como instâncias isoladas por padrão. Se necessário, você pode configurar essas VMs como parte dos conjuntos de disponibilidade na região de destino, clicando em “Personalizar”.

     >[!NOTE]
     >Depois de habilitar a replicação, não é possível alterar o tipo de disponibilidade — única instância, zona de disponibilidade ou conjunto de disponibilidade. É preciso desabilitar e habilitar a replicação para alterar o tipo de disponibilidade.
     >

   - **Política de replicação**: Define as configurações para o histórico de retenção de ponto de recuperação e a freqüência de instantâneo consistente do aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de '24 horas' para retenção de ponto de recuperação e '4 horas' para freqüência de instantâneo consistente do aplicativo.

     ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Habilitar a replicação para discos adicionados

Se você adicionar discos a uma VM do Azure para a qual a replicação está ativada, ocorrerão as seguintes:
-   A saúde de replicação da VM mostra um aviso, e uma nota informa que um ou mais discos estão disponíveis para proteção.
-   Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se você optar por não habilitar a replicação para o disco, você pode selecionar para descartar o aviso.


    ![Novo disco adicionado](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Para habilitar a replicação de um disco adicionado, faça o seguinte:

1.  No cofre > **Itens Replicados,** clique na VM à qual você adicionou o disco.
2.  Clique **em Discos**e selecione o disco de dados para o qual deseja ativar a replicação (esses discos têm um status Não **protegido).**
3.  Em **Detalhes em disco,** clique **em Ativar a replicação**.

    ![Habilitar a replicação para adicionar disco](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Depois que o trabalho de replicação habilitado é executado e o acabamento da replicação inicial, o aviso de saúde de replicação para o problema do disco é removido.



## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Você pode modificar as configurações de destino padrão usadas pelo Site Recovery.

1. Clique em **Personalizar:** ao lado de "Assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de todas as assinaturas disponíveis no mesmo locatário do AAD (Azure Active Directory).

2. Clique em **Personalizar:** para modificar as configurações padrão:
    - Em **Grupo de recursos de destino**, selecione o grupo de recursos da lista de todos os grupos de recursos no local de destino da assinatura.
    - Em **Rede virtual de destino**, selecione a rede em uma lista de todas as redes virtuais no local de destino.
    - Em **Conjunto de disponibilidade**, você pode adicionar configurações do conjunto de disponibilidade à VM, caso elas façam parte de um conjunto de disponibilidade na região de origem.
    - Em **Contas de armazenamento de destino**, selecione a conta que você deseja usar.

        ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Clique em **Personalizar** para modificar as configurações de replicação.
4. Em **consistência Multi-VM,** selecione as VMs que deseja replicar em conjunto.
    - Todos os computadores de um grupo de replicação terão pontos de recuperação consistentes com o aplicativo e consistentes com falhas quando passarem por failover.
    - Ativar a consistência de vários VM pode afetar o desempenho da carga de trabalho (já que é intensiva em CPU). Ele só deve ser habilitado se as máquinas estiverem executando a mesma carga de trabalho, e você precisa de consistência em várias máquinas.
    - Por exemplo, se um aplicativo tiver 2 máquinas virtuais SQL Server e dois servidores web, então você deve adicionar apenas as VMs do SQL Server a um grupo de replicação.
    - Você pode optar por ter um máximo de 16 VMs em um grupo de replicação.
    - Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004.
    - Certifique-se de que não há nenhum aparelho de firewall bloqueando a comunicação interna entre as VMs sobre a porta 20004.
    - Se você quiser que as VMs Linux sejam parte de um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 seja aberto manualmente de acordo com a orientação para a versão específica do Linux.
![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Clique **em Criar recurso de destino** > **Habilitar a replicação**.
6. Depois que as VMs forem habilitadas para replicação, você poderá verificar o status da integridade da VM em **Itens replicados**

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para atualizar, sem andamento. Clique no botão **Atualizar** para obter o status mais recente.
>

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
