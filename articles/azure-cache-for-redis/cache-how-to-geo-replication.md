---
title: Como configurar a replicação geográfica do Cache Do Azure para Redis | Microsoft Docs
description: Saiba como replicar as instâncias do Cache do Azure para Redis entre regiões geográficas.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129423"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Como configurar a replicação geográfica do Azure Cache para Redis

A replicação geográfica fornece um mecanismo para vincular duas instâncias do Cache do Azure para Redis de camada Premium. Um cache é escolhido como o cache vinculado principal e o outro como cache vinculado secundário. O cache vinculado secundário se torna somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache entre regiões do Azure. Este artigo fornece um guia para configurar a replicação geográfica para o cache Azure de nível Premium para instâncias Redis.

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de replicação geográfica

Para configurar a replicação geográfica entre dois caches, os seguintes pré-requisitos devem ser cumpridos:

- Ambos os caches são caches [de nível Premium.](cache-premium-tier-intro.md)
- Ambos os caches estão na mesma assinatura do Azure.
- O cache vinculado secundário é o mesmo tamanho de cache ou um tamanho de cache maior do que o cache vinculado principal.
- Ambos os caches são criados e em um estado de execução.

Alguns recursos não são suportados com geo-replicação:

- A persistência não é suportada com geo-replicação.
- O clustering é suportado se ambos os caches tiverem o cluster ativado e tiverem o mesmo número de fragmentos.
- Os caches no mesmo VNET são suportados.
- Caches em VNETs diferentes são suportados com ressalvas. Veja [Posso usar a replicação geográfica com meus caches em um VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Depois que a replicação geográfica for configurada, as seguintes restrições se aplicam ao seu par de cache vinculado:

- O cache vinculado secundário é somente leitura; você pode ler dele, mas não pode gravar dados nele. 
- Todos os dados que estavam no cache vinculado secundário antes do link ser adicionado são removidos. Se a replicação geográfica for removida posteriormente, os dados replicados permanecerão no cache vinculado secundário.
- Você não pode [dimensionar](cache-how-to-scale.md) nenhum dos caches enquanto os caches estão vinculados.
- Você não pode [alterar o número de fragmentos](cache-how-to-premium-clustering.md) se o cache tiver o cluster ativado.
- Você não pode habilitar a persistência em nenhum dos caches.
- Você pode [exportar](cache-how-to-import-export-data.md#export) de qualquer cache.
- Você não pode [importar](cache-how-to-import-export-data.md#import) para o cache vinculado secundário.
- Você não pode excluir o cache vinculado ou o grupo de recursos que os contém até desvincular os caches. Para obter mais informações, consulte [Por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se os caches estiverem em diferentes regiões, os custos de saída de rede se aplicam aos dados movidos entre as regiões. Para obter mais informações, consulte [Quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- O failover automático não ocorre entre o cache vinculado primário e secundário. Para obter mais informações e informações sobre como falhar em um aplicativo cliente, consulte [Como funciona a falha no cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicionar um link de geo-replicação

1. Para vincular dois caches juntos para replicação geográfica, clique em **Geo-replicação** do menu Recurso do cache que você pretende ser o cache principal vinculado. Em seguida, clique em Adicionar link de **replicação** de cache da lâmina de replicação geo-replicação. **Geo-replication**

    ![Adicionar Link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome do cache secundário pretendido na lista **de caches compatíveis.** Se o cache secundário não for exibido na lista, verifique se os [pré-requisitos de geo-replicação](#geo-replication-prerequisites) para o cache secundário serão atendidos. Para filtrar os caches por região, clique na região no mapa para exibir apenas esses caches na lista **de caches compatíveis.**

    ![Caches compatíveis de replicação geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Você também pode iniciar o processo de vinculação ou visualizar detalhes sobre o cache secundário usando o menu de contexto.

    ![Menu de contexto de replicação geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Clique em **Vincular** para vincular dois caches e iniciar o processo de replicação.

    ![Vincular caches](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Você pode exibir o andamento do processo de replicação na folha **Replicação geográfica**.

    ![Status da vinculação](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Você também pode exibir o status de vinculação na folha **Visão geral** para os caches primário e secundário.

    ![Status do cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Quando o processo de replicação é concluído, o **Status da vinculação** é alterado para **Êxito**.

    ![Status do cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    O cache vinculado principal permanece disponível para uso durante o processo de vinculação. O cache vinculado secundário não está disponível até que o processo de vinculação seja concluído.

## <a name="remove-a-geo-replication-link"></a>Remova um link de geo-replicação

1. Para remover o link entre dois caches e interromper a replicação geográfica, clique em **Desvincular caches** da lâmina **de geo-replicação.**
    
    ![Desvincular caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Quando o processo de desvinculação for concluído, o cache secundário estará disponível para leituras e gravações.

>[!NOTE]
>Quando o link de geo-replicação é removido, os dados replicados do cache vinculado principal permanecem no cache secundário.
>
>

## <a name="geo-replication-faq"></a>Perguntas frequentes de replicação geográfica

- [Posso usar a replicação geográfica com um cache de nível Padrão ou Básico?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [O cache está disponível para uso durante o processo de vinculação ou desvinculação?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Posso vincular mais de dois caches juntos?](#can-i-link-more-than-two-caches-together)
- [Posso vincular dois caches de assinaturas do Azure diferentes?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Posso vincular dois caches com tamanhos diferentes?](#can-i-link-two-caches-with-different-sizes)
- [Posso usar a geo-replicação com clustering ativado?](#can-i-use-geo-replication-with-clustering-enabled)
- [Posso usar a geo-replicação com meus caches em um VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [O que é o cronograma de replicação para replicação geográfica do Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto tempo leva a replicação de replicação geográfica?](#how-long-does-geo-replication-replication-take)
- [O ponto de recuperação de replicação é garantido?](#is-the-replication-recovery-point-guaranteed)
- [Posso usar o PowerShell ou o Azure CLI para gerenciar a replicação geográfica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Qual região devo usar para meu cache vinculado secundário?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Como funciona o failover para o cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Posso usar a replicação geográfica com um cache de nível Padrão ou Básico?

Não, a replicação geográfica só está disponível para caches de nível Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>O cache está disponível para uso durante o processo de vinculação ou desvinculação?

- Ao vincular, o cache vinculado principal permanece disponível enquanto o processo de vinculação é concluído.
- Ao vincular, o cache vinculado secundário não está disponível até que o processo de vinculação seja concluído.
- Ao desvincular, ambos os caches permanecem disponíveis enquanto o processo de desvinculação é concluído.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso vincular mais de dois caches juntos?

Não, você só pode ligar dois caches juntos.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Posso vincular dois caches de assinaturas do Azure diferentes?

Não, ambos os caches devem estar na mesma assinatura do Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Posso vincular dois caches com tamanhos diferentes?

Sim, contanto que o cache vinculado secundário seja maior do que o cache vinculado primário.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Posso usar a geo-replicação com clustering ativado?

Sim, desde que ambos os caches tenham o mesmo número de fragmentos.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Posso usar a geo-replicação com meus caches em um VNET?

Sim, a geo-replicação de caches em VNETs é suportada com ressalvas:

- Há suporte para a replicação geográfica entre caches na mesma VNET.
- A replicação geográfica entre caches em VNETs diferentes também é suportada.
  - Se os VNETs estiverem na mesma região, você poderá conectá-los usando [o peering VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou uma [conexão VPN Gateway VNET-to-VNET](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Se os VNETs estiverem em diferentes regiões, a replicação geográfica usando o peering VNET não é suportada devido a uma restrição com balanceadores de carga interna básicas. Para obter mais informações sobre as restrições de peering vnet, consulte [Rede Virtual - Peering - Requisitos e restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). A solução recomendada é usar uma conexão VPN Gateway VNET-to-VNET.

Usando [este modelo Do Azure,](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)você pode implantar rapidamente dois caches geo-replicados em um VNET conectado a uma conexão VPN Gateway VNET-to-VNET.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>O que é o cronograma de replicação para replicação geográfica do Redis?

A replicação é contínua e assíncrona e não acontece em um horário específico. Todas as gravações feitas para o primário são instantaneamente e assíncronamente replicadas no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo leva a replicação de replicação geográfica?

A replicação é incremental, assíncrona e contínua e o tempo demorado não é muito diferente da latência entre as regiões. Em certas circunstâncias, o cache secundário pode ser necessário para fazer uma sincronização completa dos dados das primárias. O tempo de replicação neste caso depende do número de fatores como: carregar no cache principal, largura de banda da rede disponível e latência inter-região. Encontramos tempo de replicação para um par completo de 53 GB geo-replicado pode ser em qualquer lugar entre 5 a 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação de replicação é garantido?

Para caches em um modo geo-replicado, a persistência é desativada. Se um par replicado geoérgrafe for desvinculado, como um failover iniciado pelo cliente, o cache vinculado secundário manterá seus dados sincronizados até esse ponto de tempo. Nenhum ponto de recuperação é garantido em tais situações.

Para obter um ponto de recuperação, [exporte](cache-how-to-import-export-data.md#export) de qualquer cache. Mais [tarde, você](cache-how-to-import-export-data.md#import) pode importar para o cache vinculado principal.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso usar o PowerShell ou o Azure CLI para gerenciar a replicação geográfica?

Sim, a replicação geográfica pode ser gerenciada usando o portal Azure, PowerShell ou Azure CLI. Para obter mais informações, consulte os [docs powershell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) ou [os docs da Cli do Azure](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa para replicar meus dados entre regiões do Azure?

Ao usar a replicação geográfica, os dados do cache vinculado principal são replicados no cache vinculado secundário. Não há custo para a transferência de dados se os dois caches vinculados estão na mesma região. Se os dois caches vinculados estiverem em regiões diferentes, a taxa de transferência de dados é o custo de saída de rede de dados que se deslocam em qualquer região. Para obter mais informações, consulte [Detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Por que a operação falhou quando tentei excluir meu cache vinculado?

Os caches replicados geografos e seus grupos de recursos não podem ser excluídos enquanto estiver vinculado até que você remova o link de replicação geográfica. Se você tentar excluir o grupo de recursos que contém um ou ambos os caches vinculados, outros recursos no grupo de recursos serão excluídos, mas o grupo de recursos permanecerá no estado `deleting` e quaisquer caches vinculados no grupo de recursos permanecerão no estado `running`. Para excluir completamente o grupo de recursos e os caches vinculados dentro dele, desvincule os caches descritos em [Remover um link de geo-replicação](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Qual região devo usar para meu cache vinculado secundário?

Em geral, é recomendado que seu cache exista na mesma região do Azure que o aplicativo que o acessa. Para aplicações com regiões primárias e de recuo separadas, recomenda-se que existam caches primários e secundários nessas mesmas regiões. Para obter mais informações sobre regiões emparelhadas, consulte [Melhores práticas – regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como funciona o failover para o cache vinculado secundário?

O failover automático em todas as regiões do Azure não é suportado para caches replicados geografamente. Em um cenário de recuperação de desastres, os clientes devem trazer toda a pilha de aplicativos de forma coordenada em sua região de backup. Permitir que componentes individuais do aplicativo decidam quando mudar para seus backups por conta própria pode afetar negativamente o desempenho. Um dos principais benefícios da Redis é que é uma loja de baixa latência. Se o aplicativo principal do cliente estiver em uma região diferente do seu cache, o tempo adicional de ida e volta teria um impacto perceptível no desempenho. Por essa razão, evitamos falhar automaticamente devido a problemas de disponibilidade transitória.

Para iniciar um failover iniciado pelo cliente, primeiro desvincule os caches. Em seguida, altere seu cliente Redis para usar o ponto final de conexão do cache secundário (anteriormente vinculado). Quando os dois caches são desvinculados, o cache secundário torna-se um cache de leitura-gravação regular novamente e aceita solicitações diretamente dos clientes Redis.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [Camada Premium do Cache do Azure para Redis](cache-premium-tier-intro.md).
