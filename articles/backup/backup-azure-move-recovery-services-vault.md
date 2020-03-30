---
title: Como mover os cofres do Azure Backup Recovery Services
description: Instruções sobre como mover o cofre de serviços de recuperação através de assinaturas e grupos de recursos do Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: fed42c578da2e4f27f42e11d5ac67d698bbcd939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120719"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Mova um cofre de serviços de recuperação através de assinaturas e grupos de recursos do Azure

Este artigo explica como mover um cofre dos Serviços de Recuperação configurado para Backup do Azure entre assinaturas do Azure ou para outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure ou o PowerShell para mover um cofre dos Serviços de Recuperação.

## <a name="supported-regions"></a>Regiões com suporte

A movimentação de recursos para o cofre dos Serviços de Recuperação é apoiada na Austrália Leste, Austrália Sudeste, Canadá Central, Canadá Leste, Sudeste Asiático, Leste da Ásia, Central dos EUA, Norte dos EUA, Leste dos EUA, Leste dos EUA, Leste dos EUA, Centro-Sul dos EUA, Centro-Oeste dos EUA, Oeste Central dos EUA, Índia Central, Índia do Sul, Japão Leste, Japão Oeste, Coréia Central, Coréia do Sul, Europa Do Norte, Europa Ocidental, África do Sul Norte, África do Sul Oeste, Reino Unido Do Sul e Reino Unido Oeste.

## <a name="unsupported-regions"></a>Regiões sem suporte

França Central, França Sul, Alemanha Nordeste, Alemanha Central, EUA Gov Iowa, China Norte, China North2, China East, China East2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Pré-requisitos para mover o cofre dos Serviços de Recuperação

- Durante a movimentação do cofre entre grupos de recursos, os grupos de recursos de origem e de destino são bloqueados impedindo as operações de gravação e exclusão. Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Apenas a assinatura de admin tem as permissões para mover um cofre.
- Para mover o cofre através de assinaturas, a assinatura de destino deve residir no mesmo inquilino que a assinatura de origem e seu estado deve ser habilitado.
- Você deve ter permissão para executar operações de gravação no grupo de recursos de destino.
- Mover cofre altera apenas o grupo de recursos. O cofre dos Serviços de Recuperação residirá no mesmo local e não poderá ser alterado.
- Você pode mover apenas um cofre dos Serviços de Recuperação, por região, por vez.
- Se uma VM não se mover com o cofre dos Serviços de Recuperação através de assinaturas ou para um novo grupo de recursos, os pontos de recuperação de VM atuais permanecerão intactos no cofre até que expirem.
- Seja a VM movida com o cofre ou não, você sempre pode restaurar a VM do histórico de backup retido no cofre.
- A Criptografia de Disco do Azure exige que o cofre-chave e as VMs residam na mesma região e assinatura do Azure.
- Para mover uma máquina virtual com discos gerenciados, veja este [artigo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- As opções de movimentação dos recursos implantados por meio do modelo Clássico apresentam diferenças dependendo de se você está movendo os recursos em uma assinatura ou para uma nova assinatura. Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Políticas de backup definidas para o cofre são mantidas após o cofre ser movido entre assinaturas ou para um novo grupo de recursos.
- O cofre móvel com os Arquivos Azure, Azure File Sync ou SQL em VMs IaaS em assinaturas e grupos de recursos não é suportado.
- Se você mover um cofre contendo dados de backup da VM, através de assinaturas, você deve mover suas VMs para a mesma assinatura e usar o mesmo nome de grupo de recursos vm alvo (como era na assinatura antiga) para continuar backups.

> [!NOTE]
>
> Cofres dos Serviços de Recuperação configurados para uso com o **Azure Site Recovery** ainda não podem ser movidos. Se você tiver configurado todas as VMs (IaaS do Azure, Hyper-V, VMware) ou computadores físicos para recuperação de desastre usando o **Azure Site Recovery**, a operação de movimentação será bloqueada. O recurso de movimentação de recursos para o serviço do Site Recovery ainda não está disponível.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Use o portal Azure para mover o cofre dos Serviços de Recuperação para diferentes grupos de recursos

Para mover um cofre dos serviços de recuperação e seus recursos associados para um grupo de recursos diferentes

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Abra a lista de **Cofres dos Serviços de Recuperação** e selecione o cofre que você deseja mover. Quando o painel do cofre se abrir, ele aparecerá como mostrado na imagem a seguir.

   ![Abrir o Cofre do Serviço de Recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se você não visualizar as informações do **Essentials** para seu cofre, clique no ícone de menu suspenso. Agora você deve ver as informações do Essentials para seu cofre.

   ![Guia de Informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, clique em **alterar** ao lado de **Grupo de recursos** para abrir a folha **Mover recursos**.

   ![Alterar Grupo de Recursos](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Na folha **Mover recursos**, para o cofre selecionado, é recomendado mover os recursos relacionados opcionais marcando a caixa de seleção conforme mostrado na imagem a seguir.

   ![Mover assinatura](./media/backup-azure-move-recovery-services/move-resource.png)

5. Para adicionar o grupo de recursos de destino, na lista suspensa **Grupo de recursos**, selecione um grupo de recursos existente ou clique na opção **criar um novo grupo**.

   ![Criar recurso](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Depois de adicionar o grupo de recursos, confirme a opção **Eu entendo que ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar as novas IDs de recurso** e, em seguida, clique em **OK** para concluir a movimentação do cofre.

   ![Mensagem de confirmação](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Use o portal Azure para mover o cofre dos Serviços de Recuperação para uma assinatura diferente

Você pode mover um cofre dos Serviços de Recuperação e seus recursos associados para uma assinatura diferente

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos Serviços de Recuperação e selecione o cofre que você deseja mover. Quando o painel do cofre se abrir, ele aparecerá como mostrado na imagem a seguir.

    ![Abrir o Cofre do Serviço de Recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se você não visualizar as informações do **Essentials** para seu cofre, clique no ícone de menu suspenso. Agora você deve ver as informações do Essentials para seu cofre.

    ![Guia de Informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, clique em **alterar** lado de **Assinatura** para abrir a folha **Mover recursos**.

   ![Alterar assinatura](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecione os recursos a serem movidos. Aqui, recomendamos usar a opção **Selecionar Tudo** para selecionar todos os recursos opcionais listados.

   ![mover recurso](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecione a assinatura de destino na lista suspensa **Assinaturas** para a qual você deseja que o cofre seja movido.
6. Para adicionar o grupo de recursos de destino, na lista suspensa **Grupo de recursos**, selecione um grupo de recursos existente ou clique na opção **criar um novo grupo**.

   ![Adicionar assinatura](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Clique na opção **Eu entendo que ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar as novas IDs de recurso** para confirmar e, em seguida, clique em **OK**.

> [!NOTE]
> Backup entre assinaturas (cofre RS e VMs protegidas estão em assinaturas diferentes) não é um cenário com suporte. Além disso, a opção de redundância de armazenamento de LRS (armazenamento com redundância local) para GRS (armazenamento com redundância global) e vice-versa não pode ser modificada durante a operação de movimentação de cofre.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Use o PowerShell para mover o cofre dos Serviços de Recuperação

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

Depois de executar os cmdlets acima, você será solicitado a confirmar se deseja mover os recursos especificados. Digite **Y** para confirmar. Após uma validação bem-sucedida, o recurso será movido.

## <a name="use-cli-to-move-recovery-services-vault"></a>Use a CLI para mover o cofre dos Serviços de Recuperação

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, use o seguinte cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

## <a name="post-migration"></a>Após a migração

1. Definir/verificar os controles de acesso para os grupos de recursos.  
2. O recurso de monitoramento e relatórios de Backup precisa ser configurado novamente para o cofre após a movimentação ser concluída. A configuração anterior será perdida durante a operação de movimentação.

## <a name="next-steps"></a>Próximas etapas

Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas.

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
