---
title: Mova a máquina virtual para outra região (Azure Site Recovery)
description: Saiba como migrar sua máquina virtual SQL Server de uma região para outra dentro do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022304"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Mova o VM do SQL Server para outra região dentro do Azure com os serviços de recuperação do site do Azure

Este artigo ensina como usar o Azure Site Recovery para migrar sua máquina virtual (VM) do SQL Server de uma região para outra dentro do Azure. 

Mover uma VM do SQL Server para uma região diferente requer fazer o seguinte:
1. [**Preparação**](#prepare-to-move): Confirme se tanto o VM do servidor SQL de origem quanto a região alvo estão adequadamente preparados para a mudança. 
1. [**Configuração**](#configure-azure-site-recovery-vault): Mover o VM do Servidor SQL requer que ele seja um objeto replicado dentro do cofre de recuperação do site do Azure. Você precisa adicionar seu VM do Servidor SQL ao cofre de recuperação do site Do Zure. 
1. [**Teste**](#test-move-process): A migração do VM do Servidor SQL requer a falha da região de origem para a região de destino replicada. Para garantir que o processo de movimento seja bem-sucedido, você precisa primeiro testar que o VM do Servidor SQL pode falhar com sucesso na região de destino. Isso ajudará a expor quaisquer problemas e evitá-los ao realizar o movimento real. 
1. [**Movimentação**](#move-the-sql-server-vm): Uma vez que o failover do teste tenha passado, e você saiba que está seguro para migrar o VM do Servidor SQL, você pode executar a movimentação da VM para a região de destino. 
1. [**Limpeza**](#clean-up-source-resources): Para evitar cobranças, remova o VM do Servidor SQL do cofre e quaisquer recursos desnecessários que sobraram no grupo de recursos. 

## <a name="verify-prerequisites"></a>Verificar pré-requisitos 

- Confirme se a mudança da região de origem para a região alvo [é suportada](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Revise a [arquitetura e os componentes](../../../site-recovery/azure-to-azure-architecture.md) do cenário, bem como as [limitações e requisitos](../../../site-recovery/azure-to-azure-support-matrix.md)de suporte. 
- Verificar permissões da conta. Se você criou a conta gratuita do Azure, você será o administrador da assinatura. Se você não for o administrador da assinatura, trabalhe com o administrador para atribuir as permissões necessárias. Para habilitar a replicação de uma VM e copiar dados usando o Azure Site Recovery, você deve ter: 
    - Permissões para criar uma VM. A função de *contribuinte da máquina virtual* tem essas permissões, que incluem: 
        - Permissões para criar uma VM no grupo de recursos selecionado. 
        - Permissões para criar uma VM na rede virtual selecionada. 
        - Permissões para escrever na conta de armazenamento selecionada. 
      - Permissões para gerenciar as operações do Azure Site Recovery. A função *Contribuinte de Recuperação do Site* tem todas as permissões necessárias para gerenciar as operações de recuperação de site em um cofre de Serviços de Recuperação.  

## <a name="prepare-to-move"></a>Prepare-se para se mover
Prepare tanto a VM do Servidor SQL de origem quanto a região alvo para a movimentação. 

### <a name="prepare-the-source-sql-server-vm"></a>Prepare a fonte SQL Server VM

- Certifique-se de que todos os certificados raiz mais recentes estão na VM do Servidor SQL que você deseja mover. Se os certificados raiz mais recentes não estiverem lá, as restrições de segurança impedirão a cópia de dados para a região de destino. 
- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados raiz confiáveis estejam na máquina. Em um ambiente desconectado, siga o processo padrão de atualização de update e certificado do Windows para sua organização. 
- Para VMs do Linux, siga as diretrizes fornecidas pelo distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM. 
- Certifique-se de que não está usando um proxy de autenticação para controlar a conectividade da rede para as VMs que você deseja mover. 
- Se a VM que você está tentando mover não tiver acesso à Internet ou estiver usando um proxy de firewall para controlar o acesso de saída, verifique os requisitos. 
- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Isso inclui, mas não se limita a, balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos. 

### <a name="prepare-the-target-region"></a>Preparar a região de destino

- Verifique se a assinatura do Azure permite criar VMs na região de destino utilizada para recuperação de desastre. Contate o suporte para habilitar a cota necessária. 
- Certifique-se de que sua assinatura tenha recursos suficientes para suportar VMs com tamanho que correspondam às VMs de origem. Se você estiver usando a Recuperação do Site para copiar dados para o destino, a Recuperação do Site escolhe o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino. 
- Certifique-se de criar um recurso de destino para cada componente identificado no layout de rede de origem. Essa etapa é importante para garantir que as VMs tenham toda a funcionalidade e recursos na região de destino que você tinha na região de origem. 
    - O Azure Site Recovery descobre e cria automaticamente uma rede virtual quando você habilita a replicação para a VM de origem. Você também pode pré-criar uma rede e atribuí-la à VM no fluxo de usuário para habilitar a replicação. Você precisa criar manualmente quaisquer outros recursos na região alvo.
- Para criar os recursos de rede mais utilizados e relevantes com base na configuração da VM de origem, consulte a documentação a seguir: 
    - [Grupos de segurança de rede](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Balanceador de carga](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Endereço IP público](../../../virtual-network/virtual-network-public-ip-address.md)
    - Para obter quaisquer componentes adicionais de rede, consulte a [documentação de rede](../../../virtual-network/virtual-networks-overview.md).
- Crie manualmente uma rede de não produção na região de destino, caso você queira testar a configuração antes de executar a movimentação final para a região de destino. Essa etapa é recomendável porque garante interferência mínima na rede de produção. 

## <a name="configure-azure-site-recovery-vault"></a>Configurar o cofre de recuperação do site do Azure

As etapas a seguir mostram como usar o Azure Site Recovery para copiar dados para a região de destino. Crie o cofre dos Serviços de Recuperação em qualquer outra região que não seja a região de origem. 

1. Inscreva-se no [portal Azure](https://portal.azure.com). 
1. Escolha **criar um recurso** no canto superior esquerdo do painel de navegação. 
1. Selecione **as ferramentas de gerenciamento de & de TI** e selecione Backup e Recuperação de **Sites**. 
1. Na guia **Básico,** em **detalhes do Projeto,** crie um novo grupo de recursos na região de destino ou selecione um grupo de recursos existente na região de destino. 
1. Em **Detalhes de instância,** especifique um nome para o cofre e selecione a **região** de destino a partir da queda. 
1. Selecione **'Revisar + Criar** para criar seu cofre de Serviços de Recuperação'. 
1. Selecione **Todos os serviços** no canto superior esquerdo do `recovery services`painel de navegação e no tipo caixa de pesquisa . 
1. (Opcionalmente) Selecione a estrela ao lado **dos cofres dos Serviços de Recuperação** para adicioná-la à sua barra de navegação rápida. 
1. Selecione **os cofres dos serviços de recuperação** e selecione o cofre serviços de recuperação criado. 
1. No **painel Visão geral,** selecione **Replicar**. 

   ![Configurar replicação](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Selecione **Origem** e selecione **O Azure** como fonte. Selecione os valores apropriados para os outros campos de baixa, como o local para suas VMs de origem. Somente grupos de recursos localizados na região de **localização fonte** serão visíveis no campo grupo de **recursos Fonte.** 
1. Selecione **máquinas virtuais** e escolha as máquinas virtuais que deseja migrar. Selecione **OK** para salvar sua seleção de VM. 
1. Selecione **Configurações**e escolha **o local** de destino na queda. Este deve ser o grupo de recursos que você preparou mais cedo. 
1. Depois de ter replicação personalizada, selecione **Criar recursos de destino** para criar os recursos no novo local. 
1. Uma vez concluída a criação de recursos, **selecione Habilitar a replicação** para iniciar a replicação da VM do Servidor SQL da origem para a região de destino.
1. Você pode verificar o status da replicação navegando até o cofre de recuperação, selecionando **itens replicados** e visualizando o **Status** do VM do servidor SQL. Um status de **Protegido** indica que a replicação foi concluída. 

   ![Verificar o status da replicação](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Processo de movimentação de teste
As etapas a seguir mostram como usar o Azure Site Recovery para testar o processo de mudança. 

1. Navegue até o **cofre dos Serviços de Recuperação** no portal [Azure](https://portal.azure.com) e selecione **itens replicados**. 
1. Selecione o VM do Servidor SQL que você gostaria de mover, verifique se a **Saúde de Replicação** aparece como **saudável** e, em seguida, selecione **Failover de teste**. 

   ![Failover de teste para sua VM](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. Na página **Failover de teste,** selecione o ponto de recuperação **mais recente consistente** do aplicativo para usar para o failover, pois esse é o único tipo de instantâneo que pode garantir a consistência dos dados do SQL Server. 
1. Selecione a rede virtual na **rede virtual Do Azure** e selecione **OK** para testar failover. 
   
   >[!IMPORTANT]
   > Recomendamos que você use uma rede VM Azure separada para o teste de failover. Não utilize a rede de produção que foi configurada quando você habilitou a replicação e que eventualmente poderá querer mover as VMs. 

1. Para monitorar o progresso, navegue até o cofre, selecione **trabalhos de recuperação de site** em **Monitoramento**e selecione o trabalho **de failover de teste** que está em andamento.

   ![Monitorar o progresso do teste de failover](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Assim que o teste estiver concluído, navegue até **máquinas Virtuais** no portal e revise a máquina virtual recém-criada. Certifique-se de que o VM do SQL Server está sendo executado, é dimensionado adequadamente e está conectado à rede apropriada. 
1. Exclua a VM que foi criada como parte do teste, pois a opção **Failover** será acinzentada até que os recursos de teste de failover sejam limpos. Navegue de volta para o cofre, **selecione itens replicados,** selecione o VM do servidor SQL e selecione **Failover de teste de limpeza**. Gravar e salvar quaisquer observações associadas ao teste na seção **Notas** e selecione a caixa de seleção ao lado de **Teste está concluída. Excluir máquinas virtuais failover de teste**. Selecione **OK** para limpar os recursos após o teste. 

   ![limpar itens após o teste de failover](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Mova o VM do Servidor SQL 
As etapas a seguir mostram como mover o VM do Servidor SQL da região de origem para a região de destino. 

1. Navegue até o cofre **dos Serviços de Recuperação,** **selecione Itens replicados,** selecione a VM e selecione **Failover**. 

   ![Iniciar failover](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. Selecione o ponto de recuperação **mais recente consistente** com o aplicativo em **Recovery Point**. 
1. Selecione a caixa de seleção ao lado **de Desligar a máquina antes de iniciar o failover**. A Recuperação do Site tentará desligar a VM de origem antes de acionar o failover. O failover continuará mesmo se o desligamento falhar. 
1. Selecione **OK** para iniciar o failover.
1. Você pode monitorar o processo de failover da mesma página **de trabalhos** de recuperação de site que você visualizou ao monitorar o teste de failover na seção anterior. 
1. Após a conclusão do trabalho, verifique se o VM do Servidor SQL aparece na região de destino como esperado. 
1. Navegue de volta para o cofre, **selecione Itens Replicados,** selecione a VM do servidor SQL e **selecione Comprometer-se** para concluir o processo de movimentação para a região de destino. Aguarde até que o trabalho de confirmação seja concluído. 
1. Registre seu VM do SQL Server com o provedor de recursos SQL VM para permitir a capacidade de gerenciamento de **máquinas virtuais SQL** no portal Azure e recursos associados ao provedor de recursos. Para obter mais informações, consulte [Registre o VM do Servidor SQL com o provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-rp.md). 

  > [!WARNING]
  > A consistência dos dados do SQL Server só é garantida com instantâneos consistentes com aplicativos. O **mais recente** instantâneo processado não pode ser usado para failover do SQL Server, pois um instantâneo de recuperação de falha não pode garantir a consistência dos dados do SQL Server. 

## <a name="clean-up-source-resources"></a>Limpar os recursos de origem
Para evitar cobranças, remova o VM do Servidor SQL do cofre e exclua quaisquer recursos associados desnecessários. 

1. Navegue de volta para o cofre de recuperação de **site,** selecione **itens replicados**e selecione o VM do Servidor SQL. 
1. Selecione **Desabilitar Replicação**. Selecione uma razão para desativar a proteção e selecione **OK** para desativar a replicação. 

   >[!IMPORTANT]
   > É importante realizar esta etapa para evitar ser cobrado pela replicação do Azure Site Recovery. 

1. Se você não tiver planos de reutilizar qualquer um dos recursos na região de origem, exclua todos os recursos de rede relevantes e contas de armazenamento correspondentes. 


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server em uma orientação de preços do Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


