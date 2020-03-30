---
title: Arquitetura de recuperação de desastres de servidor físico na recuperação do site do Azure
description: Este artigo fornece uma visão geral dos componentes e da arquitetura usados durante a recuperação de desastres de servidores físicos locais para o Azure com o serviço Azure Site Recovery.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162830"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Servidor físico para a arquitetura de recuperação de desastre do Azure

Este artigo descreve a arquitetura e os processos usados quando você faz a replicação, o failover e a recuperação de servidores Windows e Linux físicos entre um site local e o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

## <a name="architectural-components"></a>Componentes de arquitetura

A tabela e o gráfico a seguir fornecem uma visão de alto nível dos componentes usados para replicação física do servidor no Azure.

| **Componente** | **Exigência** | **Detalhes** |
| --- | --- | --- |
| **Azure** | Uma assinatura do Azure e uma rede Azure. | Os dados replicados de máquinas físicas locais são armazenados em discos gerenciados pelo Azure. As VMs do Azure são criadas com os dados replicados quando você faz um failover do local para o Azure. As VMs do Azure se conectam à rede virtual do Azure quando são criadas. |
| **Servidor de processos** | Instalado por padrão juntamente com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.<br/><br/> O servidor em processo também instala o serviço de Mobilidade nos servidores que você deseja replicar.<br/><br/> À medida que a implantação cresce, você pode adicionar outros servidores de processo separados para lidar com volumes maiores de tráfego de replicação. |
| **Servidor de destino mestre** | Instalado por padrão juntamente com o servidor de configuração. | Lida com dados de replicação durante a redução do Azure.<br/><br/> Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback. |
| **Servidores replicados** | O serviço de Mobilidade é instalado em cada servidor que você deseja replicar. | É recomendável que você permita a instalação automática do servidor em processo. Ou, você pode instalar o serviço manualmente ou usar um método de implantação automatizado, como o Gerenciador de Configuração. |

**Arquitetura do físico para o Azure**

![Componentes](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Configure a implantação, incluindo os componentes locais e os componentes do Azure. No cofre dos Serviços de Recuperação, você especifica a origem e o destino da replicação, configura o servidor de configuração, cria uma política de replicação e habilita a replicação.
1. As máquinas se replicam usando a política de replicação e uma cópia inicial dos dados do servidor é replicada no armazenamento Do Zure.
1. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações rastreadas para uma máquina são mantidas em um arquivo com a extensão _.hrl._
   - As máquinas se comunicam com o servidor de configuração na porta HTTPS 443 de entrada, para o gerenciamento de replicação.
   - As máquinas enviam dados de replicação para o servidor de processo na porta HTTPS 9443 de entrada (pode ser modificada).
   - O servidor de configuração orquestra o gerenciamento de replicação com o Azure sobre a porta HTTPS 443.
   - O servidor de processo recebe dados de máquinas de origem, otimiza e criptografa e os envia para o armazenamento Do Zure pela porta HTTPS 443.
   - Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004. Várias VMS serão usadas se você agrupar vários computadores em grupos de replicação que compartilham pontos de recuperação consistentes com o aplicativo e com falhas ao realizar o failover. Esses grupos são úteis se as máquinas estiverem executando a mesma carga de trabalho e precisarem ser consistentes.
1. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure, pela Internet. Como alternativa, você pode usar o [emparelhamento público](../expressroute/about-public-peering.md) do Azure ExpressRoute.

   > [!NOTE]
   > A replicação não é suportada através de uma VPN site-to-site a partir de um site local ou [peering privado](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)do Azure ExpressRoute .

**Processo de replicação do físico para o Azure**

![Processo de replicação](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de failover e failback

Depois que a replicação for configurada, você pode executar uma broca de recuperação de desastres (failover de teste) para verificar se tudo funciona como esperado. Então, você pode falhar e falhar quando necessário. Considere os itens a seguir:

- Não há suporte para failover planejado.
- Falha ao refazer uma VMM VMware no local é necessária. Você precisa de uma infra-estrutura VMware no local, mesmo quando você replica servidores físicos no local para o Azure.
- Faça failover de uma única máquina ou crie planos de recuperação para fazer failover de várias máquinas virtuais juntas.
- Ao executar um failover, as VMs do Azure são criadas com base nos dados replicados no Armazenamento do Azure.
- Depois que o failover inicial é acionado, você o compromete a começar a acessar a carga de trabalho da VM do Azure.
- Quando o site primário local estiver disponível novamente, você poderá executar o failback.
- Configure uma infra-estrutura de failback que inclua:
  - **Servidor de processo temporário no Azure**: Para falhar no Azure, você configura uma VM Do Azure para atuar como um servidor de processo, para lidar com a replicação do Azure. Você pode excluir este VM após falhas de acabamento.
  - **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou Azure ExpressRoute) da rede do Azure para o site local.
  - **Servidor de destino mestre separado**: Por padrão, o fail back é manipulado pelo servidor de destino mestre que foi instalado com o servidor de configuração na VMWare VM. Se você precisar reprovar grandes volumes de tráfego, você deve configurar um servidor de destino principal no local separado.
  - **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. A política foi criada automaticamente quando você criou sua política de replicação do local para o Azure.
  - **Infra-estrutura VMware**: Para falhar, você precisa de uma infra-estrutura VMware. Você não pode realizar o failback para um servidor físico.
- Depois que os componentes estiverem no lugar, a falha ocorre em três estágios:
  - **Estágio 1**: Reproteja as VMs do Azure para que elas se reproduzam do Azure de volta para as VMMs VMware no local.
  - **Fase 2**: Executar um failover para o local no local.
  - **Estágio 3**: Depois que as cargas de trabalho falharem, você revia a replicação.

**Failback de VMware do Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Próximas etapas

Para configurar a recuperação de desastres para servidores físicos no Azure, consulte o [guia de como fazer](physical-azure-disaster-recovery.md).
