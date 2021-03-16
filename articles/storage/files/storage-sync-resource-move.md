---
title: Sincronização de Arquivos do Azure movimentações de recursos e alterações de topologia
description: Saiba como mover recursos de sincronização entre os locatários do AAD (grupos de recursos, assinaturas e Azure Active Directory).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555201"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Mover Sincronização de Arquivos do Azure recursos para um grupo de recursos, uma assinatura ou um locatário do AAD diferentes

Este artigo descreve como fazer alterações no locatário do AAD (grupo de recursos, assinatura ou Azure Active Directory) para seus recursos de nuvem do Sincronização de Arquivos do Azure e contas de armazenamento do Azure.

Ao planejar fazer alterações nos Sincronização de Arquivos do Azure recursos de nuvem, é importante considerar os recursos de armazenamento ao mesmo tempo. Existem os seguintes recursos:

**Sincronização de Arquivos do Azure recursos (em ordem hierárquica)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Serviço de sincronização de armazenamento
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Servidor registrado
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Grupo de sincronização
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Ponto de extremidade de nuvem
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Ponto de extremidade do servidor

No Sincronização de Arquivos do Azure, o único recurso capaz de mover é o recurso de serviço de sincronização de armazenamento. Todos os subrecursos estão associados ao seu pai e não podem ser movidos para outro serviço de sincronização de armazenamento.

**Recursos de armazenamento do Azure (em ordem hierárquica)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Conta de armazenamento
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Compartilhamento de arquivos

No armazenamento do Azure, o único recurso capaz de mover é a conta de armazenamento. Um compartilhamento de arquivos do Azure, como um subrecurso, não pode ser movido para uma conta de armazenamento diferente.

## <a name="supported-combinations"></a>Combinações com suporte

Ao planejar uma movimentação de recursos, a conta de armazenamento e o recurso de Sincronização de Arquivos do Azure de nível superior, chamado de *serviço de sincronização de armazenamento*, precisam ser considerados juntos.

Como prática recomendada, o serviço de sincronização de armazenamento e as contas de armazenamento que têm a sincronização de compartilhamentos de arquivos, sempre devem residir na mesma assinatura. Há suporte para essas combinações:

* O serviço de sincronização de armazenamento e as contas de armazenamento estão localizados em **grupos de recursos diferentes** (mesmo locatário do Azure)
* O serviço de sincronização de armazenamento e as contas de armazenamento estão localizados em **assinaturas diferentes** (mesmo locatário do Azure)

> [!IMPORTANT]
> Por meio de diferentes combinações de movimentos, um serviço de sincronização de armazenamento e contas de armazenamento podem acabar em assinaturas diferentes, governadas por locatários do AAD diferentes. A sincronização pareceria estar funcionando, mas essa não é uma configuração com suporte. A sincronização pode parar no futuro sem a capacidade de voltar a uma condição de trabalho.

Ao planejar a movimentação de recursos, há diferentes considerações para [a movimentação dentro do mesmo locatário do AAD](#move-within-the-same-azure-active-directory-tenant) e [a movimentação para um locatário do AAD diferente](#move-to-a-new-azure-active-directory-tenant). Ao mover locatários do AAD, sempre mova os recursos de sincronização e armazenamento juntos.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Mover dentro do mesmo locatário Azure Active Directory

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Uma imagem que mostra a portal do Azure para um recurso de serviço de sincronização de armazenamento, com o comando mover expandido. Ele mostra as opções mover grupo de recursos e mover assinatura." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Uma maneira conveniente de mover um recurso de serviço de sincronização de armazenamento é usar o portal do Azure. Navegue até o serviço de sincronização de armazenamento que você deseja mover e selecione **mover** na barra de comandos. As mesmas etapas se aplicam à movimentação de uma conta de armazenamento. Você também pode mover todos os recursos em um grupo de recursos dessa maneira. É recomendável mover um grupo de recursos inteiro quando você tem o serviço de sincronização de armazenamento e todas as contas de armazenamento usadas nesse grupo de recursos.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Quando você move um recurso de conta de armazenamento, a sincronização será interrompida imediatamente. Você precisa autorizar manualmente a sincronização para acessar as contas de armazenamento relevantes na nova assinatura. A seção [sincronização de arquivos do Azure autorização de acesso de armazenamento](#azure-file-sync-storage-access-authorization) fornecerá as etapas necessárias.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Mover para um novo locatário Azure Active Directory

Recursos individuais, como um serviço de sincronização de armazenamento ou contas de armazenamento, não podem se mover por si mesmos para um locatário do AAD diferente. Somente as assinaturas do Azure podem mover locatários do AAD. Pense em sua estrutura de assinatura no novo locatário do AAD. Você pode usar uma assinatura dedicada para Sincronização de Arquivos do Azure. 

1. Crie uma assinatura do Azure (ou determine uma existente no locatário antigo que deve ser movido.
1. [Execute uma assinatura no mesmo locatário do AAD](#move-within-the-same-azure-active-directory-tenant) do seu serviço de sincronização de armazenamento e todas as contas de armazenamento associadas.
1. A sincronização será interrompida. Conclua sua movimentação de locatário imediatamente ou [restaure a capacidade da sincronização de acessar as contas de armazenamento que foram movidas](#azure-file-sync-storage-access-authorization). Em seguida, você pode mover para o novo locatário do AAD mais tarde.

Depois que todos os recursos de Sincronização de Arquivos do Azure relacionados tiverem sido sequestereddos em sua própria assinatura, você estará pronto para mover toda a assinatura para o locatário do AAD de destino. O [guia transferir assinatura](../../role-based-access-control/transfer-subscription.md) permite que você planeje e execute essa transferência.

> [!WARNING]
> Quando você transfere uma assinatura de um locatário para outro, a sincronização será interrompida imediatamente. Você precisa autorizar manualmente a sincronização para acessar as contas de armazenamento relevantes na nova assinatura. A seção [sincronização de arquivos do Azure autorização de acesso de armazenamento](#azure-file-sync-storage-access-authorization) fornecerá as etapas necessárias.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Uma imagem mostrando a folha portal do Azure, visão geral da assinatura, realçando o comando Alterar diretório da barra de ferramentas no centro, na parte superior da página." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Você está pronto para iniciar a migração assim que tiver um plano e as permissões necessárias:
        1. Na portal do Azure, navegue até sua assinatura, folha **visão geral** .
        1. Selecione **Alterar diretório**
        1. Siga as etapas do assistente para atribuir o novo locatário do AAD.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Sincronização de Arquivos do Azure autorização de acesso de armazenamento

Quando as contas de armazenamento forem movidas para uma nova assinatura ou forem movidas dentro de uma assinatura para um novo locatário Azure Active Directory (AAD), a sincronização será interrompida. O RBAC (acesso baseado em função) é usado para autorizar Sincronização de Arquivos do Azure acessar uma conta de armazenamento e essas atribuições de função não são migradas com os recursos.

### <a name="azure-file-sync-service-principal"></a>Entidade de serviço Sincronização de Arquivos do Azure

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Uma imagem que mostra o portal do Azure, gerenciamento de assinatura, provedores de recursos registrados." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        A entidade de serviço Sincronização de Arquivos do Azure deve existir em seu locatário do AAD antes que você possa autorizar o acesso de sincronização a uma conta de armazenamento. </br></br> Quando você cria uma nova assinatura do Azure hoje, o provedor de recursos Sincronização de Arquivos do Azure *Microsoft. StorageSync* é registrado automaticamente com sua assinatura. O registro do provedor de recursos tornará uma *entidade de serviço* para sincronização disponível no locatário Azure Active Directory que governa a assinatura. Uma entidade de serviço é semelhante a uma conta de usuário em seu AAD. Você pode usar a entidade de serviço Sincronização de Arquivos do Azure para autorizar o acesso a recursos por meio do RBAC (controle de acesso baseado em função). A única sincronização de recursos que precisa de acesso às é suas contas de armazenamento que contêm os compartilhamentos de arquivos que devem ser sincronizados. *Microsoft. StorageSync* deve ser atribuído ao leitor de função interna **e ao acesso a dados** na conta de armazenamento. </br></br> Essa atribuição é feita automaticamente por meio do contexto de usuário do usuário conectado quando você adiciona um compartilhamento de arquivo a um grupo de sincronização ou, em outras palavras, cria um ponto de extremidade de nuvem. Quando uma conta de armazenamento é movida para uma nova assinatura ou locatário do AAD, essa atribuição de função é perdida e [deve ser restabelecida manualmente](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Se a assinatura de destino do Azure não foi criada recentemente, verifique se o provedor de recursos *Microsoft. StorageSync* está registrado com a assinatura. Se não estiver, adicione-o manualmente na mesma folha do Portal.

### <a name="establish-sync-access-to-a-storage-account"></a>Estabelecer acesso de sincronização a uma conta de armazenamento

A [entidade de serviço sincronização de arquivos do Azure](#azure-file-sync-service-principal) deve ser usada para autorizar o acesso a uma conta de armazenamento por meio do RBAC (controle de acesso baseado em função). *Microsoft. StorageSync* deve ser atribuído ao leitor de função interna **e ao acesso a dados** na conta de armazenamento. 

Normalmente, essa atribuição é feita automaticamente por meio do contexto de usuário do usuário conectado quando você adiciona um compartilhamento de arquivo a um grupo de sincronização ou, em outras palavras, cria um ponto de extremidade de nuvem. No entanto, quando uma conta de armazenamento é movida para uma nova assinatura ou locatário do AAD, essa atribuição de função é perdida e deve ser restabelecida manualmente.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Uma imagem que exibe a entidade de serviço Microsoft. StorageSync atribuída ao leitor e à função de acesso a dados em uma conta de armazenamento":::
    :::column-end:::
    :::column:::
        Na portal do Azure, navegue até a conta de armazenamento para a qual você precisa autorizar o acesso de sincronização. <ol><li>Selecione **controle de acesso (iam)** no sumário à esquerda.</li><li>Selecione a guia atribuições de função para a lista de usuários e aplicativos (entidades de serviço) que têm acesso à sua conta de armazenamento.</li><li>Selecione **Adicionar**</li><li>No **campo função**, selecione **leitor e acesso a dados**.</li><li>No **campo selecionar**, digite *Microsoft. StorageSync*, selecione a função e clique em **salvar**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Mover para uma região diferente do Azure

O serviço de *sincronização de armazenamento* de recursos sincronização de arquivos do Azure e as contas de armazenamento que contêm compartilhamentos de arquivos que estão sincronizando, têm uma região do Azure na qual eles são implantados. Você determina essa região quando cria um recurso. A região do serviço de sincronização de armazenamento e os recursos da conta de armazenamento devem corresponder. Essas regiões não podem ser alteradas em um dos tipos de recurso após sua criação.

A atribuição de uma região diferente a um recurso é diferente de um [failover de região](#region-fail-over), que pode ter suporte, dependendo da configuração de redundância da conta de armazenamento.

## <a name="region-fail-over"></a>Failover de região

O [armazenamento do Azure oferece opções de redundância geográfica](../common/storage-redundancy.md#geo-redundant-storage) para uma conta de armazenamento. Essas opções de redundância podem apresentar problemas para contas de armazenamento usadas com Sincronização de Arquivos do Azure. O principal motivo é que a replicação entre regiões geograficamente distantes não é executada por Sincronização de Arquivos do Azure, mas por uma tecnologia de replicação de armazenamento interna ao subsistema de armazenamento no Azure. Ele não pode ter uma compreensão do estado do aplicativo e Sincronização de Arquivos do Azure é um aplicativo com arquivos Sincronizando de e para compartilhamentos de arquivos do Azure em um determinado momento. Se você optar por qualquer uma dessas opções de redundância de armazenamento geograficamente distribuídos, não perderá todos os seus dados em um desastre em larga escala. No entanto, você precisa [prever a perda de dados](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> O failover nunca é um substituto apropriado para provisionar seus recursos na região correta do Azure. Se os recursos estiverem na região "errada", você precisará considerar a interrupção da sincronização e da configuração da sincronização novamente para os novos compartilhamentos de arquivos do Azure implantados na região desejada.

Um failover regional pode ser iniciado pela Microsoft em um evento catastrófico que irá renderizar data centers em uma região do Azure INCAPACITATED por um longo período de tempo. A definição do tempo de inatividade que sua empresa pode sustentar pode ser menor do que o momento em que a Microsoft está preparada para permitir a passagem antes de iniciar um failover regional. Para uma situação como essa, [os failovers também podem ser iniciados por clientes](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> No caso de um failover, você precisará arquivar um tíquete de suporte para os serviços de sincronização de armazenamento impactados para que a sincronização funcione novamente.

## <a name="see-also"></a>Confira também

- [Visão geral dos guias de migração de sincronização e compartilhamento de arquivos do Azure](storage-files-migration-overview.md)
- [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)
- [Planejar uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
