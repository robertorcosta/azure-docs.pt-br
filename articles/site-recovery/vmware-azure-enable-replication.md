---
title: Habilite vMs VMware para recuperação de desastres usando a recuperação do site do Azure
description: Este artigo descreve como ativar a replicação vmware VMpara recuperação de desastres usando o serviço azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 6547bcf2061213cd01550367171d432900693ea5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584139"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Habilitar a replicação no Azure de VMs VMware

Este artigo descreve como permitir a replicação de máquinas virtuais VMware (VM) no local para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que seu sistema atende aos seguintes critérios:

- [Configure seu ambiente de origem no local.](vmware-azure-set-up-source.md)
- [Configure seu ambiente de destino no Azure](vmware-azure-set-up-target.md).
- [Verifique os requisitos e pré-requisitos](vmware-physical-azure-support-matrix.md) antes de iniciar. As coisas importantes a serem observados incluem:
  - [Sistemas operacionais suportados](vmware-physical-azure-support-matrix.md#replicated-machines) para máquinas replicadas.
  - [Suporte a armazenamento/disco.](vmware-physical-azure-support-matrix.md#storage)
  - [Requisitos do Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) com os quais as máquinas locais devem cumprir.

### <a name="resolve-common-issues"></a>Resolver problemas comuns

- Cada disco deve ser menor que 4 TB.
- O disco do sistema operacional deve ser um disco básico, não um disco dinâmico.
- Para máquinas virtuais habilitadas para UEFI de geração 2, a família do sistema operacional deve ser o Windows, e o disco de inicialização deve ser menor que 300 GB.

## <a name="before-you-start"></a>Antes de começar

Quando você replica máquinas virtuais VMware, tenha essas informações em mente:

- Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.
- VMs VMware são descobertas a cada 15 minutos. Pode levar 15 minutos ou mais para as VMs aparecerem no portal Azure após a descoberta. Quando você adiciona um novo servidor vCenter ou um host vSphere, a descoberta pode levar 15 minutos ou mais.
- Pode levar 15 minutos ou mais para que as mudanças de ambiente na máquina virtual sejam atualizadas no portal. Por exemplo, a instalação de ferramentas VMware.
- Você pode verificar o tempo descoberto por VMware VMs: Consulte o **campo Último Contato** na página Servidores de **configuração** para o host vCenter server/vSphere.
- Para adicionar máquinas virtuais para replicação sem esperar pela descoberta programada, destaque o servidor de configuração (mas não clique nele) e selecione **Atualizar**.
- Quando você habilita a replicação, se a máquina virtual estiver preparada, o servidor de processo instala automaticamente o serviço de mobilidade de recuperação do site do Azure na VM.

## <a name="enable-replication"></a>Habilitar a replicação

Antes de fazer as etapas nesta seção, revise as seguintes informações:

- O Azure Site Recovery agora se replica diretamente em discos gerenciados para todas as novas replicações. O servidor de processo grava registros de replicação em uma conta de armazenamento de cache na região de destino. Esses logs são usados para criar pontos de recuperação em `asrseeddisk`discos gerenciados por réplicas que têm convenção de nomenclatura de .
- O suporte ao PowerShell para replicação em discos gerenciados está disponível a partir [da versão 2.0.0 do módulo Az.RecoveryServices](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- No momento do failover, o ponto de recuperação selecionado é usado para criar o disco gerenciado por destino.
- As VMs que foram configuradas anteriormente para replicar contas de armazenamento de destino não são afetadas.
- A replicação das contas de armazenamento de uma nova máquina virtual só está disponível através de uma API de Transferência de Estado De Representação (REST) e do PowerShell. Use a api do Azure REST versão 2016-08-10 ou 2018-01-10 para replicação em contas de armazenamento.

Para habilitar a replicação, siga estas etapas:

1. Ir para **o passo 2: replicar a** > **fonte do**aplicativo . Depois de habilitar a replicação pela primeira vez, selecione **+Replicar** no cofre para permitir a replicação de máquinas virtuais adicionais.
1. Na página **Origem** > **Origem**, selecione o servidor de configuração.
1. Para **o tipo Máquina,** selecione **Máquinas Virtuais** ou **Máquinas Físicas**.
1. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host. Esta configuração não é relevante se você estiver replicando computadores físicos.
1. Selecione o servidor de processo. Se não houver servidores de processo adicionais criados, o servidor de processo embutido do servidor de configuração estará disponível no menu suspenso. O estado de saúde de cada servidor de processo é indicado conforme os limites recomendados e outros parâmetros. Escolha um servidor de processo íntegro. Um servidor de processo [crítico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) não pode ser escolhido. Você pode [solucionar problemas e resolver](vmware-physical-azure-troubleshoot-process-server.md) os erros **ou** configurar um [servidor de processo de expansão](vmware-azure-set-up-process-server-scale.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Habilitar janela de origem de replicação":::

   > [!NOTE]
   > A partir [da versão 9.24,](site-recovery-whats-new.md)alertas adicionais são introduzidos para melhorar os alertas de saúde do servidor de processo. Atualize os componentes de recuperação do site para a versão 9.24 ou superior para que todos os alertas sejam gerados.

1. Para **Destino,** selecione o grupo de assinatura e recursos onde deseja criar a falha em máquinas virtuais. Escolha o modelo de implantação que deseja usar no Azure para obter falhas em VMs.
1. Selecione a rede e a sub-rede azure às as que as VMs do Azure se conectarão após o failover. A rede deve estar na mesma região que o cofre de serviço de recuperação do site.

   Selecione **Configurar agora para máquinas selecionadas** para aplicar a configuração de rede a todas as máquinas virtuais selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede Azure por máquina virtual. Se você não tiver uma rede, precisará criar uma. Para criar uma rede usando o Azure Resource Manager, selecione **Criar novo**. Selecione uma sub-rede, se aplicável, e selecione **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Habilitar janela de destino de replicação":::

1. Para **máquinas** > virtuais**Selecione máquinas virtuais,** selecione cada máquina virtual que deseja replicar. Você só pode selecionar máquinas virtuais para as quais a replicação pode ser ativada. Depois, selecione **OK**. Se você não puder ver ou selecionar qualquer máquina virtual em particular, veja [A máquina De Origem não está listada no portal Azure](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) para resolver o problema.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Ativar replicação Selecionar janela de máquinas virtuais":::

1. Para **propriedades** > **Configurar propriedades,** selecione a conta que o servidor de processo usa para instalar automaticamente o serviço de mobilidade de recuperação do site na VM. Além disso, escolha o tipo de disco gerenciado de destino a ser usado para replicação com base em seus padrões de churn de dados.
1. Por padrão, todos os discos de uma VM de origem são replicados. Para excluir discos da replicação, limpe a caixa **Incluir** seleção para quaisquer discos que você não deseja replicar. Depois, selecione **OK**. Você pode definir propriedades adicionais posteriormente. [Saiba mais](vmware-azure-exclude-disk.md) sobre a exclusão de discos.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Habilitar a replicação configurar a janela de propriedades":::

1. A partir **das configurações** > de replicação**Configure as configurações de replicação,** verifique se a diretiva de replicação correta está selecionada. Você pode modificar as configurações da diretiva de replicação em **Configurações** > de_diretiva_ > de**configuração de configuração** > de configurações de replicação**configurações**de edição . As alterações aplicadas a uma política também se aplicam à replicação e às novas máquinas virtuais.
1. Se você quiser reunir máquinas virtuais em um grupo de replicação, habilite **a consistência multi-VM**. Especifique um nome para o grupo e selecione **OK**.

   > [!NOTE]
   > - As máquinas virtuais em um grupo de replicação se replicam juntas e compartilham pontos de recuperação consistentes com falhas e consistentes com aplicativos quando falham.
   > - Colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho. Faça isso somente se as máquinas virtuais estiverem executando a mesma carga de trabalho, e você precisar de consistência.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Habilitar janela de replicação":::

1. Selecione **Ativar a replicação**. Você pode acompanhar o progresso do trabalho **de proteção de habilitação** em **Configurações** > **Empregos** > **de recuperação de sites**. Depois que o **trabalho de Proteção finalizada** for executado, a máquina virtual está pronta para failover.

## <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar as propriedades da VM

Em seguida, verifique as propriedades da máquina virtual de origem. Lembre-se de que o nome da VM do Azure precisa estar em conformidade com os [Requisitos de máquina virtual do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Vá para **Configurações** > **Itens replicados**e selecione a máquina virtual. A página **Essentials** mostra informações sobre as configurações e o status da VM.
1. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.
1. Em **propriedades de Computação e** > **Computação de**Rede, você pode alterar várias propriedades de VM.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Janela de propriedades de computação e rede":::

   - **Nome azure VM**: Modifique o nome para atender aos requisitos do Azure, se necessário.
   - **Tamanho de VM ou tipo VM:** O tamanho padrão da VM é escolhido com base em parâmetros que incluem contagem de discos, contagem de NIC, contagem de núcleos da CPU, memória e tamanhos de função VM disponíveis na região do Azure alvo. O Azure Site Recovery escolhe o primeiro tamanho de VM disponível que satisfaz todos os critérios. Você pode selecionar um tamanho de VM diferente com base em suas necessidades a qualquer momento antes do failover. O tamanho do disco VM também é baseado no tamanho do disco de origem, e ele só pode ser alterado após o failover. Saiba mais sobre tamanhos de disco e taxas de IOPS em [metas de escalabilidade e desempenho para discos VM no Windows](/azure/virtual-machines/windows/disk-scalability-targets).
   - **Grupo de recursos**: Você pode selecionar um [grupo de recursos](/azure/azure-resource-manager/management/overview#resource-groups), a partir do qual uma máquina virtual se torna parte de um failover de post. Você pode alterar esta configuração a qualquer momento antes do failover. Após o failover, se você migrar a máquina virtual para um grupo de recursos diferente, as configurações de proteção para essa quebra de máquina virtual.
   - **Conjunto de disponibilidade**: Você pode selecionar um [conjunto de disponibilidade](/azure/virtual-machines/windows/tutorial-availability-sets) se sua máquina virtual precisar fazer parte de um failover pós.. Ao selecionar um conjunto de disponibilidade, tenha em mente as seguintes informações:
     - Apenas conjuntos de disponibilidade que pertencem ao grupo de recursos especificado estão listados.
     - VMs que estão em diferentes redes virtuais não podem fazer parte do mesmo conjunto de disponibilidade.
     - Somente máquinas virtuais do mesmo tamanho podem fazer parte de um mesmo conjunto de disponibilidade.

1. Você também pode adicionar informações sobre a rede de destino, a sub-rede e o endereço IP atribuídos à VM do Azure.
1. Em **Discos,** você pode ver o sistema operacional e os discos de dados na VM que serão replicados.

### <a name="configure-networks-and-ip-addresses"></a>Configurar redes e endereços IP

Você pode definir o endereço IP de destino:

- Se você não fornecer um endereço, a falha sobre a VM usa DHCP.
- Se você definir um endereço que não esteja disponível no failover, o failover não funcionará.
- Se o endereço estiver disponível na rede de failover de teste, você poderá usar o mesmo endereço IP de destino para failover de teste.

O número de adaptadores de rede é ditado pelo tamanho que você especifica para a máquina virtual de destino, da seguinte forma:

- Se o número de adaptadores de rede na máquina virtual de origem for menor ou igual ao número de adaptadores permitidos para o tamanho da VM alvo, o alvo tem o mesmo número de adaptadores que a fonte.
- Se o número de adaptadores para a máquina virtual de origem exceder o número permitido para o tamanho da VM alvo, o tamanho máximo do tamanho do alvo será usado. Por exemplo, se uma máquina virtual de origem tem dois adaptadores de rede e o tamanho da VM de destino suporta quatro, a máquina virtual de destino tem dois adaptadores. Se a VM de origem tiver dois adaptadores, mas o tamanho do destino só suportar um, a VM alvo tem apenas um adaptador.
- Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede. Além disso, o primeiro adaptador mostrado na lista torna-se o adaptador de rede padrão na máquina virtual Do Zure.

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Os clientes do Microsoft Software Assurance podem usar o Azure Hybrid Benefit para economizar nos custos de licenciamento de computadores Windows Server que são migrados para o Azure. O benefício também se aplica à recuperação de desastres do Azure. Se você for elegível, você pode atribuir o benefício à máquina virtual que o Site Recovery cria se houver um failover.

1. Vá para as **propriedades de Computador e Rede** da máquina virtual replicada.
1. Responda quando perguntado se você tem uma licença do Windows Server que o torna elegível para o Azure Hybrid Benefit.
1. Confirme se você tem uma licença elegível do Windows Server com garantia de software que você pode usar para aplicar o benefício à VM que será criada no failover.
1. Salve as configurações para a máquina virtual replicada.

[Saiba mais](https://azure.microsoft.com/pricing/hybrid-benefit/) sobre o Azure Hybrid Benefit.

## <a name="next-steps"></a>Próximas etapas

Depois que a máquina virtual atingir um estado protegido, tente um [failover](site-recovery-failover.md) para verificar se seu aplicativo aparece no Azure.

- [Saiba mais](site-recovery-manage-registration-and-protection.md) sobre como limpar as configurações de registro e proteção para desativar a replicação.
- [Saiba mais](vmware-azure-disaster-recovery-powershell.md) sobre como automatizar a replicação de suas máquinas virtuais usando o PowerShell.
