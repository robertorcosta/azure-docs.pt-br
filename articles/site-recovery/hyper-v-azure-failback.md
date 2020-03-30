---
title: Fail back Hyper-V VMs do Azure com recuperação do site do Azure
description: Como refalhar os VMs Hyper-V em um site local do Azure com o Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281776"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Executar um failback de VMs Hyper-V

Este artigo descreve como reprovar as VMs do Azure que foram criadas após o failover de VMs Hyper-V de um site local para o Azure, com [o Azure Site Recovery](site-recovery-overview.md).

- Você falha em Hiper-V VMs do Azure executando um failover planejado do Azure para o site no local. Se a direção do failover for do Azure para o local, é considerado um failback.
- Como o Azure é um ambiente altamente disponível e as VMs estão sempre disponíveis, o failback do Azure é uma atividade planejada. Você pode planejar um pequeno tempo de inatividade para que as cargas de trabalho possam começar a ser executados no local novamente. 
- Failback planejado desliga as VMs no Azure e baixa as últimas alterações. Nenhuma perda de dados é esperada.

## <a name="before-you-start"></a>Antes de começar

1. [Revise os tipos de failback que](failover-failback-overview.md#hyper-v-reprotectionfailback) você pode usar - recuperação de localização original e recuperação de localização alternativa.
2. Certifique-se de que as VMs do Azure estão usando uma conta de armazenamento e discos não gerenciados. Não é suportado failback de VMs Hyper-V replicados usando discos gerenciados.
3. Verifique se o host Hyper-V no local (ou servidor VMM da Central de Sistema se estiver usando com a Recuperação do Site) está sendo executado e conectado ao Azure. 
4. Certifique-se de que failover e commit estão completos para as VMs. Você não precisa configurar nenhum componente específico de recuperação de site para failback de VMs Hyper-V do Azure.
5. O tempo necessário para completar a sincronização de dados e iniciar a VM no local dependerá de uma série de fatores. Para acelerar o download de dados, você pode configurar o agente microsoft recovery services para usar mais threads para paralelenear o download. [Saiba mais](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Failback para o local original

Para reprovar os VMs Hyper-V no Azure para a VM original no local, execute um failover planejado do Azure para o site local da seguinte forma:

1. No cofre > **itens replicados,** selecione a VM. Clique com o botão direito do mouse no **Failover planejado**> VM . Se você estiver falhando em um plano de recuperação, selecione o nome do plano e clique em **Failover** > **Planned Failover**.
2. Em **Confirmar Failover Planejado,** escolha a origem e os locais de destino. Observe a direção do failover. Se o failover do primário funcionou como esperado e todas as máquinas virtuais estiverem no local secundário, isso será apenas para informações.
3. Em **Sincronização de dados,** selecione uma opção:
    - **Sincronizar dados antes do failover (sincronizar apenas as alterações delta)**— Essa opção minimiza o tempo de inatividade das VMs à medida que sincroniza sem desligá-los.
        - **Fase 1**: Tira um instantâneo do Azure VM e copia-o para o host Hyper-V no local. O computador continua em execução no Azure.
        - **Fase 2**: Desliga a VM do Azure para que não ocorram novas alterações por lá. O conjunto final de alterações delta é transferido para o servidor local e a VM no local é iniciada.
    - **Sincronizar dados durante o failover apenas (download completo)**— Essa opção é mais rápida porque presumimos que a maior parte do disco foi alterada e não queremos gastar tempo calculando as somas de verificação. Essa opção não executará os cálculos de soma de verificação.
        - Ela executa um download do disco. 
        - Recomendamos que você use esta opção se estiver executando o Azure por um tempo (um mês ou mais) ou se a VM no local for excluída.

4. Somente para VMM, se a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia,** selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do Provedor no servidor VMM.
5. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
6. Se você selecionou a opção de sincronizar os dados antes do failover, depois que a sincronização inicial de dados estiver concluída e estiver pronto para desligar as máquinas virtuais no Azure, clique em **Jobs** > nome de trabalho > **Failover completo**. Isso faz o seguinte:
    - Desliga a máquina Azure.
    - Transfere as últimas alterações para a VM no local.
    - Começa a VM no local.
7. Agora você pode entrar na máquina VM no local para verificar se está disponível como esperado.
8. A máquina virtual está em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover.
9. Para concluir o failback, clique **em Reversão para** começar a replicar a VM no local para o Azure novamente.



## <a name="fail-back-to-an-alternate-location"></a>Failback para um local alternativo 

Falhe de volta a um local alternativo da seguinte forma:

1. Se você estiver configurando um novo hardware, instale uma [versão suportada do Windows](hyper-v-azure-support-matrix.md#replicated-vms)e a função Hyper-V na máquina.
2. Crie um comutador de rede virtual com o mesmo nome que você tinha no servidor original.
3. Em**Proteção** > \<de **Grupos de Proteção de Itens ProtegidosO** > grupo> -> \<VirtualMachineName>, selecione a VM que deseja falhar e selecione **Failover planejado**.
4. Em **Confirmar Failover planejado,** eleja **Criar máquina virtual no local se ela não existir**.
5. Em **Nome do host,** selecione o novo servidor de host Hyper-V no qual deseja colocar a VM.
6. Em **Sincronização de dados,** recomendamos que você selecione a opção de sincronizar os dados antes do failover. Isso minimiza o tempo de inatividade das VMs à medida que sincroniza sem desligá-las. Ele faz o seguinte:
    - **Fase 1**: Tira o instantâneo da VM Do Azure e copia-a para o host Hyper-V no local. O computador continua em execução no Azure.
    - **Fase 2**: Desliga a VM do Azure para que não ocorram novas alterações por lá. O conjunto final de alterações é transferido para o servidor local e a máquina virtual local é inicializada.
    
7. Clique na marca de seleção para iniciar o failover (failback).
8. Depois que a sincronização inicial terminar e você estiver pronto para encerrar a VM do Azure, clique em **Jobs** > \<planejado failover job> > **Complete Failover**. Isso desliga a máquina Azure, transfere as últimas alterações para a VM no local e a inicia.
9. Você pode entrar na VM no local para verificar se tudo está funcionando como esperado.
10. Clique **em Comprometer-se** para concluir o failover. Commit exclui a VM do Azure e seus discos e prepara a VM no local para ser protegida novamente.
10. Clique **em 'Reversão'** para começar a replicar a VM no local para o Azure. Apenas as mudanças delta desde que a VM foi desligada no Azure serão replicadas.

    > [!NOTE]
    > Se você cancelar o trabalho de failback durante a sincronização de dados, a VM no local estará em um estado corrompido. Isso porque a sincronização de dados copia os dados mais recentes dos discos VM do Azure para os discos de dados locais e, até que a sincronização seja concluída, os dados do disco podem não estar em um estado consistente. Se a VM no local for iniciada após o cancelamento da sincronização de dados, ela pode não ser inicializada. Neste caso, execute novamente o failover para concluir a sincronização de dados.


## <a name="next-steps"></a>Próximas etapas
Depois que a VM no local estiver se replicando para o Azure, você pode [executar outro failover](site-recovery-failover.md) para o Azure conforme necessário.
