---
title: Gerenciar atualizações e patches para as VMs do Azure na Automação do Azure
description: Este artigo mostra como usar o Gerenciamento de Atualizações para gerenciar atualizações e patches para suas VMs do Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: a701a5a9fd77bd801bb535fe1f26bfa17c97757b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185782"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gerenciar atualizações e patches para as VMs do Azure

Este artigo descreve como usar o recurso [Gerenciamento de Atualizações](automation-update-management.md) da Automação do Azure para gerenciar atualizações e patches para suas VMs do Azure. Para obter informações sobre preços, consulte [Preços de automação do Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/).

> [!NOTE]
> O Gerenciamento de Atualizações dá suporte à implantação de atualizações próprias e de patches baixados previamente. Esse suporte requer alterações nos sistemas aos quais o patch está sendo aplicado. Confira [Definir configurações do Windows Update para o Gerenciamento de Atualizações da Automação do Azure](automation-configure-windows-update.md) para saber como definir essas configurações em seus sistemas.

Antes de usar os procedimentos neste artigo, verifique se você habilitou Gerenciamento de Atualizações em suas VMs usando uma destas técnicas:

* [Habilitar o Gerenciamento de Atualizações de uma conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Habilitar o Gerenciamento de Atualizações navegando no portal do Azure](automation-onboard-solutions-from-browse.md)
* [Habilitar o Gerenciamento de Atualizações de um runbook](automation-onboard-solutions.md)
* [Habilitar o Gerenciamento de Atualizações de uma VM do Azure](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o escopo para a implantação

O Gerenciamento de Atualizações usa uma configuração de escopo dentro do workspace para definir os computadores a receberem atualizações. Para obter mais informações, confira [Limitar o escopo de implantação do Gerenciamento de Atualizações](automation-scope-configurations-update-management.md).

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Para exibir uma avaliação de atualização:

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**. 

2. As atualizações de seu ambiente estão listadas na página Gerenciamento de atualizações. Se alguma atualização for identificada como ausente, uma lista de atualizações ausentes aparecerá na guia **Atualizações ausentes**.

3. Em **Link para informações**, selecione o link para uma atualização para abrir o artigo de suporte que fornece informações importantes sobre a atualização.

    ![Exibir o status de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. Clique em qualquer lugar da atualização para abrir o painel da Pesquisa de logs. A consulta da pesquisa de log é predefinida para essa atualização específica. Você pode modificar essa consulta ou criar sua própria consulta para exibir informações detalhadas.

    ![Exibir o status de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Siga as etapas abaixo para configurar alertas que informam o status da implantação de uma atualização:

1. Em sua Conta de automação, acesse **Alertas**, em **Monitoramento** e, em seguida, clique em **+ Nova regra de alerta**.

2. Na página Criar regra de alerta, sua Conta de automação já está selecionada como o recurso. Se você desejar alterá-la, clique em **Editar recurso**. 

3. Na página Selecionar um recurso, escolha **Contas de Automação** no menu suspenso **Filtrar por tipo de recurso**. 

4. Selecione a Conta de automação que deseja usar e clique em **Concluído**.

5. Clique em **Adicionar condição** para selecionar o sinal adequado para sua implantação de atualização. A tabela a seguir mostra os detalhes dos dois sinais disponíveis.

    |Nome do sinal|Dimensões|Descrição
    |---|---|---|
    |`Total Update Deployment Runs`|– Nome da implantação de atualização<br>– Status    |Alerta quanto ao status geral de uma implantação de atualização.|
    |`Total Update Deployment Machine Runs`|– Nome da implantação de atualização</br>– Status</br>– Computador de destino</br>– ID de execução da implantação de atualizações    |Alerta quanto ao status de uma implantação de atualização direcionada a computadores específicos.|

6. Para uma dimensão, selecione um valor válido na lista. Se o valor desejado não estiver na lista, clique em **\+** ao lado da dimensão e digite o nome personalizado. Em seguida, selecione o valor a ser pesquisado. Se desejar selecionar todos os valores de uma dimensão, clique no botão **Selecionar \*** . Se você não escolher um valor para uma dimensão, o Gerenciamento de Atualizações vai ignorar essa dimensão.

    ![Configurar sinal lógico](./media/automation-tutorial-update-management/signal-logic.png)

7. Em **Lógica de alerta**, insira os valores os campos **Agregação de tempo** e **Limite** e clique em **Concluído**.

8. No painel seguinte, insira um nome e uma descrição para o alerta.

9. Defina o campo **Gravidade** como **Informational(Sev 2)** para uma execução bem-sucedida ou **Informational(Sev 1)** para uma execução com falha.

    ![Configurar sinal lógico](./media/automation-tutorial-update-management/define-alert-details.png)

10. Clique em **Sim** ou **Não**, dependendo de como você deseja habilitar a regra de alerta.

11. Se não quiser ter alertas para essa regra, selecione **Suprimir alertas**.

## <a name="configure-action-groups-for-your-alerts"></a>Configurar grupos de ações para seus alertas

Depois de configurar os alertas, você pode configurar um grupo de ações, que é um grupo de ações a serem usadas em vários alertas. As ações podem incluir notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

1. Selecione um alerta e, em seguida, selecione **Criar** em **Grupos de Ações**. 

2. Insira um nome completo e um nome curto para o grupo de ações. O Gerenciamento de Atualizações usa o nome curto ao enviar notificações usando o grupo especificado.

3. Em **Ações**, insira um nome que especifique a ação, por exemplo, **Notificação por email**. 

4. Para **Tipo de ação**, selecione o tipo apropriado, por exemplo, **Email/SMS/Push/Voz**. 

5. Clique em **Editar detalhes**.

6. Preencha o painel do tipo de ação. Por exemplo, se estiver usando **Email/SMS/Push/Voz**, insira um nome de ação, marque a caixa de seleção **Email**, insira um endereço de email válido e clique em **OK**.

    ![Configurar um grupo de ação de email](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. No painel Adicionar grupo de ações, clique em **OK**.

8. Para um email de alerta, você pode personalizar o assunto do email. Selecione **Personalizar ações** em **Criar regra** e, em seguida, selecione **Assunto do email**. 

9. Quando terminar, clique em **Criar regra de alerta**. 

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

O agendamento de uma implantação de atualização cria um recurso de [agenda](shared-resources/schedules.md) vinculado ao runbook **Patch-MicrosoftOMSComputers** que manipula a implantação de atualização nos computadores de destino. Você precisa agendar uma implantação que siga o agendamento de versão e o período de serviço para instalar as atualizações. Você pode escolher os tipos de atualização que deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

>[!NOTE]
>Se você excluir o recurso de agenda no portal do Azure ou usando o PowerShell depois de criar a implantação, a exclusão interromperá a implantação de atualização agendada e apresentará um erro quando você tentar reconfigurar o recurso de agenda no portal. Você só pode excluir o recurso de agendamento excluindo o agendamento de implantação correspondente.  

Para agendar uma nova implantação de atualização:

1. E sua conta de Automação, vá para **Gerenciamento de atualizações** em **Gerenciamento de atualizações** e, em seguida, selecione **Agendar implantação de atualização**.

2. Em **Nova implantação de atualização**, use o campo **Nome** para inserir um nome exclusivo para sua implantação.

3. Selecione o sistema operacional de destino para a implantação de atualização.

4. Na região **Grupos para atualizar (versão prévia)** , defina uma consulta que combina assinatura, grupos de recursos, locais e marcas para compilar um grupo dinâmico de VMs do Azure a ser incluído na implantação. Para saber mais, confira [Usar grupos dinâmicos com o Gerenciamento de Atualizações](automation-update-management-groups.md).

5. Na região **Computadores para atualizar**, selecione uma pesquisa salva, um grupo importado ou selecione **Computadores** no menu suspenso e selecione computadores individuais. Com essa opção, você pode ver a prontidão do agente do Log Analytics para cada computador. Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, confira [Grupos de computadores nos logs do Azure Monitor](../azure-monitor/platform/computer-groups.md).

6. Use a região **Classificações de atualização** para especificar [classificações de atualização](automation-view-update-assessments.md#work-with-update-classifications) para os produtos. para cada produto, desmarque todas as classificações de atualização com suporte, exceto as que serão incluídas na implantação da atualização.

7. Use a região **Incluir/excluir atualizações** para selecionar atualizações específicas para implantação. A página Incluir/excluir exibe as atualizações pelos números da ID do artigo na KB para inclusão ou exclusão. 
    
   > [!IMPORTANT]
   > Lembre-se de que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão de `*`, o Gerenciamento de Atualizações excluirá todos os patches ou pacotes da instalação. Correções excluídas ainda são mostradas como ausentes dos computadores. Para computadores Linux, se você incluir um pacote que tem um pacote dependente que foi excluído, o Gerenciamento de Atualizações não instalará o pacote principal.

   > [!NOTE]
   > Não é possível especificar atualizações que foram substituídas para inclusão na implantação de atualização.

8. Selecione **Configurações da agenda**. A hora de início padrão é 30 minutos após a hora atual. Você pode definir a hora de início para qualquer momento a partir de 10 minutos.

9. Use o campo **Recorrência** para especificar se a implantação ocorre uma vez ou se usa um agendamento recorrente e clique em **OK**.

10. Na região **Pré-scripts + pós-scripts (versão prévia)** , selecione os scripts a serem executados antes e após sua implantação. Para saber mais, confira [Gerenciar pré-scripts e pós-scripts](pre-post-scripts.md).
    
11. Use o campo **Janelas de manutenção (minutos)** para especificar o tempo permitido para a instalação das atualizações. Considere os seguintes detalhes ao especificar uma janela de manutenção:

    * As janelas de manutenção controlam o número de atualizações que são instaladas.
    * O Gerenciamento de Atualizações não interromperá a instalação de novas atualizações se o fim de uma janela de manutenção estiver se aproximando.
    * O Gerenciamento de Atualizações não encerrará as atualizações em andamento se a janela de manutenção for excedida.
    * Se a janela de manutenção é excedida no Windows, isso geralmente ocorre devido a uma longa instalação de atualização do service pack.

    > [!NOTE]
    > Para evitar atualizações aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote `Unattended-Upgrade` para desabilitar as atualizações automáticas. Para saber mais sobre como configurar o pacote, veja o [tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Use o campo **Opções de reinicialização** para especificar como lidar com reinicializações durante a implantação. As seguintes opções estão disponíveis: 
    * Reinicializar se necessário (padrão)
    * Sempre reinicializar
    * Nunca reinicializar
    * Somente reinicialização; esta opção não instala atualizações

    > [!NOTE]
    > As chaves do Registro listadas em [Chaves do Registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) poderão causar um evento de reinicialização se a opção **Opções de reinicialização** estiver definida como **Nunca reinicializar**.

13. Quando terminar de configurar a agenda de implantação, clique em **Criar**.

    ![Painel Configurações de agendamento de atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. Você é retornado ao painel de status. Selecione **Implantações de atualização agendadas** para mostrar a agenda de implantação que você criou.

## <a name="schedule-an-update-deployment-programmatically"></a>Agendar uma implantação de atualização programaticamente

Para saber como criar uma implantação de atualizações com a API REST, confira [Configurações de atualização de software – Criar](/rest/api/automation/softwareupdateconfigurations/create). 

Você pode usar um runbook de exemplo para criar uma implantação de atualizações semanal. Para saber mais sobre este runbook, consulte [Criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Verifique o status da implantação

Após o início da implantação agendada, você pode ver o status dela na guia **Implantações de atualização** em **Gerenciamento de atualizações**. O status é **Em andamento** se a implantação está em execução no momento. Quando a implantação for concluída com êxito, o status será alterado para **Êxito**. Se houver falha em uma ou mais atualizações na implantação, o status será **Falha parcial**.

## <a name="view-results-of-a-completed-update-deployment"></a>Exibir resultados de uma implantação de atualização concluída

Quando a implantação for concluída, você poderá selecioná-la para ver seu painel.

![Painel de status de implantação de atualização para uma implantação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Em **Resultados da atualização** há um resumo do número total de atualizações e resultados de implantação nas VMs de destino. A tabela à direita mostra uma análise detalhada de cada atualização e os resultados da instalação de cada uma delas.

Os valores disponíveis são:

* **Não foi tentada**: a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Não selecionado**: a atualização não foi selecionada para implantação.
* **Êxito**: a atualização foi bem-sucedida.
* **Falha**: falha na atualização.

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização nas VMs de destino.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros da implantação.

## <a name="view-the-deployment-alert"></a>Exibir o alerta de implantação

Quando a implantação da atualização for concluída, você receberá o alerta que especificou durante a instalação da implantação. Por exemplo, aqui está um email confirmando um patch.

![Configurar o grupo de ação de email](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre as configurações de escopo, confira [Limitar o escopo de implantação do Gerenciamento de Atualizações](automation-scope-configurations-update-management.md).
* Se você precisar pesquisar logs armazenados em seu workspace do Log Analytics, confira [Pesquisas de logs em logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
* Se tiver terminado com as implantações, confira [Desvincular o workspace da conta de Automação para o Gerenciamento de Atualizações](automation-unlink-workspace-update-management.md).
* Para excluir suas VMs do Gerenciamento de Atualizações, confira [Remover VMs do Gerenciamento de Atualizações](automation-remove-vms-from-update-management.md).
* Para solucionar problemas gerais do Gerenciamento de Atualizações, consulte [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para solucionar problemas com o agente de atualização do Windows, consulte [Solucionar problemas do agente de atualização do Windows](troubleshoot/update-agent-issues.md).
* Para solucionar problemas com o agente de atualização do Linux, confira [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).
