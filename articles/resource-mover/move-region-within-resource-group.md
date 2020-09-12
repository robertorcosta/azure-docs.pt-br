---
title: Mover recursos para outra região com o Azure Resource mover
description: Saiba como mover recursos dentro de um grupo de recursos para outra região com o Azure Resource mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 7d230ce068678bbc074b54ab361f3d70d9b102f8
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670263"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Mover recursos entre regiões (do grupo de recursos)

Neste artigo, saiba como mover recursos em um grupo de recursos específico para uma região diferente do Azure. No grupo de recursos, você seleciona os recursos que deseja mover. Em seguida, você os move usando o [Azure Resource mover](overview.md).

> [!IMPORTANT]
> O Azure Resource mover está atualmente em visualização pública.


## <a name="prerequisites"></a>Pré-requisitos

- Você precisa de acesso de *proprietário* na assinatura na qual os recursos que você deseja mover estão localizados.
    - Na primeira vez que você adicionar um recurso para um mapeamento de origem e destino específico em uma assinatura do Azure, o Resource mover criará uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI (identificador de serviço gerenciado)) que é confiável para a assinatura.
    - Para criar a identidade e atribuir a ela a função necessária (colaborador ou administrador de acesso do usuário na assinatura de origem), a conta usada para adicionar recursos precisa de permissões de *proprietário* na assinatura. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre as funções do Azure.
- A assinatura precisa de cota suficiente para criar os recursos de origem na região de destino. Caso contrário, solicite limites adicionais. [Saiba mais](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Verifique os preços e os encargos associados à região de destino para a qual você está movendo as VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudá-lo.
- Verifique se os recursos que você deseja mover têm suporte do Resource mover:
    - VMs do Azure e discos associados
    - NICs
    - Conjuntos de disponibilidade
    - Redes virtuais do Azure
    - Endereços IP públicos
    - NSGs (grupos de segurança de rede)
    - Balanceadores de carga internos e públicos
    - Bancos de dados SQL do Azure e pools elásticos


## <a name="check-vm-requirements"></a>Verificar os requisitos da VM

1. Verifique se as VMs que você deseja mover têm suporte.

    - [Verifique](support-matrix-move-region-azure-vm.md#windows-vm-support) as VMs do Windows com suporte.
    - [Verifique](support-matrix-move-region-azure-vm.md#linux-vm-support) as VMs do Linux e as versões do kernel com suporte.
    - Verifique as configurações de [computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rede](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) com suporte.
2. Verifique se as VMs têm os certificados raiz confiáveis mais recentes e uma CRL (lista de certificados revogados) atualizada. 
    - Em VMs do Azure que executam o Windows, instale as atualizações mais recentes do Windows.
    - Em VMs que executam o Linux, siga as diretrizes do distribuidor do Linux para garantir que o computador tenha os certificados e a CRL mais recentes. 
3. Permitir conectividade de saída de VMs:
    - Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a essas [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Se você estiver usando regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída, crie essas [regras de marca de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selecionar os recursos a serem movidos

Selecione os recursos que você deseja mover. Você move os recursos para uma região de destino na assinatura da região de origem. Se você quiser alterar a assinatura, poderá fazer isso depois que os recursos forem movidos.

1. No portal do Azure, abra o grupo de recursos relevante.
2. Na página grupo de recursos, selecione os recursos que você deseja mover.
3. Selecione **mover**  >  **mover para outra região**.

    ![Seleção para mover recursos para uma região diferente](./media/move-region-within-resource-group/select-move-region.png)
    
4. Em **origem + destino**, selecione a região de destino para a qual você deseja mover os recursos. Em seguida, selecione **Avançar**.
5. Na **região de metadados**, selecione onde você deseja armazenar metadados sobre os recursos que você está movendo.  Um grupo de recursos é criado especificamente para essa finalidade. Em seguida, selecione **Avançar**.


    ![Página de origem e destino para selecionar a região de destino](./media/move-region-within-resource-group/source-target.png)


7. Em **recursos a serem movidos**, selecione **Avançar**.  
8. Em **selecionar recursos**, selecione o recurso que você deseja mover. Você só pode adicionar recursos com suporte para movimentação. Em seguida, selecione **Concluído**.
9. Em **mover recursos**, selecione **Avançar**. 
10. Em **examinar + adicionar**, verifique os detalhes de origem e de destino.
11. Confirme que você entendeu que os metadados sobre os recursos que estão sendo movidos serão armazenados em um grupo de recursos criado para essa finalidade e que você permitirá que o motor de recurso Crie uma identidade gerenciada pelo sistema para acessar os recursos de assinatura.
1. Selecione **continuar** para começar a adicionar os recursos.

    ![Página de resumo para verificar os detalhes e continuar com a movimentação](./media/move-region-within-resource-group/summary.png)    

11. A operação de adição de recurso é iniciada. Quando a operação é concluída, as notificações mostram que os recursos foram adicionados e a implantação foi bem-sucedida.
13. Nas notificações, selecione **Adicionar recursos para mover**.

    ![Mensagem mostrada em notificações](./media/move-region-within-resource-group/notification.png)    


14. Depois de selecionar a notificação, os recursos selecionados são adicionados a uma coleção de movimentação no Hub do Azure Resource mover.  O Resource mover ajuda a verificar dependências e, em seguida, iniciar a movimentação de recursos para a região de destino.

## <a name="resolve-dependencies"></a>Resolver dependências

Os recursos que você está movendo aparecem na página **entre regiões** , em um estado de *preparação pendente* . Inicie a validação da seguinte maneira:

1. Se os recursos mostrarem uma mensagem *validar dependências* na coluna **problemas** , selecione o botão **validar dependências** . O processo de validação é iniciado.

    ![Botão para validar dependências](./media/move-region-within-resource-group/validate-dependencies.png)

2. Se forem encontradas dependências, selecione **Adicionar dependências**. 
3. Em **Adicionar dependências**, selecione os recursos dependentes > **Adicionar dependências**. Monitorar o progresso nas notificações.

    ![Botão para adicionar dependências](./media/move-region-within-resource-group/add-dependencies.png)

3. Adicione outras dependências, se necessário, e valide as dependências conforme necessário. Selecione **Atualizar** para garantir que os recursos mostrem um estado atualizado.

4. Na página **entre regiões** , verifique se os recursos estão agora em um estado de *preparação pendente* , sem problemas.

    ![Página para mostrar o estado de preparação pendente para todos os recursos](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de você poder preparar e mover recursos, o grupo de recursos de origem deve estar presente na região de destino. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Prepare da seguinte maneira:

1. Em **várias regiões**, selecione o grupo de recursos de origem > **preparar**.
2. Em **preparar recursos**, selecione **preparar**.
1. 
    ![Botão para preparar o grupo de recursos de origem](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Durante o processo de preparação, o Resource mover gera modelos de Azure Resource Manager (ARM) usando as configurações do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.
    
> [!NOTE]
>  Depois de preparar o grupo de recursos, ele está no estado *Iniciar movimentação pendente* . Atualize para mostrar o estado mais recente.

![Status mostrando o estado de início pendente](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie a movimentação da seguinte maneira:

1. Em **várias regiões**, selecione o grupo de recursos > **Iniciar movimentação**
2. ln **mover recursos**, selecione **Iniciar movimentação**. O grupo de recursos se move para um estado *de movimentação inicial em andamento* .
3. Depois de iniciar a movimentação, o grupo de recursos de destino é criado, com base no modelo ARM gerado. O grupo de recursos de origem se move para um estado de *movimentação pendente de confirmação* .

![Status mostrando a movimentação de confirmação](./media/move-region-availability-zone/commit-move-pending.png)

Para confirmar e concluir o processo de movimentação:

1. Em **várias regiões**, selecione o grupo de recursos > **confirmar movimentação**
2. ln **mover recursos**, selecione **confirmar**.

> [!NOTE]
> Depois de confirmar a movimentação, o grupo de recursos de origem estará em um estado *excluir origem pendente* .

## <a name="modify-target-settings"></a>Modificar configurações de destino

Se você não quiser mover um recurso de origem, poderá fazer o seguinte:

- Crie um recurso na região de destino com o mesmo nome e as mesmas configurações que o recurso na região de origem.
- Crie um novo recurso equivalente na região de destino. Exceto pelas configurações especificadas, o recurso de destino é criado com as mesmas configurações da origem.
- Use um recurso existente na região de destino.

Modifique uma configuração da seguinte maneira:

1. Para modificar uma configuração, selecione a entrada na coluna **configuração de destino** do recurso.
2. Na página **configuração de destino** , especifique as configurações de destino que você deseja usar.
    As alterações são feitas apenas para o recurso que você está editando. Você precisa atualizar os recursos dependentes separadamente.   
    
As configurações exatas que você modifica dependem do tipo de recurso. [Saiba mais](modify-target-settings.md) sobre como editar as configurações de destino.

## <a name="prepare-resources-to-move"></a>Preparar os recursos para mover

Agora que o grupo de recursos de origem foi movido, você pode se preparar para mover os outros recursos.

1. Em **várias regiões**, selecione os recursos que você deseja preparar. 

    ![Página para selecionar preparar para outros recursos](./media/move-region-availability-zone/prepare-other.png)

2. Selecione **preparar**.

> [!NOTE]
> - Durante o processo de preparação, o agente de mobilidade Azure Site Recovery é instalado em VMs, para replicação.
> - Os dados da VM são replicados periodicamente para a região de destino. Isso não afeta a VM de origem.
> - A movimentação de recursos gera modelos de ARM para os outros recursos de origem.
> - Depois de preparar os recursos, eles estão em um estado *Iniciar movimentação pendente* .

![Página mostrando recursos no estado iniciar movimentação pendente](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Iniciar a movimentação

Com os recursos preparados, agora você pode iniciar a movimentação.

1. Em **várias regiões**, selecione recursos com o estado *Iniciar movimentação pendente*. Em seguida, selecione **Iniciar movimentação**.
2. Em **mover recursos**, selecione **Iniciar movimentação**.

    ![Selecione para o botão iniciar movimentação](./media/move-region-within-resource-group/initiate-move.png)

3. Acompanhe o progresso da movimentação na barra de notificações.


> [!NOTE]
> - Para VMs, as VMs de réplica são criadas na região de destino. A VM de origem é desligada e ocorre algum tempo de inatividade (geralmente minutos).<br/>
> - O movimentador de recursos recria outros recursos usando os modelos ARM que foram preparados. Geralmente, não há nenhum tempo de inatividade.<br/> 
> - Para balanceadores de carga, as regras de NAT não são copiadas. Crie-os na região de destino depois de confirmar a movimentação.
> - Para endereços IP públicos, o rótulo de nome DNS não é copiado. Recrie o rótulo depois de confirmar a movimentação.
> - Depois de preparar os recursos, eles estão em um estado de *movimentação pendente de confirmação* .


## <a name="discard-or-commit"></a>Descartar ou confirmar?

Após a movimentação inicial, você pode decidir se deseja confirmar a movimentação ou descartá-la. 

- **Descartar**: você pode descartar uma movimentação se estiver testando e não deseja realmente mover o recurso de origem. Descartar a movimentação retorna o recurso para um estado de *Iniciar movimentação pendente*.
- **Confirmar**: a confirmação conclui a movimentação para a região de destino. Após a confirmação, um recurso de origem estará em um estado de *exclusão de origem pendente*e você poderá decidir se deseja excluí-lo.


## <a name="discard-the-move"></a>Descartar a movimentação 

Você pode descartar a movimentação da seguinte maneira:

1. Em **várias regiões**, selecione recursos com o estado *confirmação de movimentação pendente*e selecione **descartar movimentação**.
2. Em **descartar movimentação**, selecione **descartar**.
3. Acompanhe o progresso da movimentação na barra de notificações.
4. Quando as notificações mostrarem que a movimentação foi bem-sucedida, selecione **Atualizar**. 

> [!NOTE]
> Para VMs, depois de descartar os recursos, eles estão em um estado *Iniciar movimentação pendente* .

## <a name="commit-the-move"></a>Confirmar a movimentação

Se você quiser concluir o processo de movimentação, confirme a movimentação. 


1. Em **várias regiões**, selecione recursos com o estado *confirmação de movimentação pendente*e selecione **confirmar movimentação**.
2. Em **confirmar recursos**, selecione **confirmar**.

    ![Página para confirmar os recursos para finalizar movimentação](./media/move-region-within-resource-group/commit-resources.png)

3. Acompanhe o andamento da confirmação na barra de notificações.

> [!NOTE]
> - Depois de confirmar a movimentação, as VMs param de replicar. A VM de origem não é afetada pela confirmação.
> - A confirmação não afeta os recursos de rede de origem.
> - Depois de confirmar a movimentação, os recursos estão em um estado *excluir origem pendente* .

## <a name="configure-settings-after-the-move"></a>Definir as configurações após a movimentação

1. Como os rótulos de nome DNS não são copiados para endereços IP públicos, após a conclusão da movimentação, navegue até os recursos de destino e atualize o rótulo. 
2. Para balanceadores de carga internos, como as regras de NAT não são copiadas, navegue até os recursos criados na região de destino e atualize as regras de NAT.
3. O serviço de mobilidade não é desinstalado automaticamente das VMs. Desinstale-o manualmente ou deixe-o se você planeja mover o servidor novamente.
## <a name="delete-source-resources-after-commit"></a>Excluir recursos de origem após confirmação

Após a movimentação, você pode opcionalmente excluir recursos na região de origem. 

1. Em **várias regiões**, selecione o nome de cada recurso de origem que você deseja excluir.
2. Na página Propriedades de cada recurso, selecione **excluir**.

## <a name="delete-additional-resources-created-for-move"></a>Excluir recursos adicionais criados para movimentação

Após a movimentação, você pode excluir manualmente a coleção de movimentação e Site Recovery recursos que foram criados.

- A coleção move fica oculta por padrão. Para vê-lo, você precisa ativar os recursos ocultos.
- O armazenamento em cache tem um bloqueio que deve ser excluído antes que possa ser excluído.

Exclua da seguinte maneira: 

1. Localize os recursos no grupo de recursos ```RegionMoveRG-<sourceregion>-<target-region>``` , na região de origem.
2. Verifique se toda a VM e outros recursos de origem na coleção de movimentação foram movidos/excluídos. Isso garante que não haja recursos pendentes usando-os.
2. Exclua os recursos:

    - O nome da coleção de movimentação é ```movecollection-<sourceregion>-<target-region>``` .
    - O nome da conta de armazenamento em cache é ```resmovecache<guid>```
    - O nome do cofre é ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Próximas etapas


[Saiba mais sobre](about-move-process.md) o processo de movimentação.
