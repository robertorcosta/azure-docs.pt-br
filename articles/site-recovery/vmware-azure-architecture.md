---
title: Arquitetura de recuperação de desastres VMware VM na Recuperação do Site do Azure
description: Este artigo fornece uma visão geral dos componentes e da arquitetura utilizados durante a recuperação de desastre de VMs do VMware locais no Azure com o Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 77b4dd4c0efbe6d03e64865f18c2c87614aaecb5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632517"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Arquitetura de recuperação de desastre do VMware para o Azure

Este artigo descreve a arquitetura e os processos usados quando você implanta a replicação de recuperação de desastre, o failover e a recuperação de VMs (máquinas virtuais) do VMware entre um site local da VMware e o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de arquitetura

A tabela e o gráfico a seguir fornecem uma visão geral dos componentes usados para recuperação de desastre do VMware para o Azure.

**Componente** | **Exigência** | **Detalhes**
--- | --- | ---
**Azure** | Uma assinatura do Azure, a conta do Azure Storage para cache, disco gerenciado e rede Azure. | Os dados replicados das VMs no local são armazenados no armazenamento Do Zure. As VMs do Azure são criadas com os dados replicados quando você faz um failover do local para o Azure. As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Máquina do servidor de configuração** | Uma única máquina local. É recomendável executar como uma VM de VMware que possa ser implantada desde um modelo de OVF baixado.<br/><br/> A máquina executa todos os componentes do Site Recovery locais, que incluem o servidor de configuração, servidor de processo e o servidor de destino mestre. | **Servidor de configuração**: coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.<br/><br/> **Servidor de processo**: Instalado por padrão no servidor de configuração. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Microsoft Azure. O servidor de processo também instala o Serviço de Mobilidade do Azure Site Recovery nas VMs que você deseja replicar e executa a descoberta automática de máquinas locais. À medida que a implantação cresce, você pode adicionar outros servidores de processo separados para lidar com volumes maiores de tráfego de replicação.<br/><br/> **Servidor de destino mestre**: instalado por padrão no servidor de configuração. Lida com os dados de replicação durante o failback do Azure. Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback.
**Servidores VMware** | VMs de VMware são hospedadas em servidores ESXi do vSphere locais. Recomendamos um servidor vCenter para gerenciar os hosts. | Durante a implantação do Site Recovery, você pode adicionar servidores VMware no cofre dos Serviços de Recuperação.
**Computadores replicados** | O Serviço de Mobilidade é instalado em cada VM de VMware que você replicar. | É recomendável permitir a instalação automática do servidor em processo. Alternativamente, você pode instalar o serviço manualmente ou usar um método de implantação automatizado, como o Gerenciador de Configuração.

**Arquitetura do VMware para o Azure**

![Componentes](./media/vmware-azure-architecture/arch-enhanced.png)


## <a name="replication-process"></a>Processo de replicação

1. Ao habilitar a replicação para uma VM, a replicação inicial para o armazenamento do Azure começa, usando a política de replicação especificada. Observe o seguinte:
    - Para as VMs de VMware, a replicação é em nível de bloco, quase contínua, usando o agente do serviço Mobilidade em execução na VM.
    - Qualquer configuração de política de replicação é aplicada:
        - **Limiar de RPO**. Essa configuração não afeta a replicação. Isso ajuda no monitoramento. Um evento é gerado e, opcionalmente, um email enviado, se o RPO atual exceder o limite que você especificar.
        - **Retenção de ponto de recuperação**. Essa configuração especifica o tempo retroativo da recuperação quando ocorrer uma interrupção. A retenção máxima no armazenamento Premium é de 24 horas. No armazenamento padrão, são 72 horas. 
        - **Instantâneos consistentes com o aplicativo**. O instantâneo consistente com o aplicativo pode ser tomado a cada 1 a 12 horas, dependendo das necessidades do seu aplicativo. Instantâneos são instantâneos de blob padrão do Azure. O agente de Mobilidade executando em uma VM solicita um instantâneo de VSS de acordo com essa configuração e indica esse ponto no tempo como um ponto consistente do aplicativo no fluxo de replicação.

2. O tráfego é replicado para pontos de extremidade públicos do Armazenamento do Microsoft Azure pela Internet. Alternativamente, você pode usar o Azure ExpressRoute com [o peering da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Não há suporte para replicação de tráfego através de uma VPN (rede virtual privada) site a site de um site local para o Azure.
3. A operação inicial de replicação garante que dados inteiros na máquina no momento da replicação de habilitação sejam enviados ao Azure. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações controladas de uma máquina são enviadas para o servidor de processo.
4. A comunicação ocorre da seguinte maneira:

    - As VMs se comunicam com o servidor de configuração local na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - O servidor de configuração coordena a replicação com o Azure pela porta HTTPS 443 de saída.
    - As VMs que estão sendo replicadas enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta 9443 HTTPS de entrada. Essa porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza e criptografa e os envia para o armazenamento Azure na porta 443.
5. Os dados de replicação registram primeiro a terra em uma conta de armazenamento de cache no Azure. Esses logs são processados e os dados são armazenados em um Disco Gerenciado do Azure (chamado de disco de sementes asr). Os pontos de recuperação são criados neste disco.

**Processo de replicação do VMware para o Azure**

![Processo de replicação](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Processo de ressincronização

1. Às vezes, durante a replicação inicial ou durante a transferência de alterações delta, pode haver problemas de conectividade de rede entre a máquina de origem para o servidor de processo ou entre o servidor de processo para o Azure. Qualquer uma delas pode levar a falhas na transferência de dados para o Azure momentaneamente.
2. Para evitar problemas de integridade de dados e minimizar os custos de transferência de dados, a Recuperação do Site marca uma máquina para ressincronização.
3. Uma máquina também pode ser marcada para ressincronização em situações como seguir para manter a consistência entre a máquina de origem e os dados armazenados no Azure
    - Se uma máquina sofrer um desligamento da força
    - Se uma máquina sofrer alterações de configuração como redimensionamento de disco (modificando o tamanho do disco de 2 TB para 4 TB)
4. A ressincronização envia apenas dados delta para o Azure. Transferência de dados entre o local e o Azure, minimizado pela computação de resumos de dados entre a máquina de origem e os dados armazenados no Azure.
5. Por padrão, a ressincronização está agendada para execução automática fora das horas de trabalho. Se você não quiser aguardar a ressincronização fora do horário de trabalho, você poderá ressincronizar uma VM manualmente. Para isso, vá para o portal Azure, selecione o VM > **Resynchronize**.
6. Se a ressincronização padrão falhar fora do horário de expediente e uma intervenção manual for necessária, então um erro será gerado na máquina específica no portal Azure. Você pode resolver o erro e acionar a ressincronização manualmente.
7. Após a conclusão da ressincronização, a replicação das alterações delta será retomada.

## <a name="failover-and-failback-process"></a>Processo de failover e failback

Depois que a replicação é configurada e você executou uma simulação de recuperação de desastre (failover de teste) para verificar se tudo está funcionando conforme o esperado, você poderá executar failover e failback conforme necessário.

1. É possível fazer failover de uma única máquina ou criar planos de recuperação para fazer failover de várias VMs ao mesmo tempo. As vantagens de um plano de recuperação em vez de failover de única máquina incluem:
    - Você pode modelar dependências do aplicativo, incluindo todas as VMs entre o aplicativo em um único plano de recuperação.
    - Você pode adicionar scripts, runbooks do Azure e pausar para ações manuais.
2. Depois de disparar o failover inicial, você confirma-o para começar a acessar a carga de trabalho da VM do Azure.
3. Quando o site primário local estiver disponível novamente, você poderá se preparar executar o failback. Para executar o failback, você precisa configurar uma infraestrutura de failback, incluindo:

    * **Servidor em processo temporário no Azure**: para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor em processo, a fim de manipular a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
    * **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou ExpressRoute) da rede do Azure para o site local.
    * **Servidor de destino mestre separado**: por padrão, o servidor de destino mestre que foi instalado com o servidor de configuração na VM do VMware local manipula o failback. Se for necessário fazer failback de grandes volumes de tráfego, configure um servidor de destino mestre local separado para essa finalidade.
    * **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. Essa política é criada automaticamente quando você cria uma política de replicação de local para o Azure.
4. Depois que os componentes estão em vigor, o failback ocorre em três ações:

    - Estágio 1: proteja novamente as VMs do Azure para que elas comecem a replicação para as VMs de VMware locais.
    -  Estágio 2: execute um failover para o site local.
    - Etapa 3: depois que as cargas de trabalho fizeram failback, você reabilitará a replicação para as VMs locais.
    
 
**Failback de VMware do Azure**

![Failback](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Próximas etapas

Siga [este tutorial](vmware-azure-tutorial.md) para habilitar a replicação de VMware para o Azure.
