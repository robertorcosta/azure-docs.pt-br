---
title: Gerenciar atualizações e patches para as VMs do Azure
description: Este artigo fornece uma visão geral de como usar o Gerenciamento de Atualizações da Automação do Azure para gerenciar atualizações e patches das VMs Windows do Azure.
services: automation
author: zjalexander
ms.service: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 12/04/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: a2d13833b60076caa371a7fa8a696ab5964a28e3
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376076"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gerenciar atualizações e patches para as VMs do Azure

Você pode usar a solução Gerenciamento de Atualizações para gerenciar atualizações e patches de suas máquinas virtuais. Neste tutorial, você aprende a avaliar rapidamente o status das atualizações disponíveis, a agendar a instalação das atualizações necessárias e revisar os resultados da implantação e criar um alerta para verificar se as atualizações foram aplicadas com sucesso.

Para obter informações sobre preços, consulte [Preços de automação do Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Integrar uma VM para o Gerenciamento de Atualizações
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar seu crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [Conta da Automação do Azure](automation-offering-get-started.md) para manter os runbooks observador e de ação e a tarefa do observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Primeiro, habilite o Gerenciamento de Atualizações da VM para este tutorial:

1. No Portal do Azure, no menu esquerdo, selecione **Máquinas virtuais**. Selecione uma VM na lista.
2. Na página da VM, em **OPERAÇÕES**, selecione **Gerenciamento de atualização**. O painel **Habilitar Gerenciamento de Atualizações** é aberto.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado para essa VM. Essa validação inclui verificar se há um espaço de trabalho do Azure Log Analytics e uma conta de automação vinculada e se a solução Gerenciamento de Atualizações está no espaço de trabalho.

Um workspace do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) é usado para coletar dados gerados por recursos e serviços, como o Gerenciamento de Atualizações. O workspace fornece um único local para examinar e analisar dados de várias fontes.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Runbook Worker da Automação. Esse agente é usado para comunicar-se com a Automação do Azure e obter informações sobre o status de atualização. O agente requer que a porta 443 esteja aberta para se comunicar com o serviço de automação do Azure e realizar o download de atualizações.

Se algum dos seguintes pré-requisitos estiver ausente durante a integração, ele será adicionado automaticamente:

* Workspace do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* Uma [conta da Automação](./automation-offering-get-started.md)
* Um [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) (habilitado na VM)

Em **Gerenciamento de Atualizações**, defina o local, o espaço de trabalho do Log Analytics e a conta da Automação do Azure a serem usados. Em seguida, selecione **Habilitar**. Se essas opções não estão disponíveis, isso significa que outra solução de automação está habilitada para a VM. Nesse caso, o mesmo workspace e conta da Automação devem ser usados.

![Habilitar a janela da solução Gerenciamento de Atualizações](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

A habilitação da solução pode levar alguns minutos. Durante esse tempo, não feche a janela do navegador. Depois que a solução for habilitada, as informações sobre atualizações ausentes na VM fluirão para os logs do Azure Monitor. Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o Gerenciamento de Atualizações for habilitado, o painel **Gerenciamento de Atualizações** será aberto. Se alguma atualização estiver faltando, uma lista de atualizações ausentes aparece na guia **Atualizações ausentes**.

Em **INFORMATION LINK**, selecione o link de atualização para abrir o artigo de suporte da atualização em uma nova janela. Você pode ver informações importantes sobre a atualização nessa janela.

![Exibir o status de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Clique em qualquer lugar na atualização para abrir o painel **Pesquisa de Logs** da atualização selecionada. A consulta da pesquisa de log é predefinida para essa atualização específica. Modifique ou crie sua própria consulta para exibir informações detalhadas sobre as atualizações implantadas ou ausentes no ambiente.

![Exibir o status de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Nesta etapa, você aprenderá a configurar um alerta para informar o status da implantação de uma atualização.

### <a name="alert-conditions"></a>Condições de alerta

Em sua Conta de Automação, em **Monitoramento**, acesse **Alertas** e, em seguida, clique em **+ Nova regra de alerta**.

Sua Conta de Automação já está selecionada como o recurso. Se quiser alterá-lo, você poderá clicar em **Selecionar** e, na página **Selecionar um recurso**, selecionar **Contas de Automação** na lista suspensa **Filtrar por tipo de recurso**. Selecione sua Conta de automação, depois selecione **Concluído**.

Clique em **Adicionar condição** para selecionar o sinal adequado para sua implantação de atualização. A tabela a seguir mostra os detalhes dos dois sinais disponíveis para implantações de atualização:

|Nome do sinal|Dimensões|DESCRIÇÃO|
|---|---|---|
|**Total de execuções da implantação de atualização**|– Nome da implantação de atualização</br>– Status|Esse sinal é usado para alertar quanto ao status geral de uma implantação de atualização.|
|**Total de execuções de computador da implantação de atualização**|– Nome da implantação de atualização</br>– Status</br>– Computador de destino</br>– ID de execução da implantação de atualizações|Esse sinal é usado para alertar quanto ao status de uma implantação de atualização voltada para computadores específicos.|

Para os valores de dimensão, selecione um valor válido na lista. Se o valor que você está procurando não estiver na lista, clique no sinal **\+** ao lado da dimensão e digite o nome personalizado. Em seguida, você pode selecionar o valor desejado a ser procurado. Se quiser selecionar todos os valores de uma dimensão, clique no botão **Selecionar \*** . Se você não escolher um valor para uma dimensão, essa dimensão será ignorada durante a avaliação.

![Configurar sinal lógico](./media/automation-tutorial-update-management/signal-logic.png)

Em **Lógica de alerta**, para **Limite**, digite **1**. Quando tiver terminado, selecione **Concluído**.

### <a name="alert-details"></a>Detalhes do Alerta

Em **2. Defina os detalhes do alerta** e insira um nome e uma descrição para o alerta. Definir a **Gravidade** para **Informational(Sev 2)** para uma execução bem-sucedida ou **Informational(Sev 1)** para uma execução com falha.

![Configurar sinal lógico](./media/automation-tutorial-update-management/define-alert-details.png)

Em **Grupos de ações**, selecione **Criar Novo**. Um grupo de ação é um grupo de ações que você pode usar através de vários alertas. As ações podem incluir, dentre outras, notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

Na caixa **Nome do grupo de ação**, digite um nome para o alerta e um nome curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

Em **Ações**, insira um nome para a ação, como **Notificações por Email**. Em **TIPO DE AÇÃO**, selecione **Email/SMS/Push/Voz**. Em **DETALHES**, selecione **Editar detalhes**.

No painel **Email/SMS/Push/Voz**, digite um nome. Marque a caixa de seleção **Email** e digite um endereço de email válido.

![Configurar um grupo de ação de email](./media/automation-tutorial-update-management/configure-email-action-group.png)

No painel **Email/SMS/Push/Voice**, selecione **OK**. No painel **Adicionar grupo de ação**, selecione **OK**.

Para personalizar o assunto do email de alerta, em **Criar regra**, em **Personalizar Ações**, selecione **Assunto do email**. Quando terminar, selecione **Criar regra de alerta**. O alerta indica quando uma implantação de atualização for bem-sucedida e quais computadores fazem parte da execução de implantação de atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Em seguida, agende uma implantação que siga o agendamento de versão e o período de serviço para instalar as atualizações. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Para agendar uma nova implantação de atualização para a VM, vá para **Gerenciamento de atualizações** e selecione **Agendar implantação de atualização**.

Em **Nova implantação de atualização**, especifique as seguintes informações:

* **Nome**: insira um nome exclusivo para a implantação de atualização.

* **Sistema operacional**: selecione o sistema operacional de destino para a implantação de atualização.

* **Grupos para atualizar (versão prévia)** : Defina uma consulta com base em uma combinação de assinatura, grupos de recursos, locais e tags para criar um grupo dinâmico de VMs do Azure para incluir em sua implantação. Para obter mais informações, consulte [grupos dinâmicos](automation-update-management-groups.md)

* **Computadores para atualizar**: Selecione uma pesquisa salva, um grupo importado ou selecione a máquina na lista suspensa e selecione máquinas individuais. Se você escolher **Machines**, a prontidão da máquina é mostrada na coluna **UPDATE AGENT READINESS**. Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../azure-monitor/platform/computer-groups.md)

* **Classificação de atualização**: selecione os tipos de software que a implantação de atualização incluiu na implantação. Para este tutorial, deixe todos os tipos selecionados.

  Os tipos de classificação são:

   |SO  |Type  |
   |---------|---------|
   |Windows     | Atualizações críticas</br>Atualizações de segurança</br>Pacotes cumulativos de atualização</br>Feature packs</br>Service packs</br>Atualizações de definição</br>Ferramentas</br>Atualizações        |
   |Linux     | Atualizações críticas ou de segurança</br>Outras atualizações       |

   Para obter uma descrição dos tipos de classificação, consulte [classificações de atualização](automation-view-update-assessments.md#update-classifications).

* **Atualizações a serem incluídas/excluídas** – Isso abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em guias separadas.

> [!NOTE]
> É importante saber que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão de `*`, nenhuma correção ou pacote será instalado, pois todos serão excluídos. Correções excluídas ainda são mostradas como ausentes do computador. Para computadores Linux, se um pacote estiver incluído, mas tiver um pacote dependente excluído, o pacote não será instalado.

* **Configurações da agenda**: O painel **Configurações da agenda** é aberto. A hora de início padrão é 30 minutos após a hora atual. Você pode definir a hora de início para qualquer momento a partir de 10 minutos.

   Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Em **Recorrência**, selecione **Uma vez**. Deixe o padrão como 1 dia e selecione **OK**. Isso configura um agendamento recorrente.

* **Pré-scripts + pós-scripts**: Selecione os scripts a serem executados antes e depois de sua implantação. Para saber mais, consulte [Gerenciar pré e pós-scripts](pre-post-scripts.md).

* **Janela de manutenção (minutos)** : Mantenha o valor padrão. As janelas de manutenção controlam o tempo permitido para a instalação das atualizações. Considere os detalhes a seguir ao especificar uma janela de manutenção.

  * As janelas de manutenção controlam o número de tentativas de instalação das atualizações.
  * O Gerenciamento de Atualizações não interrompe a instalação de novas atualizações se o fim de uma janela de manutenção está se aproximando.
  * O Gerenciamento de Atualizações não encerra as atualizações em andamento se a janela de manutenção é excedida.
  * Se a janela de manutenção é excedida no Windows, isso geralmente ocorre devido a uma longa instalação de atualização do service pack.

  > [!NOTE]
  > Informações relacionadas não críticas a uma tarefa Para evitar atualizações aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote Unattended-Upgrade para desabilitar as atualizações automáticas. Para saber mais sobre como configurar o pacote, veja [o tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opções de reinicialização**: essa configuração determina como reinicializações devem ser tratadas. As opções disponíveis são:
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

* **Nenhuma tentativa**: a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Êxito**: A atualização foi bem-sucedida.
* **Falha**: falha na atualização.

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros da implantação.

Quando a implantação de atualização é bem-sucedida, um email semelhante ao exemplo a seguir é enviado para mostrar o êxito da implantação:

![Configurar o grupo de ação de email](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Integrar uma VM para o Gerenciamento de Atualizações
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

Prossiga para a visão geral da solução de Gerenciamento de Atualizações.

> [!div class="nextstepaction"]
> [Solução Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)

