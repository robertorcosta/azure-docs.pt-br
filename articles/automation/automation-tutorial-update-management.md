---
title: Gerenciar atualizações e patches para as VMs do Azure
description: Este artigo fornece uma visão geral de como usar o Gerenciamento de Atualizações da Automação do Azure para gerenciar atualizações e patches das VMs do Azure e não Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 888dc99162551482afc715f1a793614d2c866384
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677045"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gerenciar atualizações e patches para as VMs do Azure

Você pode usar a solução Gerenciamento de Atualizações para gerenciar atualizações e patches de suas máquinas virtuais. Neste tutorial, você aprende a avaliar rapidamente o status das atualizações disponíveis, a agendar a instalação das atualizações necessárias e revisar os resultados da implantação e criar um alerta para verificar se as atualizações foram aplicadas com sucesso.

Para obter informações sobre preços, consulte [Preços de automação do Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* A solução [Gerenciamento de Atualizações](automation-update-management.md) habilitada para uma ou mais de suas VMs.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Após habilitar o Gerenciamento de Atualizações, a página **Gerenciamento de Atualizações** será aberta. Se alguma atualização for identificada como ausente, uma lista de atualizações ausentes aparecerá na guia **Atualizações ausentes**.

Em **Link de informações**, selecione o link de atualização para abrir o artigo de suporte da atualização. Você pode ver informações importantes sobre a atualização.

![Exibir o status de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Clique em qualquer lugar da atualização para abrir o painel **Pesquisa de logs** da atualização selecionada. A consulta da pesquisa de log é predefinida para essa atualização específica. Você pode modificar ou criar uma consulta própria para ver informações detalhadas sobre as atualizações implantadas ou ausentes no ambiente.

![Exibir o status de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Nesta etapa, você aprenderá a configurar um alerta para informar o status da implantação de uma atualização.

### <a name="alert-conditions"></a>Condições de alerta

Em sua Conta de automação, acesse **Alertas**, em **Monitoramento** e, em seguida, clique em **+ Nova regra de alerta**.

Sua Conta de automação já está selecionada como o recurso. Se você desejar alterá-la, clique em **Selecionar**. Na página **Selecionar um recurso**, escolha **Contas de Automação** no menu suspenso **Filtrar por tipo de recurso**. Selecione sua Conta de automação, depois clique em **Concluído**.

Clique em **Adicionar condição** para selecionar o sinal adequado para sua implantação de atualização. A tabela a seguir mostra os detalhes dos dois sinais disponíveis.

|Nome do sinal|Dimensões|Descrição|
|---|---|---|
|`Total Update Deployment Runs`|– Nome da implantação de atualização<br>– Status|Esse sinal alerta quanto ao status geral de uma implantação de atualização.|
|`Total Update Deployment Machine Runs`|– Nome da implantação de atualização</br>– Status</br>– Computador de destino</br>– ID de execução da implantação de atualizações|Esse sinal alerta quanto ao status de uma implantação de atualização direcionada a computadores específicos.|

Para uma dimensão, selecione um valor válido na lista. Se o valor desejado não estiver na lista, clique no sinal **\+** ao lado da dimensão e digite o nome personalizado. Em seguida, selecione o valor a ser pesquisado. Se desejar selecionar todos os valores de uma dimensão, clique no botão **Selecionar \*** . Se você não escolher um valor para uma dimensão, o Gerenciamento de Atualizações ignorará essa dimensão.

![Configurar sinal lógico](./media/automation-tutorial-update-management/signal-logic.png)

Em **Lógica de alerta**, para **Limite**, digite **1**. Quando tiver terminado, selecione **Concluído**.

### <a name="alert-details"></a>Detalhes do Alerta

Em **2. Defina os detalhes do alerta** e insira um nome e uma descrição para o alerta. Definir a **Gravidade** para **Informational(Sev 2)** para uma execução bem-sucedida ou **Informational(Sev 1)** para uma execução com falha.

![Configurar sinal lógico](./media/automation-tutorial-update-management/define-alert-details.png)

Em **Grupos de ações**, selecione **Criar Novo**. Um grupo de ação é um grupo de ações que você pode usar através de vários alertas. As ações podem incluir notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

No campo **Nome do grupo de ações**, digite um nome para o alerta e um nome curto. O Gerenciamento de Atualizações usa o nome curto no lugar de um nome completo do grupo de ações ao enviar notificações usando o grupo especificado.

Em **Ações**, insira um nome para a ação, como **Notificação por Email**. Para **Tipo de Ação**, selecione **Email/SMS/Push/Voz**. Para **Detalhes**, selecione **Editar detalhes**.

No painel **Email/SMS/Push/Voz**, digite um nome. Marque a caixa de seleção **Email** e digite um endereço de email válido.

![Configurar um grupo de ação de email](./media/automation-tutorial-update-management/configure-email-action-group.png)

No painel **Email/SMS/Push/Voice**, clique em **OK**. No painel **Adicionar grupo de ações**, clique em **OK**.

Para personalizar o assunto do email de alerta, em **Criar regra**, em **Personalizar ações**, selecione **Assunto do email**. Quando terminar, selecione **Criar regra de alerta**. O alerta indica quando uma implantação de atualização é bem-sucedida e quais computadores fazem parte da execução de implantação de atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Em seguida, agende uma implantação que siga o agendamento de versão e o período de serviço para instalar as atualizações. Você pode escolher os tipos de atualização que deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

>[!NOTE]
>O agendamento de uma implantação de atualização cria um recurso de [agenda](shared-resources/schedules.md) vinculado ao runbook **Patch-MicrosoftOMSComputers** que manipula a implantação de atualização nos computadores de destino. Se você excluir o recurso de agenda no portal do Azure ou usando o PowerShell depois de criar a implantação, a exclusão interromperá a implantação de atualização agendada e apresentará um erro quando você tentar reconfigurar o recurso de agenda no portal. Você só pode excluir o recurso de agendamento excluindo o agendamento de implantação correspondente.  

Para agendar uma nova implantação de atualização para a VM, vá para **Gerenciamento de atualizações** e selecione **Agendar implantação de atualização**.

Em **Nova implantação de atualização**, especifique as seguintes informações:

* **Name**: insira um nome exclusivo para a implantação de atualização.

* **Sistema operacional**: selecione o sistema operacional de destino para a implantação de atualização.

* **Grupos para atualizar (versão prévia)** : defina uma consulta que combina assinatura, grupos de recursos, locais e tags para criar um grupo dinâmico de VMs do Azure para incluir em sua implantação. Para saber mais, confira [Grupos dinâmicos](automation-update-management-groups.md).

* **Computadores para atualizar**: selecione uma Pesquisa salva, um Grupo importado ou selecione **Computadores** no menu suspenso e selecione computadores individuais. Se você escolher **Computadores**, a preparação de cada computador será mostrada na coluna **Preparação para atualização do agente**. Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, confira [Grupos de computadores nos logs do Azure Monitor](../azure-monitor/platform/computer-groups.md).

* **Classificação de atualização**: para cada produto, desmarque todas as classificações de atualização com suporte, exceto as que serão incluídas na implantação da atualização. Para este tutorial, deixe todos os tipos selecionados para todos os produtos.

  Os tipos de classificação são:

   |Sistema operacional  |Type  |
   |---------|---------|
   |Windows     | Atualizações críticas</br>Atualizações de segurança</br>Pacotes cumulativos de atualização</br>Feature packs</br>Service packs</br>Atualizações de definição</br>Ferramentas</br>Atualizações<br>Driver        |
   |Linux     | Atualizações críticas ou de segurança</br>Outras atualizações       |

   Para obter descrições dos tipos de classificação, confira [Classificações de atualização](automation-view-update-assessments.md#update-classifications).

* **Atualizações a serem incluídas/excluídas** – abre a página Incluir/Excluir. As atualizações a serem incluídas ou excluídas estão em guias separadas, especificando os números de ID do artigo da base de dados de conhecimento. Ao especificar um ou mais números de ID, você precisa remover ou desmarcar todas as classificações com a implantação de atualização. Isso verifica se nenhuma outra atualização foi incluída em seu pacote de atualização ao especificar IDs de atualização.

> [!NOTE]
> É importante saber que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão de `*`, o Gerenciamento de Atualizações não instalará nenhum patch ou pacote, pois todos serão excluídos. Correções excluídas ainda são mostradas como ausentes do computador. Para computadores Linux, se você incluir um pacote que tem um pacote dependente que foi excluído, o Gerenciamento de Atualizações não instalará o pacote principal.

> [!NOTE]
> Não é possível especificar atualizações que foram substituídas para inclusão na implantação de atualização.
>

* **Configurações da agenda**: O painel **Configurações da agenda** é aberto. A hora de início padrão é 30 minutos após a hora atual. Você pode definir a hora de início para qualquer momento a partir de 10 minutos.

   Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Em **Recorrência**, selecione **Uma vez**. Deixe o padrão como 1 dia e clique em **OK**. Essas entradas configuram um agenda recorrente.

* **Pré-scripts + pós-scripts**: Selecione os scripts a serem executados antes e depois de sua implantação. Para saber mais, consulte [Gerenciar pré e pós-scripts](pre-post-scripts.md).

* **Janela de manutenção (minutos)** : Mantenha o valor padrão. As janelas de manutenção controlam o tempo permitido para a instalação das atualizações. Considere os seguintes detalhes ao especificar uma janela de manutenção:

  * As janelas de manutenção controlam o número de atualizações que são instaladas.
  * O Gerenciamento de Atualizações não interromperá a instalação de novas atualizações se o fim de uma janela de manutenção estiver se aproximando.
  * O Gerenciamento de Atualizações não encerrará as atualizações em andamento se a janela de manutenção for excedida.
  * Se a janela de manutenção é excedida no Windows, isso geralmente ocorre devido a uma longa instalação de atualização do service pack.

  > [!NOTE]
  > Para evitar atualizações aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para saber mais sobre como configurar o pacote, veja [o tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opções de reinicialização**: use para especificar opções para manipular reinicializações. As seguintes opções estão disponíveis:
  * Reinicializar se necessário (padrão)
  * Sempre reinicializar
  * Nunca reinicializar
  * Somente reinicialização – não instala atualizações

> [!NOTE]
> As chaves do Registro listadas em [Chaves do Registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) poderão causar um evento de reinicialização se a opção **Opções de reinicialização** estiver definida como **Nunca reinicializar**.

Quando terminar de configurar o agenda, clique em **Criar**.

![Painel Configurações de agendamento de atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Você é retornado ao painel de status. Selecione **Implantações de atualização agendadas** para mostrar a agenda de implantação que você acabou de criar.

> [!NOTE]
> O Gerenciamento de Atualizações dá suporte à implantação de atualizações próprias e patches baixados previamente. Esse suporte requer alterações nos sistemas aos quais o patch está sendo aplicado. Confira [Suporte próprio e de pré-download](automation-configure-windows-update.md) para saber como definir essas configurações em seus sistemas.

Crie também implantações de atualizações de forma programática. Para saber como criar uma implantação de atualizações com a API REST, confira [Configurações de atualização de software – Criar](/rest/api/automation/softwareupdateconfigurations/create). Há também um runbook de exemplo que pode ser usado para criar uma implantação de atualizações semanal. Para saber mais sobre este runbook, consulte [Criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** em **Gerenciamento de Atualizações**. O status é **Em andamento** se a implantação está em execução no momento. Quando a implantação for concluída com êxito, o status será alterado para **Êxito**. Se houver falha em uma ou mais atualizações na implantação, o status será **Falha parcial**.

Selecione a implantação de atualizações concluída para ver o painel.

![Painel de status de implantação de atualização para uma implantação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Em **Resultados da atualização** há um resumo do número total de atualizações e resultados de implantação na VM. A tabela à direita mostra uma análise detalhada de cada atualização e os resultados da instalação.

Os valores disponíveis são:

* **Nenhuma tentativa**: a atualização não foi instalada devido ao tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Bem-sucedido**: A atualização foi bem-sucedida.
* **Falhou**: Falha na atualização.

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros da implantação.

Quando sua implantação de atualização for realizada com êxito, você receberá um email de confirmação semelhante ao seguinte:

![Configurar o grupo de ação de email](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Integrar uma VM para o Gerenciamento de Atualizações
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

Prossiga para a visão geral da solução de Gerenciamento de Atualizações.

> [!div class="nextstepaction"]
> [Solução Gerenciamento de Atualizações](automation-update-management.md)