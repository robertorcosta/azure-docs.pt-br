---
title: Criar cofres de serviços de recuperação
description: Neste artigo, saiba como criar cofres de Serviços de Recuperação que armazenam os backups e pontos de recuperação.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 38f23ee458845b92c04af47307a2411b26c8ac81
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529393"
---
# <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre de Serviços de Recuperação:

1. Entre na sua assinatura no [Portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Todos os serviços**.

    ![Selecionar Todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

3. Na caixa de diálogo **Todos os serviços**, insira **Serviços de Recuperação**. A lista de recurso filtra de acordo com sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação**.

    ![Insira e escolha os cofres dos Serviços de Recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres de Serviços de Recuperação na assinatura aparecerá.

4. No painel **Cofres dos Serviços de Recuperação**, selecione **Adicionar**.

    ![Adicionar um cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo **Cofre dos Serviços de Recuperação** é aberta. Forneça valores para **Nome**, **Assinatura**, **Grupo de recursos** e **Local**.

    ![Configurar o cofre de Serviços de Recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: digite um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens.
   - **Assinatura**: escolha a assinatura a ser usada. Se você for um membro de apenas uma assinatura, verá esse nome. Se você não tem certeza de qual assinatura usar, utilize a assinatura padrão (sugerida). Só haverá múltiplas opções se a sua conta corporativa ou de estudante estiver associada a várias assinaturas do Azure.
   - **Grupo de recursos**: use um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis em sua assinatura, selecione **Usar existente** e, em seguida, selecione um recurso na caixa de listagem suspensa. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Local**: selecione a região geográfica para o cofre. Se você estiver criando um cofre para proteger as máquinas virtuais, o cofre **deverá** estar na mesma região que as máquinas virtuais.

      > [!IMPORTANT]
      > Se você não tem certeza do local da sua VM, feche a caixa de diálogo. Vá para a lista de máquinas virtuais no portal. Se você tem máquinas virtuais em várias regiões, crie um cofre dos Serviços de Recuperação em cada região. Crie o cofre no primeiro local antes de criar o cofre para outro local. Não é necessário especificar contas de armazenamento para armazenar os dados de backup. O cofre de Serviços de Recuperação e o serviço de Backup do Azure lidam com isso automaticamente.
      >
      >

5. Quando você estiver pronto para criar o cofre dos Serviços de Recuperação, clique em **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode levar um tempo para criar o cofre dos Serviços de Recuperação. Monitore as notificações de status na área **Notificações** no canto superior direito do portal. Depois que o cofre tiver sido criado, ele aparecerá na lista de cofres dos Serviços de Recuperação. Se você não encontrar seu cofre, selecione **Atualizar**.

     ![Atualizar a lista de cofres de backup](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

O Azure Backup lida automaticamente com o armazenamento do cofre. Você precisa especificar como esse armazenamento é replicado.

1. Na folha **Cofres dos Serviços de Recuperação**, clique no novo cofre. Na seção **Configurações,** clique **em Propriedades**.
2. Em **Propriedades,** em **Configuração de backup,** clique em **Atualizar**.

3. Selecione o tipo de replicação de armazenamento e clique **em Salvar**.

     ![Definir a configuração de armazenamento para o novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Recomendamos que, se você estiver usando o Azure como um ponto final de armazenamento de backup principal, continue a usar a configuração **geo-redundante** padrão.
   - Se você não usar o Azure como um ponto de extremidade de armazenamento de backup principal, escolha **Localmente redundante**, que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre [a redundância geográfica](../storage/common/storage-redundancy-grs.md) e [local.](../storage/common/storage-redundancy-lrs.md)

> [!NOTE]
> A alteração **do tipo de replicação de armazenamento** (localmente redundante/geo-redundante) para um cofre de serviços de recuperação deve ser feita antes de configurar backups no cofre. Uma vez configurado o backup, a opção de modificar está desativada e você não pode alterar o **tipo de replicação de armazenamento**.

## <a name="set-cross-region-restore"></a>Definir restauração da região cruzada

Como uma das opções de restauração, o CRR (Cross Region Restore, restauração de regiões cruzadas) permite restaurar as VMs do Azure em uma região secundária, que é uma [região emparelhada azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Esta opção permite que você:

- realizar exercícios quando há um requisito de auditoria ou conformidade
- restaurar o VM ou seu disco se houver um desastre na região primária.

Para escolher esse recurso, **selecione Ativar restauração de região cruzada** na lâmina **Configuração de** backup.

Para este processo, há implicações de preços como está no nível de armazenamento.

>[!NOTE]
>Antes de começar:
>
>- Revise a matriz de [suporte](backup-support-matrix.md#cross-region-restore) para uma lista de tipos e regiões gerenciados suportados.
>- O recurso Derdrestore (Cross Region Restore, na região) está disponível atualmente apenas nas seguintes regiões: 
>    - Centro-Oeste dos EUA
>    - Oeste dos EUA
>    - Oeste dos EUA 2
>    - Centro-Sul dos Estados Unidos
>    - Leste dos EUA
>    - Leste dos EUA 2
>    - Centro-Norte dos EUA
>    - Canadá Central
>    - Leste do Canadá
>    - Leste da Austrália
>    - Sudeste da Austrália
>    - Índia Central
>    - Sul da Índia
>    - Oeste da Índia
>    - Leste do Japão
>    - Oeste do Japão
>    - Sudeste Asiático
>    - Sul do Reino Unido
>    - Oeste do Reino Unido
>    - Europa Ocidental
>    - Norte da Europa
>    - França Central
>    - Coreia Central
>    - Sul da Coreia
>- CRR é um recurso de opt-in nível de cofre para qualquer cofre GRS (desligado por padrão).
>- Por favor, use o seguinte comando para embarcar na sua assinatura para este recurso:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Se você estiver a bordo desse recurso durante a pré-visualização pública limitada, o e-mail de aprovação de revisão incluirá detalhes da política de preços.
>- Após o opt-in, pode levar até 48 horas para que os itens de backup estejam disponíveis em regiões secundárias.
>- Atualmente, o CRR é suportado apenas para o Tipo de Gerenciamento de Backup - ARM Azure VM (o clássico Azure VM não será suportado).  Quando os tipos de gerenciamento adicionais suportam crr, então eles **serão** automaticamente inscritos.

### <a name="configure-cross-region-restore"></a>Configurar restauração de região cruzada

Um cofre criado com redundância GRS inclui a opção de configurar o recurso De restauração de região cruzada. Cada cofre GRS terá um banner, que se vinculará à documentação. Para configurar crr para o cofre, vá para a lâmina Configuração de backup, que contém a opção de habilitar esse recurso.

 ![Banner de configuração de backup](./media/backup-azure-arm-restore-vms/banner.png)

1. A partir do portal, vá para o cofre de Serviços de Recuperação > Configurações > Propriedades.
2. Clique **em Ativar a restauração de região cruzada neste cofre** para ativar a funcionalidade.

   ![Antes de clicar em Ativar a restauração da região cruzada neste cofre](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Depois de clicar em Ativar a restauração da região cruzada neste cofre](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Saiba como [visualizar itens de backup na região secundária](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Saiba como [restaurar na região secundária.](backup-azure-arm-restore-vms.md#restore-in-secondary-region)

Saiba como [monitorar os empregos de restauração de regiões secundárias.](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)

## <a name="modifying-default-settings"></a>Modificando configurações padrão

Recomendamos que você revise as configurações padrão para **o tipo de replicação de armazenamento** e as **configurações de segurança** antes de configurar backups no cofre.

- **O tipo de replicação** de armazenamento por padrão é definido **como Geo-redundante**. Depois de configurar o backup, a opção de modificação será desativada. Siga [estas etapas](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) para revisar e modificar as configurações.

- **A exclusão suave** por padrão é **habilitada** em cofres recém-criados para proteger os dados de backup de exclusões acidentais ou maliciosas. Siga [estas etapas](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) para revisar e modificar as configurações.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre](backup-azure-recovery-services-vault-overview.md) Cofres dos Serviços de Recuperação.
[Saiba mais sobre](backup-azure-delete-vault.md) Exclua os cofres dos Serviços de Recuperação.
