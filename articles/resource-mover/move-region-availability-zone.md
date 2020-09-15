---
title: Mover VMs do Azure para zonas de disponibilidade em outra região com o Azure Resource mover
description: Saiba como mover VMs do Azure para zonas de disponibilidade com o Azure Resource mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 315ea9b683ccd583f5c29c7527013f0d924336f4
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061862"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Mover VMs do Azure para uma zona de disponibilidade em outra região

Neste artigo, saiba como mover as VMs do Azure (e os recursos de rede/armazenamento relacionados) para uma zona de disponibilidade em uma região diferente do Azure, usando o [Azure Resource mover](overview.md).

As [zonas de disponibilidade do Azure](../availability-zones/az-overview.md#availability-zones) ajudam a proteger sua implantação do Azure de falhas do datacenter. Cada zona de disponibilidade é composta de um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as [regiões habilitadas](../availability-zones/az-region.md). Usando o Resource mover, você pode mover:

- Uma única VM de instância para uma zona de disponibilidade/conjunto de disponibilidade na região de destino.
- Uma VM em um conjunto de disponibilidade para uma zona de disponibilidade/conjunto de disponibilidade na região de destino.
- Uma VM em uma zona de disponibilidade de região de origem para uma zona de disponibilidade na região de destino.


> [!IMPORTANT]
> O Azure Resource mover está atualmente em visualização pública.

Se você quiser mover VMs para uma zona de disponibilidade diferente na mesma região, [Leia este artigo](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Pré-requisitos

- Acesso de *proprietário* na assinatura na qual os recursos que você deseja mover estão localizados.
    - Na primeira vez que você adicionar um recurso para um mapeamento de origem e destino específico em uma assinatura do Azure, o Resource mover criará uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI (identificador de serviço gerenciado)) que é confiável para a assinatura.
    - Para criar a identidade e atribuir a ela a função necessária (colaborador ou administrador de acesso do usuário na assinatura de origem), a conta usada para adicionar recursos precisa de permissões de *proprietário* na assinatura. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre as funções do Azure.
- A assinatura precisa de cota suficiente para criar os recursos de origem na região de destino. Caso contrário, solicite limites adicionais. [Saiba mais](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Verifique os preços e os encargos associados à região de destino para a qual você está movendo as VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudá-lo.
    


## <a name="check-vm-requirements"></a>Verificar os requisitos da VM

1. Verifique se as VMs que você deseja mover têm suporte.

    - [Verifique](support-matrix-move-region-azure-vm.md#windows-vm-support) as VMs do Windows com suporte.
    - [Verifique](support-matrix-move-region-azure-vm.md#linux-vm-support) as VMs do Linux e as versões do kernel com suporte.
    - Verifique as configurações de [computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rede](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) com suporte.
2. Verifique se as VMs que você deseja mover estão ativadas.
3. Verifique se as VMs têm os certificados raiz confiáveis mais recentes e uma CRL (lista de certificados revogados) atualizada. 
    - Em VMs do Azure que executam o Windows, instale as atualizações mais recentes do Windows.
    - Em VMs que executam o Linux, siga as diretrizes do distribuidor do Linux para garantir que o computador tenha os certificados e a CRL mais recentes. 
4. Permitir conectividade de saída de VMs:
    - Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a essas [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Se você estiver usando regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída, crie essas [regras de marca de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selecionar os recursos a serem movidos

Selecione os recursos que você deseja mover.

- Você pode selecionar qualquer tipo de recurso com suporte nos grupos de recursos na região de origem selecionada.
- Você move os recursos para uma região de destino na assinatura da região de origem. Se você quiser alterar a assinatura, poderá fazer isso depois que os recursos forem movidos.

1. Na portal do Azure, pesquise por Resource mover. Em seguida, em **Serviços**, selecione **Azure Resource mover**.

    ![Pesquisar o movimentador de recursos](./media/move-region-availability-zone/search.png)

2. Em **visão geral**, selecione **introdução**.

    ![Botão para começar](./media/move-region-availability-zone/get-started.png)

3. Em **mover recursos**  >  **origem + destino**, selecione a assinatura e a região de origem.
4. Em **destino**, selecione a região para a qual você deseja mover as VMs. Em seguida, clique em **Próximo**.

     ![Página para preencher a assinatura/região de origem e de destino](./media/move-region-availability-zone/source-target.png)

6. Em **recursos a serem movidos**, clique em **selecionar recursos**.
7. Em **selecionar recursos**, selecione a VM. Você só pode adicionar recursos com suporte para movimentação. Em seguida, clique em **concluído**. Em **recursos a serem movidos**, clique em **Avançar**.

    ![Página para selecionar as VMs a serem movidas](./media/move-region-availability-zone/select-vm.png)
8. Em **examinar + adicionar**, verifique as configurações de origem e destino.

    ![Página para revisar as configurações e prosseguir com a movimentação](./media/move-region-availability-zone/review.png)

9. Clique em **continuar**para começar a adicionar os recursos.
10. Depois que o processo de adição for concluído com êxito, clique em **Adicionar recursos para mover** no ícone de notificação.

    ![Mensagem em notificações](./media/move-region-availability-zone/notification.png)

Depois de clicar na notificação, os recursos aparecem na página **entre regiões**

> [!NOTE]
> Depois de clicar na notificação, os recursos aparecem na página **entre regiões** , em um estado de *preparação pendente* .
> - Se você quiser remover um recurso de uma coleção de movimentação, o método para fazer isso dependerá de onde você está no processo de movimentação. [Saiba mais](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Resolver dependências

1. Se os recursos mostrarem uma mensagem *validar dependências* na coluna **problemas** , clique no botão **validar dependências** . O processo de validação se seres.
2. Se forem encontradas dependências, clique em **Adicionar dependências**. 
3. Em **Adicionar dependências**, selecione os recursos dependentes > **Adicionar dependências**. Monitorar o progresso nas notificações.

    ![Botão para adicionar dependências](./media/move-region-availability-zone/add-dependencies.png)

3. Adicione outras dependências, se necessário, e valide as dependências novamente. 

    ![Página para adicionar dependências adicionais](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Na página **entre regiões** , verifique se os recursos estão agora em um estado de *preparação pendente* , sem problemas.

    ![Página mostrando recursos no estado de preparação pendente](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de você poder preparar e mover VMs, o grupo de recursos de origem deve estar presente na região de destino. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Prepare da seguinte maneira:

1. Em **várias regiões**, selecione o grupo de recursos de origem > **preparar**.
2. Em **preparar recursos**, clique em **preparar**.

    ![Botão para preparar o grupo de recursos de origem](./media/move-region-availability-zone/prepare-resource-group.png)

    Durante o processo de preparação, o Resource mover gera modelos de Azure Resource Manager (ARM) usando as configurações do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.

> [!NOTE]
>  Depois de preparar o grupo de recursos, ele está no estado *Iniciar movimentação pendente* . 

![Status mostrando o estado de início pendente](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie a movimentação da seguinte maneira:

1. Em **várias regiões**, selecione o grupo de recursos > **Iniciar movimentação**
2. ln **mover recursos**, clique em **Iniciar movimentação**. O grupo de recursos se move para um estado *de movimentação inicial em andamento* .
3. Depois de iniciar a movimentação, o grupo de recursos de destino é criado, com base no modelo ARM gerado. O grupo de recursos de origem se move para um estado de *movimentação pendente de confirmação* .

![Status mostrando a movimentação de confirmação](./media/move-region-availability-zone/commit-move-pending.png)

Para confirmar e concluir o processo de movimentação:

1. Em **várias regiões**, selecione o grupo de recursos > **confirmar movimentação**
2. ln **mover recursos**, clique em **confirmar**.

> [!NOTE]
> Depois de confirmar a movimentação, o grupo de recursos de origem estará em um estado *excluir origem pendente* .


## <a name="add-a-target-availability-zone"></a>Adicionar uma zona de disponibilidade de destino

Antes de movermos o restante dos recursos, definiremos uma zona de disponibilidade de destino para a VM.

1. Na página **entre regiões** , clique no link na coluna **configuração de destino** da VM que você está movendo.

    ![Propriedades da VM](./media/move-region-availability-zone/select-vm-settings.png)

3. Em **definições de configuração**, especifique a configuração para a VM de destino. Você pode configurar uma VM na região de destino da seguinte maneira:
    -  Crie um novo recurso equivalente na região de destino. Exceto pelas configurações especificadas, o recurso de destino é criado com as mesmas configurações da origem.
    - Selecione uma VM existente na região de destino. 

4. Em **zonas**, selecione a zona na qual você deseja posicionar a VM quando ela se mover.

    As alterações são feitas apenas para o recurso que você está editando. Você precisa atualizar os recursos dependentes separadamente.

5. Em **SKU**, especifique a [camada do Azure](..//virtual-machines/sizes.md) que você deseja atribuir à VM de destino.
6. Em **conjunto de disponibilidade**, selecione um conjunto de disponibilidade se desejar que a VM de destino seja executada dentro de um conjunto de disponibilidade na zona de disponibilidade.
7. Selecione **Salvar alterações**.

    ![Configurações da VM](./media/move-region-availability-zone/vm-settings.png)


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

1. Em **várias regiões**, selecione recursos com o estado *Iniciar movimentação pendente*. Em seguida, clique em **Iniciar movimentação**
2. Em **mover recursos**, clique em **Iniciar mover**.

    ![Página para iniciar a movimentação de recursos](./media/move-region-availability-zone/initiate-move.png)

3. Acompanhe o progresso da movimentação na barra de notificações.


> [!NOTE]
> - Para VMs, as VMs de réplica são criadas na região de destino. A VM de origem é desligada e ocorre algum tempo de inatividade (geralmente minutos).
> - O movimentador de recursos recria outros recursos usando os modelos ARM que foram preparados. Geralmente, não há nenhum tempo de inatividade.
> - Depois de preparar os recursos, eles estão em um estado de *movimentação pendente de confirmação* .


![Página para mostrar os recursos em um estado de movimentação pendente de confirmação](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Descartar ou confirmar?

Após a movimentação inicial, você pode decidir se deseja confirmar a movimentação ou descartá-la. 

- **Descartar**: você pode descartar uma movimentação se estiver testando e não deseja realmente mover o recurso de origem. Descartar a movimentação retorna o recurso para um estado de *Iniciar movimentação pendente*.
- **Confirmar**: a confirmação conclui a movimentação para a região de destino. Após a confirmação, um recurso de origem estará em um estado de *exclusão de origem pendente*e você poderá decidir se deseja excluí-lo.

## <a name="discard-the-move"></a>Descartar a movimentação 

Você pode descartar a movimentação da seguinte maneira:

1. Em **várias regiões**, selecione recursos com o estado *confirmação de movimentação pendente*e clique em **descartar movimentação**.
2. Em **descartar movimentação**, clique em **descartar**.
3. Acompanhe o progresso da movimentação na barra de notificações.
 

> [!NOTE]
> Para VMs, depois de descartar os recursos, eles estão em um estado *Iniciar movimentação pendente* .

## <a name="commit-the-move"></a>Confirmar a movimentação

Se você quiser concluir o processo de movimentação, confirme a movimentação. 

1. Em **várias regiões**, selecione recursos com o estado *confirmação de movimentação pendente*e clique em **confirmar movimentação**.
2. Em **confirmar recursos**, clique em **confirmar**.

    ![Página para confirmar os recursos para finalizar movimentação](./media/move-region-availability-zone/commit-resources.png)

3. Acompanhe o andamento da confirmação na barra de notificações.

> [!NOTE]
> - Depois de confirmar a movimentação, as VMs param de replicar. A VM de origem não é afetada pela confirmação.
> - A confirmação não afeta os recursos de rede de origem.
> - Depois de confirmar a movimentação, os recursos estão em um estado *excluir origem pendente* .

![Página mostrando recursos no * estado * excluir origem pendente *](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Definir as configurações após a movimentação

O serviço de mobilidade não é desinstalado automaticamente das VMs. Desinstale-o manualmente ou deixe-o se você planeja mover o servidor novamente.


## <a name="delete-source-resources-after-commit"></a>Excluir recursos de origem após confirmação

Após a movimentação, você pode opcionalmente excluir recursos na região de origem.

1. Em **várias regiões**, clique no nome de cada recurso de origem que você deseja excluir.
2. Na página Propriedades de cada recurso, selecione **excluir**.

## <a name="delete-additional-resources-created-for-move"></a>Excluir recursos adicionais criados para movimentação

Após a movimentação, você pode excluir manualmente a coleção de movimentação e Site Recovery recursos que foram criados.

- A coleção move fica oculta por padrão. Para vê-lo, você precisa ativar os recursos ocultos.
- O armazenamento em cache tem um bloqueio que deve ser excluído antes que possa ser excluído.

Exclua da seguinte maneira: 

1. Localize os recursos no grupo de recursos ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Verifique se toda a VM e outros recursos de origem na região de origem foram movidos ou excluídos. Isso garante que não haja recursos pendentes usando-os.
2. Exclua os recursos:

    - O nome da coleção de movimentação é ```movecollection-<sourceregion>-<target-region>``` .
    - O nome da conta de armazenamento em cache é ```resmovecache<guid>```
    - O nome do cofre é ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre](about-move-process.md) o processo de movimentação.
