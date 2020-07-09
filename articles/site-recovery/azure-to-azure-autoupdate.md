---
title: Atualização automática do serviço de mobilidade no Azure Site Recovery
description: Visão geral da atualização automática do serviço de mobilidade ao replicar VMs do Azure usando Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b57ce89979225015dc87bbfb17f9603897ef6d6b
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135848"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Atualização automática do serviço de mobilidade na replicação do Azure para o Azure

Azure Site Recovery usa uma cadência de versão mensal para corrigir quaisquer problemas e aprimorar os recursos existentes ou adicionar novos. Para permanecer atualizado com o serviço, você deve planejar a implantação de patch a cada mês. Para evitar a sobrecarga associada a cada atualização, você pode permitir que Site Recovery gerencie atualizações de componentes.

Conforme mencionado na [arquitetura de recuperação de desastre do Azure para o Azure](azure-to-azure-architecture.md), o serviço de mobilidade é instalado em todas as VMs (máquinas virtuais) do Azure que têm a replicação habilitada de uma região do Azure para outra. Quando você usa atualizações automáticas, cada nova versão atualiza a extensão do serviço de mobilidade.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Como funcionam as atualizações automáticas

Quando você usa Site Recovery para gerenciar atualizações, ele implanta um runbook global (usado pelos serviços do Azure) por meio de uma conta de automação, criada na mesma assinatura que o cofre. Cada cofre usa uma conta de automação. Para cada VM em um cofre, o runbook verifica atualizações automáticas ativas. Se uma versão mais recente da extensão do serviço de mobilidade estiver disponível, a atualização será instalada.

A agenda padrão do runbook ocorre diariamente às 12:00 no fuso horário da geografia da VM replicada. Você também pode alterar o agendamento do runbook por meio da conta de automação.

> [!NOTE]
> A partir do [pacote cumulativo](site-recovery-whats-new.md#updates-march-2019)de atualizações 35, você pode escolher uma conta de automação existente a ser usada para atualizações. Antes do pacote cumulativo de atualizações 35, Site Recovery criou a conta de automação por padrão. Você só pode selecionar essa opção quando habilitar a replicação para uma VM. Ele não está disponível para uma VM que já tenha a replicação habilitada. A configuração que você seleciona aplica-se a todas as VMs do Azure protegidas no mesmo cofre.

Ativar as atualizações automáticas não exige uma reinicialização de suas VMs do Azure ou afeta a replicação em andamento.

A cobrança de trabalhos na conta de automação baseia-se no número de minutos de tempo de execução de trabalho usados em um mês. A execução do trabalho leva alguns segundos a cerca de um minuto por dia e é coberta como unidades livres. Por padrão, 500 minutos são incluídos como unidades livres para uma conta de automação, conforme mostrado na tabela a seguir:

| Unidades livres incluídas (cada mês) | Price |
|---|---|
| Tempo de execução de trabalho 500 minutos | ₹ 0.14/minuto

## <a name="enable-automatic-updates"></a>Habilitar as atualizações automáticas

Há várias maneiras pelas quais Site Recovery pode gerenciar as atualizações de extensão:

- [Gerenciar como parte da etapa habilitar replicação](#manage-as-part-of-the-enable-replication-step)
- [Ativar/desativar as configurações de atualização de dentro do cofre](#toggle-the-extension-update-settings-inside-the-vault)
- [Gerenciar atualizações manualmente](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Gerenciar como parte da etapa habilitar replicação

Ao habilitar a replicação para uma VM iniciada [na exibição da VM](azure-to-azure-quickstart.md) ou [no cofre dos serviços de recuperação](azure-to-azure-how-to-enable-replication.md), você pode permitir que site Recovery gerencie atualizações para a extensão site Recovery ou gerencie-as manualmente.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Configurações de extensão":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Ativar/desativar as configurações de atualização de dentro do cofre

1. No cofre dos serviços de recuperação, vá para **gerenciar**  >  **site Recovery infraestrutura**.
1. Em **para configurações de atualização de extensão de máquinas virtuais do Azure**  >  **Extension Update Settings**  >  ,**permita que site Recovery gerencie**, selecione **ativado**.

   Para gerenciar a extensão manualmente, selecione **desativado**.

1. Clique em **Salvar**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Configurações de atualização de extensão":::

> [!IMPORTANT]
> Quando você escolhe **permitir site Recovery gerenciar**, a configuração é aplicada a todas as VMs no cofre.

> [!NOTE]
> Qualquer opção notifica você sobre a conta de automação usada para gerenciar atualizações. Se você estiver usando esse recurso em um cofre pela primeira vez, uma nova conta de automação será criada por padrão. Como alternativa, você pode personalizar a configuração e escolher uma conta de automação existente. Todos os subseqüentes são necessários para habilitar a replicação no mesmo cofre usará a conta de automação criada anteriormente. Atualmente, o menu suspenso listará apenas as contas de automação que estão no mesmo grupo de recursos que o cofre.

### <a name="manage-updates-manually"></a>Gerenciar atualizações manualmente

1. Se houver novas atualizações para o serviço de mobilidade instaladas em suas VMs, você verá a seguinte notificação: **novo site Recovery atualização do agente de replicação está disponível. Clique para instalar.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Janela Itens replicados":::

1. Selecione a notificação para abrir a página seleção de VM.
1. Escolha as VMs que você deseja atualizar e, em seguida, selecione **OK**. O serviço de mobilidade de atualização será iniciado para cada VM selecionada.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Lista de VMs de itens replicados":::

## <a name="common-issues-and-troubleshooting"></a>Problemas comuns e solução de problemas

Se houver um problema com as atualizações automáticas, você verá uma notificação de erro em **problemas de configuração** no painel do cofre.

Se você não puder habilitar as atualizações automáticas, consulte os seguintes erros comuns e as ações recomendadas:

- **Erro**: você não tem permissões para criar uma conta Executar como do Azure (entidade de serviço) e conceder a função Colaborador à entidade de serviço.

  **Ação recomendada**: Verifique se a conta conectada está atribuída como colaborador e tente novamente. Para obter mais informações sobre como atribuir permissões, consulte a seção permissões necessárias de [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Para corrigir a maioria dos problemas depois de habilitar as atualizações automáticas, selecione **reparar**. Se o botão reparar não estiver disponível, consulte a mensagem de erro exibida no painel configurações de atualização de extensão.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Botão de reparo do serviço Site Recovery nas configurações de atualização de extensão":::

- **Erro**: a conta Executar como não tem permissão para acessar o recurso de serviços de recuperação.

  **Ação recomendada**: exclua e [recrie a conta Executar como](../automation/manage-runas-account.md). Ou então, certifique-se de que o aplicativo de Azure Active Directory da conta Executar como da automação possa acessar o recurso dos serviços de recuperação.

- **Erro**: a conta Executar como não foi encontrada. Um deles foi excluído ou não criado - o aplicativo do Azure Active Directory, a entidade de serviço, a função, o ativo do certificado de automação, o ativo de conexão de automação - ou a impressão digital não é idêntica entre o certificado e a conexão.

  **Ação recomendada**: exclua e [recrie a conta Executar como](../automation/manage-runas-account.md).

- **Erro**: o certificado executar como do Azure usado pela conta de automação está prestes a expirar.

  O certificado autoassinado criado para a conta Executar como expirará um ano a partir da data de criação. Você pode renová-lo a qualquer momento antes que ele expire. Se você se inscreveu para notificações por email, também receberá emails quando uma ação for necessária do seu lado. Esse erro será mostrado dois meses antes da data de expiração e será alterado para um erro crítico se o certificado tiver expirado. Depois que o certificado expirar, a atualização automática não será funcional até que você renove o mesmo.

  **Ação recomendada**: para resolver esse problema, selecione **reparar** e **renovar certificado**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="renovar-CERT":::

  > [!NOTE]
  > Depois de renovar o certificado, atualize a página para exibir o status atual.
