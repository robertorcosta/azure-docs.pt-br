---
title: Gerenciar atualizações e patches para as VMs do Azure
description: Este artigo fornece uma visão geral de como usar o Gerenciamento de Atualizações da Automação do Azure para gerenciar atualizações e patches das VMs do Azure e não Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: 347f2fbc0f12aa775c42dbb14a4625dc509a20ed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373052"
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

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Para concluir este tutorial, você precisará:

* A solução [Gerenciamento de atualizações](automation-update-management.md) habilitada para uma ou mais de suas VMs.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o Gerenciamento de Atualizações for habilitado, o painel **Gerenciamento de Atualizações** será aberto. Se alguma atualização for identificada como ausente, uma lista de atualizações ausentes aparecerá na guia **Atualizações ausentes**.

Em **link de informações**, selecione o link atualizar para abrir o artigo de suporte para a atualização. Você pode ver informações importantes sobre a atualização.

![Exibir o status de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Clique em qualquer outro lugar na atualização para abrir o painel de **pesquisa de log** para a atualização selecionada. A consulta da pesquisa de log é predefinida para essa atualização específica. Modifique ou crie sua própria consulta para exibir informações detalhadas sobre as atualizações implantadas ou ausentes no ambiente.

![Exibir o status de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Nesta etapa, você aprenderá a configurar um alerta para informar o status da implantação de uma atualização.

### <a name="alert-conditions"></a>Condições de alerta

Em sua Conta de Automação, em **Monitoramento**, acesse **Alertas** e, em seguida, clique em **+ Nova regra de alerta**.

Sua Conta de Automação já está selecionada como o recurso. Se quiser alterá-lo, você poderá clicar em **Selecionar** e, na página **Selecionar um recurso**, selecionar **Contas de Automação** na lista suspensa **Filtrar por tipo de recurso**. Selecione sua Conta de automação, depois selecione **Concluído**.

Clique em **Adicionar condição** para selecionar o sinal adequado para sua implantação de atualização. A tabela a seguir mostra os detalhes dos dois sinais disponíveis para implantações de atualização:

|Nome do sinal|Dimensions|Descrição|
|---|---|---|
|**Total de execuções da implantação de atualização**|– Nome da implantação de atualização</br>– Status|Esse sinal é usado para alertar quanto ao status geral de uma implantação de atualização.|
|**Total de execuções de computador da implantação de atualização**|– Nome da implantação de atualização</br>– Status</br>– Computador de destino</br>– ID de execução da implantação de atualizações|Esse sinal é usado para alertar quanto ao status de uma implantação de atualização voltada para computadores específicos.|

Para os valores de dimensão, selecione um valor válido na lista. Se o valor que você está procurando não estiver na lista, clique no sinal **\+** ao lado da dimensão e digite o nome personalizado. Em seguida, você pode selecionar o valor desejado a ser procurado. Se quiser selecionar todos os valores de uma dimensão, clique no botão **Selecionar \*** . Se você não escolher um valor para uma dimensão, essa dimensão será ignorada durante a avaliação.

![Configurar sinal lógico](./media/automation-tutorial-update-management/signal-logic.png)

Em **Lógica de alerta**, para **Limite**, digite **1**. Quando tiver terminado, selecione **Concluído**.

### <a name="alert-details"></a>Detalhes do Alerta

Em **2. Defina os detalhes do alerta**, insira um nome e uma descrição para o alerta. Definir a **Gravidade** para **Informational(Sev 2)** para uma execução bem-sucedida ou **Informational(Sev 1)** para uma execução com falha.

![Configurar sinal lógico](./media/automation-tutorial-update-management/define-alert-details.png)

Em **Grupos de ações**, selecione **Criar Novo**. Um grupo de ação é um grupo de ações que você pode usar através de vários alertas. As ações podem incluir, dentre outras, notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

Na caixa **Nome do grupo de ação**, digite um nome para o alerta e um nome curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

Em **ações**, insira um nome para a ação, como **notificações por email**. Em **tipo de ação**, selecione **email/SMS/Push/voz**. Em **detalhes**, selecione **Editar detalhes**.

No painel **Email/SMS/Push/Voz**, digite um nome. Marque a caixa de seleção **Email** e digite um endereço de email válido.

![Configurar um grupo de ação de email](./media/automation-tutorial-update-management/configure-email-action-group.png)

No painel **email/SMS/Push/voz** , selecione **OK**. No painel **Adicionar grupo de ações** , selecione **OK**.

Para personalizar o assunto do email de alerta, em **criar regra**, em **Personalizar ações**, selecione **assunto do email**. Quando terminar, selecione **Criar regra de alerta**. O alerta indica quando uma implantação de atualização for bem-sucedida e quais computadores fazem parte da execução de implantação de atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Em seguida, agende uma implantação que siga o agendamento de versão e o período de serviço para instalar as atualizações. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

>[!NOTE]
>Quando você agenda uma implantação de atualização, ela cria um recurso de [agendamento](shared-resources/schedules.md) vinculado ao runbook **Patch-MicrosoftOMSComputers** que processa a implantação de atualização nos computadores de destino. Se você excluir o recurso de agendamento no portal do Azure ou usando o PowerShell depois de criar a implantação, ele interromperá a implantação de atualização agendada e apresentará um erro quando você tentar reconfigurá-la no portal. Você só pode excluir o recurso de agendamento excluindo o agendamento de implantação correspondente.  
>

Para agendar uma nova implantação de atualização para a VM, vá para **Gerenciamento de atualizações** e selecione **Agendar implantação de atualização**.

Em **Nova implantação de atualização**, especifique as seguintes informações:

* **Nome**: insira um nome exclusivo para a implantação da atualização.

* **Sistema operacional**: escolha o sistema operacional de destino para a implantação de atualização.

* **Grupos para atualizar (versão prévia)** : defina uma consulta com base em uma combinação de assinatura, grupos de recursos, locais e marcas para compilar um grupo dinâmico de VMs do Azure a ser incluído na implantação. Para obter mais informações, consulte [grupos dinâmicos](automation-update-management-groups.md)

* **Computadores para atualização**: selecione uma Pesquisa salva, um Grupo importado ou selecione Computador na lista suspensa e selecione computadores individuais. Se você escolher **computadores**, a prontidão do computador será mostrada na coluna **prontidão do agente de atualização** . Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../azure-monitor/platform/computer-groups.md)

* **Classificação de atualização**: selecione as classificações de atualização com suporte disponíveis para cada produto que pode ser incluído na implantação da atualização. Para este tutorial, deixe todos os tipos selecionados.

  Os tipos de classificação são:

   |Sistema operacional  |Tipo  |
   |---------|---------|
   |Portal     | Atualizações críticas</br>Atualizações de segurança</br>Pacotes cumulativos de atualização</br>Feature packs</br>Service packs</br>Atualizações de definição</br>Ferramentas</br>Atualizações        |
   |Linux     | Atualizações críticas ou de segurança</br>Outras atualizações       |

   Para obter uma descrição dos tipos de classificação, consulte [classificações de atualização](automation-view-update-assessments.md#update-classifications).

* **Atualizações a serem incluídas/excluídas** – Isso abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em guias separadas.

> [!NOTE]
> É importante saber que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão de `*`, nenhuma correção ou pacote será instalado, pois todos serão excluídos. Correções excluídas ainda são mostradas como ausentes do computador. Para computadores Linux, se um pacote estiver incluído, mas tiver um pacote dependente excluído, o pacote não será instalado.

> [!NOTE]
> Você não pode especificar atualizações que foram substituídas para inclusão na implantação de atualização.
>

* **Agendar configurações**: isso abre o painel **Agendar configurações**. A hora de início padrão é 30 minutos após a hora atual. Você pode definir a hora de início para qualquer momento a partir de 10 minutos.

   Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Em **Recorrência**, selecione **Uma vez**. Deixe o padrão como 1 dia e selecione **OK**. Isso configura um agendamento recorrente.

* **Pré-scripts + pós-scripts**: selecione os scripts a serem executados antes e após sua implantação. Para saber mais, consulte [Gerenciar pré e pós-scripts](pre-post-scripts.md).

* **Janela de manutenção (minutos)** : deixe o valor padrão. As janelas de manutenção controlam o tempo permitido para a instalação das atualizações. Considere os detalhes a seguir ao especificar uma janela de manutenção.

  * As janelas de manutenção controlam o número de tentativas de instalação das atualizações.
  * O Gerenciamento de Atualizações não interrompe a instalação de novas atualizações se o fim de uma janela de manutenção está se aproximando.
  * O Gerenciamento de Atualizações não encerra as atualizações em andamento se a janela de manutenção é excedida.
  * Se a janela de manutenção é excedida no Windows, isso geralmente ocorre devido a uma longa instalação de atualização do service pack.

  > [!NOTE]
  > Para evitar atualizações aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para saber mais sobre como configurar o pacote, veja [o tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opções de reinicialização**: essa configuração determina como a reinicializações deve ser tratada. As opções disponíveis são:
  * Reinicialização, se necessário (Padrão)
  * Sempre reinicializar
  * Nunca reinicializar
  * Somente reinicialização - não instalará as atualizações

> [!NOTE]
> As chaves do Registro listadas em [Chaves do Registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) poderão causar um evento de reinicialização se a opção **Controle de Reinicialização** estiver definida como **Nunca Reinicializar**.

Quando terminar de configurar a agenda, selecione **Criar**.

![Painel Configurações de agendamento de atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Você é retornado ao painel de status. Selecione **Implantações de atualização agendadas** para mostrar a agenda de implantação que você criou.

> [!NOTE]
> O Gerenciamento de Atualizações dá suporte à implantação de atualizações próprias e patches baixados previamente. Isso requer alterações nos sistemas em correção, confira [suporte próprio e de pré-download](automation-configure-windows-update.md) para saber como definir essas configurações em seus sistemas.

As **Implantações de Atualizações** também podem ser criadas de forma programática. Para saber como criar uma **Implantação de Atualizações** com a API REST, confira [Configurações de atualização de software – Criar](/rest/api/automation/softwareupdateconfigurations/create). Há também um runbook de exemplo que pode ser usado para criar uma **Implantação de Atualizações** semanal. Para saber mais sobre este runbook, consulte [Criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** em **Gerenciamento de Atualizações**. O status é **Em andamento** se a implantação está em execução no momento. Quando a implantação for concluída, se for bem-sucedida, o status mudará para **Êxito**. Se houver falha em uma ou mais atualizações na implantação, o status será **Falha Parcial**.

Selecione a implantação de atualização concluída para ver o painel dessa implantação de atualização.

![Painel de status de implantação de atualização para uma implantação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Em **Resultados da atualização** há um resumo do número total de atualizações e resultados de implantação na VM. A tabela à direita mostra uma análise detalhada de cada atualização e os resultados da instalação.

A lista a seguir mostra os valores disponíveis:

* **Não foi tentada**: a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Êxito**: a atualização foi bem-sucedida.
* **Falha**: a atualização falhou.

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros da implantação.

Quando a implantação de atualização é bem-sucedida, um email semelhante ao exemplo a seguir é enviado para mostrar o êxito da implantação:

![Configurar o grupo de ação de email](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Integrar uma VM para o Gerenciamento de Atualizações
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

Prossiga para a visão geral da solução de Gerenciamento de Atualizações.

> [!div class="nextstepaction"]
> [Solução Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)

