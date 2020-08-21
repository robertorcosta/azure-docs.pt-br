---
title: Como criar implantações de atualização para a automação do Azure Gerenciamento de Atualizações
description: Este artigo descreve como agendar implantações de atualização e revisar seu status.
services: automation
ms.subservice: update-management
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 4336ba272dd83ad2a35060c1c7524a564b928484
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717686"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Como implantar atualizações e examinar os resultados

Este artigo descreve como agendar uma implantação de atualização e examinar o processo após a conclusão da implantação.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

O agendamento de uma implantação de atualização cria um recurso de [agenda](../shared-resources/schedules.md) vinculado ao runbook **Patch-MicrosoftOMSComputers** que manipula a implantação de atualização nos computadores de destino. Você precisa agendar uma implantação que siga o agendamento de versão e o período de serviço para instalar as atualizações. Você pode escolher os tipos de atualização que deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

>[!NOTE]
>Se você excluir o recurso de agenda no portal do Azure ou usando o PowerShell depois de criar a implantação, a exclusão interromperá a implantação de atualização agendada e apresentará um erro quando você tentar reconfigurar o recurso de agenda no portal. Você só pode excluir o recurso de agendamento excluindo o agendamento de implantação correspondente.  

Para agendar uma nova implantação de atualização:

1. E sua conta de Automação, vá para **Gerenciamento de atualizações** em **Gerenciamento de atualizações** e, em seguida, selecione **Agendar implantação de atualização**.

2. Em **nova implantação de atualização**, no campo **nome** , insira um nome exclusivo para sua implantação.

3. Selecione o sistema operacional de destino para a implantação de atualização.

4. Na região **Grupos para atualizar (versão prévia)** , defina uma consulta que combina assinatura, grupos de recursos, locais e marcas para compilar um grupo dinâmico de VMs do Azure a ser incluído na implantação. Para saber mais, confira [Usar grupos dinâmicos com o Gerenciamento de Atualizações](update-mgmt-groups.md).

5. Na região **Computadores para atualizar**, selecione uma pesquisa salva, um grupo importado ou selecione **Computadores** no menu suspenso e selecione computadores individuais. Com essa opção, você pode ver a prontidão do agente do Log Analytics para cada computador. Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, confira [Grupos de computadores nos logs do Azure Monitor](../../azure-monitor/platform/computer-groups.md).

6. Use a região **Classificações de atualização** para especificar [classificações de atualização](update-mgmt-view-update-assessments.md#work-with-update-classifications) para os produtos. para cada produto, desmarque todas as classificações de atualização com suporte, exceto as que serão incluídas na implantação da atualização.

    Se sua implantação for destinada a aplicar apenas um conjunto selecionado de atualizações, será necessário desmarcar todas as classificações de atualização pré-selecionadas ao configurar a opção **incluir/excluir atualizações** , conforme descrito na próxima etapa. Isso garante que somente as atualizações que você especificou para *incluir* nessa implantação sejam instaladas nos computadores de destino.

7. Use a região **incluir/excluir atualizações** para adicionar ou excluir as atualizações selecionadas da implantação. Na página **incluir/excluir** , insira os números de ID do artigo da base de conhecimento a serem incluídos ou excluídos.

   > [!IMPORTANT]
   > Lembre-se de que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão de `*`, o Gerenciamento de Atualizações excluirá todos os patches ou pacotes da instalação. Correções excluídas ainda são mostradas como ausentes dos computadores. Para computadores Linux, se você incluir um pacote que tem um pacote dependente que foi excluído, o Gerenciamento de Atualizações não instalará o pacote principal.

   > [!NOTE]
   > Não é possível especificar atualizações que foram substituídas para inclusão na implantação de atualização.

8. Selecione **Configurações da agenda**. A hora de início padrão é 30 minutos após a hora atual. Você pode definir a hora de início para qualquer momento a partir de 10 minutos.

9. Use o campo **recorrência** para especificar se a implantação ocorre uma vez ou usa um agendamento recorrente e, em seguida, selecione **OK**.

10. Na região **Pré-scripts + pós-scripts (versão prévia)** , selecione os scripts a serem executados antes e após sua implantação. Para saber mais, confira [Gerenciar pré-scripts e pós-scripts](update-mgmt-pre-post-scripts.md).
    
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

13. Quando você terminar de configurar a agenda de implantação, selecione **criar**.

    ![Painel Configurações de agendamento de atualizações](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. Você é retornado ao painel de status. Selecione **Implantações de atualização agendadas** para mostrar a agenda de implantação que você criou.

## <a name="schedule-an-update-deployment-programmatically"></a>Agendar uma implantação de atualização programaticamente

Para saber como criar uma implantação de atualizações com a API REST, confira [Configurações de atualização de software – Criar](/rest/api/automation/softwareupdateconfigurations/create).

Você pode usar um runbook de exemplo para criar uma implantação de atualizações semanal. Para saber mais sobre este runbook, consulte [Criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Verifique o status da implantação

Após o início da implantação agendada, você pode ver o status dela na guia **Implantações de atualização** em **Gerenciamento de atualizações**. O status é **Em andamento** se a implantação está em execução no momento. Quando a implantação for concluída com êxito, o status será alterado para **Êxito**. Se houver falha em uma ou mais atualizações na implantação, o status será **Falha parcial**.

## <a name="view-results-of-a-completed-update-deployment"></a>Exibir resultados de uma implantação de atualização concluída

Quando a implantação for concluída, você poderá selecioná-la para ver seus resultados.

[![Atualizar o painel de status de implantação para uma implantação específica](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Em **Resultados da atualização** há um resumo do número total de atualizações e resultados de implantação nas VMs de destino. A tabela à direita mostra uma análise detalhada de cada atualização e os resultados da instalação de cada uma delas.

Os valores disponíveis são:

* **Não foi tentada**: a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Não selecionado**: a atualização não foi selecionada para implantação.
* **Êxito**: a atualização foi bem-sucedida.
* **Falha**: falha na atualização.

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização nas VMs de destino.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros da implantação.

## <a name="next-steps"></a>Próximas etapas

Para saber como criar alertas para notificá-lo sobre os resultados da implantação de atualização, consulte [criar alertas para gerenciamento de atualizações](update-mgmt-configure-alerts.md).