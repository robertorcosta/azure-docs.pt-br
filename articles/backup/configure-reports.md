---
title: Configurar relatórios de Backup do Azure
description: Configurar e exibir relatórios para o Backup do Azure usando o Log Analytics e as pastas de trabalho do Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 5d1c7d628a61e550aa9dc4a5265ae16c5ed5336a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513618"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

Um requisito comum para administradores de backup é obter insights sobre backups com base em dados que abrangem um longo período. Os casos de uso para essa solução incluem:

- Alocação e previsão do armazenamento em nuvem consumido.
- Auditoria de backups e restaurações.
- Identificação das principais tendências em diferentes níveis de granularidade.

Hoje, o Backup do Azure fornece uma solução de relatório que usa os [logs do Azure Monitor](../azure-monitor/log-query/get-started-portal.md) e as [pastas de trabalho do Azure](../azure-monitor/platform/workbooks-overview.md). Esses recursos ajudam você a obter informações detalhadas sobre os backups em toda a sua propriedade de backups. Este artigo explica como configurar e ver Relatórios de Backup do Azure.

## <a name="supported-scenarios"></a>Cenários com suporte

- Há suporte para Relatórios de Backup em VMs do Azure, no SQL em VMs do Azure, no SAP HANA em VMs do Azure, no agente do MARS (Serviços de Recuperação do Microsoft Azure), no MABS (Servidor de Backup do Microsoft Azure) e no System Center DPM (Data Protection Manager). Para o backup do compartilhamento de arquivos do Azure, os dados são exibidos para todos os registros criados em ou após 1 de junho de 2020.
- Para cargas de trabalho do DPM, há suporte para Relatórios de Backup no DPM versão 5.1.363.0 e superior e no Agente versão 2.0.9127.0 e superior.
- Para cargas de trabalho do MABS, há suporte para Relatórios de Backup no MABS versão 13.0.415.0 e superior e no Agente versão 2.0.9170.0 e superior.
- Os Relatórios de Backup podem ser exibidos em todos os itens de backup, cofres, assinaturas e regiões, desde que os dados estejam sendo enviados para um workspace do Log Analytics ao qual o usuário tenha acesso. Para exibir os relatórios de um conjunto de cofres, você só precisa ter acesso de leitor ao workspace do Log Analytics ao qual os cofres estão enviando os dados. Você não precisa ter acesso aos cofres individuais.
- Se você for um usuário do [Azure Lighthouse](../lighthouse/index.yml) com acesso delegado às assinaturas dos clientes, poderá usar esses relatórios com o Azure Lighthouse para ver os relatórios em todos os seus locatários.
- Atualmente, os dados podem ser exibidos nos Relatórios de Backup em um máximo de 100 workspaces do Log Analytics (entre locatários).
- Atualmente, os dados para trabalhos de backup de log não são exibidos nos relatórios.

## <a name="get-started"></a>Introdução

Siga estas etapas para começar a usar os relatórios.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Criar um workspace do Log Analytics ou usar um existente

Configure um ou mais workspaces do Log Analytics para armazenar seus dados de relatório de backup. A localização e a assinatura em que esse workspace do Log Analytics pode ser criado são independentes da localização e da assinatura em que os cofres estão.

Para configurar um workspace do Log Analytics, confira [Criar um workspace do Log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md).

Por padrão, os dados de um workspace do Log Analytics são mantidos por 30 dias. Para ver os dados em um horizonte de tempo maior, altere o período de retenção do workspace do Log Analytics. Para alterar o período de retenção, confira [Gerenciar o uso e os custos com os logs do Azure Monitor](../azure-monitor/platform/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Definir as configurações de diagnóstico para os cofres

Os recursos do Azure Resource Manager, como os cofres dos Serviços de Recuperação, registram informações sobre as operações agendadas e as operações disparadas pelo usuário como dados de diagnóstico.

Na seção de monitoramento do cofre dos Serviços de Recuperação, selecione **Configurações de diagnóstico** e especifique o destino dos dados de diagnóstico do cofre dos Serviços de Recuperação. Para saber mais sobre como usar eventos de diagnóstico, confira [Usar configurações de diagnóstico para os cofres dos Serviços de Recuperação](./backup-azure-diagnostic-events.md).

![Painel Configurações de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O Backup do Azure também fornece uma definição interna do Azure Policy, que automatiza a definição de configurações de diagnóstico para todos os cofres em determinado escopo. Para saber como usar essa política, confira [Definir as configurações de diagnóstico do cofre em escala](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> Depois que você configurar o diagnóstico, poderá levar até 24 horas para que o push de dados inicial seja concluído. Depois que os dados começam a fluir para o workspace do Log Analytics, talvez você não veja os dados nos relatórios imediatamente, pois os dados do dia parcial atual não são mostrados nos relatórios. Para obter mais informações, confira [Convenções usadas em Relatórios de Backup](#conventions-used-in-backup-reports). Recomendamos que você comece a ver os relatórios dois dias depois de configurar os cofres para enviar dados para o Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Ver os relatórios no portal do Azure

Depois de configurar os cofres para enviar dados ao Log Analytics, veja os Relatórios de Backup acessando qualquer painel do cofre e selecionando **Relatórios de Backup**.

![Painel do cofre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecione este link para abrir a pasta de trabalho de relatório de Backup.

> [!NOTE]
>
> - Atualmente, a carga inicial do relatório pode levar até 1 minuto.
> - O cofre dos Serviços de Recuperação é meramente um ponto de entrada para os Relatórios de Backup. Depois que a pasta de trabalho do relatório de Backup for aberta no painel de um cofre, selecione o conjunto apropriado de workspaces do Log Analytics para ver os dados agregados em todos os cofres.

O relatório contém várias guias:

- **Resumo**: use essa guia para obter uma visão geral de alto nível da sua propriedade de backups. Obtenha uma visão rápida do número total de itens de backup, do armazenamento em nuvem total consumido, do número de instâncias protegidas e da taxa de sucesso do trabalho por tipo de carga de trabalho. Para obter informações mais detalhadas sobre um tipo de artefato de backup específico, acesse as respectivas guias.

   ![Guia Resumo](./media/backup-azure-configure-backup-reports/summary.png)

- **Itens de Backup**: use essa guia para ver informações e tendências sobre o armazenamento em nuvem consumido em um nível de item de Backup. Por exemplo, se você usar o SQL em um backup de VM do Azure, poderá ver o armazenamento em nuvem consumido para cada Banco de Dados SQL que está sendo copiado em backup. Você também pode optar por ver os dados de itens de backup de um status de proteção específico. Por exemplo, a seleção do bloco **Proteção Interrompida** na parte superior da guia filtra todos os widgets abaixo para mostrar os dados somente dos itens de backup no estado Proteção Interrompida.

   ![Guia Itens de Backup](./media/backup-azure-configure-backup-reports/backup-items.png)

- **Uso**: use essa guia para ver os principais parâmetros de cobrança dos backups. As informações mostradas nessa guia estão em um nível de entidade de cobrança (contêiner protegido). Por exemplo, no caso de um servidor DPM cujo backup está sendo feito no Azure, você pode ver a tendência das instâncias protegidas e o armazenamento em nuvem consumido do servidor DPM. Da mesma forma, se você usar o SQL ou o SAP HANA no Backup do Azure, essa guia fornecerá informações relacionadas ao uso no nível da máquina virtual na qual esses bancos de dados estão contidos.

   ![Guia Uso](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> Para cargas de trabalho do DPM, os usuários podem ver uma pequena diferença (da ordem de 20 MB por servidor DPM) entre os valores de uso mostrados nos relatórios em comparação com o valor de uso agregado, conforme mostrado na guia Visão geral do cofre dos Serviços de Recuperação. Essa diferença é contabilizada pelo fato de que cada servidor DPM que está sendo registrado para backup tem uma fonte de dados de 'metadados' associada que não está sendo exibida como um artefato para relatórios.

- **Trabalhos**: use essa guia para ver as tendências de execução longa nos trabalhos, como o número de trabalhos com falha por dia e as principais causas de falha do trabalho. Veja essas informações em um nível de agregação e de item de Backup. Selecione um item de Backup específico em uma grade para ver informações detalhadas sobre cada trabalho disparado nesse item de Backup no intervalo de tempo selecionado.

   ![Guia Trabalhos](./media/backup-azure-configure-backup-reports/jobs.png)

- **Políticas**: use essa guia para ver informações sobre todas as políticas ativas, como o número de itens associados e o armazenamento em nuvem total consumido por itens de backup em determinada política. Selecione uma política específica para ver informações sobre cada um dos itens de backup associados.

   ![Guia Políticas](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportar para o Excel

Selecione o botão de seta para baixo no canto superior direito de qualquer widget, como uma tabela ou um gráfico, para exportar o conteúdo desse widget como uma planilha do Excel no estado em que se encontra com os filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, aumente o número de linhas exibidas na página usando a seta suspensa **Linhas por Página** na parte superior de cada grade.

## <a name="pin-to-dashboard"></a>Fixar no painel

Selecione o botão Fixar na parte superior de cada widget para fixar o widget ao painel do portal do Azure. Esse recurso ajuda você a criar painéis personalizados adaptados para exibir as informações mais importantes de que você precisa.

## <a name="cross-tenant-reports"></a>Relatórios entre locatários

Se você usar o [Azure Lighthouse](../lighthouse/index.yml) com acesso delegado a assinaturas em vários ambientes de locatário, poderá usar o filtro de assinatura padrão. Selecione o botão Filtro no canto superior direito do portal do Azure para escolher todas as assinaturas para as quais deseja ver os dados. Isso permite que você selecione workspaces do Log Analytics nos seus locatários para ver relatórios de multilocatário.

## <a name="conventions-used-in-backup-reports"></a>Convenções usadas em Relatórios de Backup

- Os filtros funcionam da esquerda para a direita e de cima para baixo em cada guia. Ou seja, qualquer filtro só se aplica aos widgets posicionados à direita desse filtro ou abaixo dele.
- A seleção de um bloco colorido filtra os widgets abaixo do bloco para os registros que pertencem ao valor desse bloco. Por exemplo, a seleção do bloco **Proteção Interrompida** na guia **Itens de Backup** filtra as grades e os gráficos abaixo para mostrar os dados de itens de backup no estado Proteção Interrompida.
- Os blocos não coloridos não são clicáveis.
- Os dados do dia parcial atual não são mostrados nos relatórios. Portanto, quando o valor selecionado de **Intervalo de Tempo** for **Últimos 7 dias**, o relatório mostrará os registros dos últimos sete dias concluídos. O dia atual não está incluído.
- O relatório mostra os detalhes dos trabalhos (além dos trabalhos de log) que foram *disparados* no intervalo de tempo selecionado.
- Os valores mostrados para **Armazenamento em Nuvem** e **Instâncias Protegidas** estão no *final* do intervalo de tempo selecionado.
- Os itens de Backup exibidos nos relatórios são aqueles que estão no *final* do intervalo de tempo selecionado. Os itens de Backup que foram excluídos no meio do intervalo de tempo selecionado não são exibidos. A mesma convenção também se aplica às políticas de Backup.

## <a name="query-load-times"></a>Tempos de carregamento de consulta

Os widgets do relatório de Backup são ativados por consultas Kusto, que são executadas nos workspaces do Log Analytics do usuário. Essas consultas normalmente envolvem o processamento de grandes quantidades de dados, com várias junções para proporcionar insights mais sofisticados. Como resultado, os widgets podem não ser carregados instantaneamente quando o usuário vê os relatórios em uma propriedade de backup grande. Esta tabela fornece uma estimativa aproximada do tempo que diferentes widgets podem levar para serem carregados, com base no número de itens de Backup e no intervalo de tempo no qual o relatório está sendo exibido.

| **Nº de fontes de dados**                         | **Horizonte de tempo** | **Tempos de carregamento aproximados**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 mil                       | 1 mês          | Blocos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros no nível do relatório: 5-10 segundos|
| ~5 mil                       | 3 meses          | Blocos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros no nível do relatório: 5-10 segundos|
| ~10 mil                       | 3 meses          | Blocos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 1 a 2 minutos <br> Filtros no nível do relatório: 25-30 segundos|
| ~15 mil                       | 1 mês          | Blocos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 50-60 segundos <br> Filtros no nível do relatório: 20-25 segundos|
| ~15 mil                       | 3 meses          | Blocos: 20-30 segundos <br> Grades: 20-30 segundos <br> Gráficos: 2 a 3 minutos <br> Filtros no nível do relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a>O que aconteceu com os relatórios do Power BI?

- O aplicativo de modelo anterior do Power BI para relatórios, que obtinha dados de uma conta de armazenamento do Azure, está em um caminho de substituição. Recomendamos que você comece a enviar os dados de diagnóstico do cofre ao Log Analytics para ver os relatórios.

- Além disso, o [esquema V1](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) de envio dos dados de diagnóstico para uma conta de armazenamento ou um workspace do LA também está em um caminho de substituição. Isso significa que, se você tiver escrito qualquer consulta ou automação personalizada com base no esquema V1, precisará atualizar essas consultas para usar o esquema V2 atualmente compatível.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre monitoramento e relatórios com o Backup do Azure](./backup-azure-monitor-alert-faq.md)
