---
title: Como mover os cofres dos serviços de recuperação de backup do Azure
description: Instruções sobre como mover um cofre dos serviços de recuperação entre assinaturas do Azure e grupos de recursos.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 69021131f12b57aedcd531997029858b0722933f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181503"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Mover um cofre dos serviços de recuperação entre assinaturas e grupos de recursos do Azure

Este artigo explica como mover um cofre dos Serviços de Recuperação configurado para Backup do Azure entre assinaturas do Azure ou para outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure ou o PowerShell para mover um cofre dos Serviços de Recuperação.

## <a name="supported-regions"></a>Regiões com suporte

A movimentação de recursos para o cofre dos serviços de recuperação é suportada no leste da Austrália, leste da Austrália, Canadá central, leste do Canadá, Sul Ásia Oriental, Ásia Oriental, EUA Central, norte EUA Central, leste dos EUA, leste dos Estados Unidos, Sul EUA Central, Oeste EUA Central, oeste dos EUA, oeste do Japão, oeste da Coreia, sul da Coreia , Europa Setentrional, Europa Ocidental, norte da África do Sul, oeste da África do Sul, Sul do Reino Unido e Oeste do Reino Unido.

## <a name="unsupported-regions"></a>Regiões sem suporte

França central, sul da França, Alemanha nordeste, Alemanha central, US Gov Iowa, Norte da China, China North2, Leste da China, China 2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Pré-requisitos para mover o cofre dos serviços de recuperação

- Durante a movimentação do cofre entre grupos de recursos, os grupos de recursos de origem e de destino são bloqueados, impedindo as operações de gravação e exclusão. Para obter mais informações, consulte este [artigo](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Somente a assinatura de administrador tem as permissões para mover um cofre.
- Para mover cofres entre assinaturas, a assinatura de destino deve residir no mesmo locatário que a assinatura de origem e seu estado deve ser habilitado.
- Você deve ter permissão para executar operações de gravação no grupo de recursos de destino.
- Mover cofre altera apenas o grupo de recursos. O cofre dos serviços de recuperação residirá no mesmo local e não poderá ser alterado.
- Você pode mover apenas um cofre dos serviços de recuperação, por região, por vez.
- Se uma VM não se mover com o cofre dos serviços de recuperação entre assinaturas ou para um novo grupo de recursos, os pontos de recuperação da VM atuais permanecerão intactos no cofre até que expirem.
- Seja a VM movida com o cofre ou não, você sempre pode restaurar a VM do histórico de backup retido no cofre.
- A Azure Disk Encryption requer que o cofre de chaves e as VMs residam na mesma região e assinatura do Azure.
- Para mover uma máquina virtual com discos gerenciados, veja este [artigo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- As opções para mover recursos implantados por meio do modelo clássico diferem dependendo se você está movendo os recursos dentro de uma assinatura ou para uma nova assinatura. Para obter mais informações, consulte este [artigo](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Políticas de backup definidas para o cofre são mantidas após o cofre ser movido entre assinaturas ou para um novo grupo de recursos.
- Você só pode mover um cofre que contenha qualquer um dos seguintes tipos de itens de backup. Todos os itens de backup dos tipos não listados abaixo precisarão ser interrompidos e os dados excluídos permanentemente antes da movimentação do cofre.
  - Máquinas Virtuais do Azure
  - Agente de Serviços de Recuperação do Microsoft Azure (MARS)
  - Servidor de Backup do Microsoft Azure (MABS)
  - DPM (Data Protection Manager)
- Se você mover um cofre que contém dados de backup de VM, entre assinaturas, deverá mover suas VMs para a mesma assinatura e usar o mesmo nome de grupo de recursos de VM de destino (como se ela estava na assinatura antiga) para continuar os backups.

> [!NOTE]
> Não há suporte para a movimentação de cofres dos serviços de recuperação para o backup do Azure nas regiões do Azure.<br><br>
> Se você tiver configurado quaisquer VMs (Azure IaaS, Hyper-V, VMware) ou máquinas físicas para a recuperação de desastre usando **Azure site Recovery**, a operação de movimentação será bloqueada. Se você quiser mover cofres para Azure Site Recovery, leia [Este artigo](../site-recovery/move-vaults-across-regions.md) para saber mais sobre como mover cofres manualmente.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Usar portal do Azure para mover o cofre dos serviços de recuperação para um grupo de recursos diferente

Para mover um cofre dos serviços de recuperação e seus recursos associados para um grupo de recursos diferente:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de **Cofres dos Serviços de Recuperação** e selecione o cofre que você deseja mover. Quando o painel do cofre se abrir, ele aparecerá como mostrado na imagem a seguir.

   ![Abrir cofre dos serviços de recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se você não vir as informações do **Essentials** para seu cofre, selecione o ícone suspenso. Agora você deve ver as informações do Essentials para seu cofre.

   ![Guia de Informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu visão geral do cofre, selecione **alterar** ao lado do **grupo de recursos**para abrir o painel **mover recursos** .

   ![Alterar Grupo de Recursos](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. No painel **mover recursos** , para o cofre selecionado, é recomendável mover os recursos relacionados opcionais marcando a caixa de seleção conforme mostrado na imagem a seguir.

   ![Mover assinatura](./media/backup-azure-move-recovery-services/move-resource.png)

5. Para adicionar o grupo de recursos de destino, na lista suspensa **grupo de recursos** , selecione um grupo de recursos existente ou selecione **a opção criar um novo grupo** .

   ![Criar recurso](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Depois de adicionar o grupo de recursos, confirme **que eu entendo que as ferramentas e os scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar a nova opção IDs de recurso** e, em seguida, selecione **OK** para concluir a movimentação do cofre.

   ![Mensagem de confirmação](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Usar portal do Azure para mover o cofre dos serviços de recuperação para uma assinatura diferente

Você pode mover um cofre dos Serviços de Recuperação e seus recursos associados para uma assinatura diferente

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos Serviços de Recuperação e selecione o cofre que você deseja mover. Quando o painel do cofre se abrir, ele aparecerá como mostrado na imagem a seguir.

    ![Abrir cofre dos serviços de recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se você não vir as informações do **Essentials** para seu cofre, selecione o ícone suspenso. Agora você deve ver as informações do Essentials para seu cofre.

    ![Guia de Informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu visão geral do cofre, selecione **alterar** ao lado de **assinatura**para abrir o painel **mover recursos** .

   ![Alterar assinatura](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecione os recursos a serem movidos. Aqui, recomendamos usar a opção **Selecionar Tudo** para selecionar todos os recursos opcionais listados.

   ![mover recurso](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecione a assinatura de destino na lista suspensa **Assinaturas** para a qual você deseja que o cofre seja movido.
6. Para adicionar o grupo de recursos de destino, na lista suspensa **grupo de recursos** , selecione um grupo de recursos existente ou selecione **a opção criar um novo grupo** .

   ![Adicionar assinatura](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Selecione **eu entendo que ferramentas e scripts associados a recursos movidos não funcionarão até que eu os atualize para usar a opção de novas IDs de recurso** para confirmar e, em seguida, selecione **OK**.

> [!NOTE]
> O backup de assinatura cruzada (cofre RS e VMs protegidas estão em assinaturas diferentes) não é um cenário com suporte. Além disso, a opção de redundância de armazenamento do LRS (armazenamento redundante local) para o armazenamento com redundância global (GRS) e vice-versa não pode ser modificada durante a operação de movimentação do cofre.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Usar o PowerShell para mover o cofre dos serviços de recuperação

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, use o cmdlet `Move-AzureRMResource`. `Move-AzureRMResource` requer o nome do recurso e o tipo de recurso. Você pode obter ambos do cmdlet `Get-AzureRmRecoveryServicesVault`.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Para mover os recursos para uma assinatura diferente, inclua o parâmetro `-DestinationSubscriptionId`.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Depois de executar os cmdlets acima, você será solicitado a confirmar que deseja mover os recursos especificados. Digite **Y** para confirmar. Após uma validação bem-sucedida, o recurso será movido.

## <a name="use-cli-to-move-recovery-services-vault"></a>Usar a CLI para mover o cofre dos serviços de recuperação

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, use o seguinte cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

## <a name="post-migration"></a>Após a migração

1. Defina/Verifique os controles de acesso para os grupos de recursos.  
2. O recurso de monitoramento e relatório de backup precisa ser configurado novamente para o cofre após a conclusão da movimentação. A configuração anterior será perdida durante a operação de movimentação.

## <a name="next-steps"></a>Próximas etapas

Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas.

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).
