---
title: Configurar relatórios de Backup do Azure
description: Configurar e exibir relatórios para o backup do Azure usando Log Analytics e pastas de trabalho do Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: c1af9a532b390b428e74957c455988dfd4df3967
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184938"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

Um requisito comum para administradores de backup é obter informações sobre backups com base em dados que abrangem um longo período de tempo. Os casos de uso para essa solução incluem:

- Alocação e previsão do armazenamento em nuvem consumidos.
- Auditoria de backups e restaurações.
- Identificação das principais tendências em diferentes níveis de granularidade.

Hoje, o backup do Azure fornece uma solução de relatório que usa [logs de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e [pastas de trabalho do Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks). Esses recursos ajudam você a obter informações detalhadas sobre seus backups em todo o seu espaço de backup. Este artigo explica como configurar e exibir relatórios de backup do Azure.

## <a name="supported-scenarios"></a>Cenários com suporte

- Os relatórios de backup têm suporte para VMs do Azure, SQL em VMs do Azure, SAP HANA/ASE em VMs do Azure, agente de Serviços de Recuperação do Microsoft Azure (MARS), Backup do Microsoft Azure Server (MABS) e System Center Data Protection Manager (DPM). Os dados do backup do compartilhamento de arquivos do Azure não estão visíveis no momento nos relatórios de backup.
- Para cargas de trabalho do DPM, os relatórios de backup têm suporte para o DPM versão 5.1.363.0 e superior e a versão do agente 2.0.9127.0 e superior.
- Para cargas de trabalho MABS, os relatórios de backup têm suporte para MABS versão 13.0.415.0 e superior e versão do agente 2.0.9170.0 e superior.
- Os relatórios de backup podem ser exibidos em todos os itens de backup, cofres, assinaturas e regiões, desde que seus dados estejam sendo enviados a um espaço de trabalho Log Analytics ao qual o usuário tenha acesso. Para exibir relatórios de um conjunto de cofres, você só precisa ter acesso de leitor ao espaço de trabalho Log Analytics ao qual os cofres estão enviando seus dados. Você não precisa ter acesso aos cofres individuais.
- Se você for um usuário [Lighthouse do Azure](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado às assinaturas de seus clientes, poderá usar esses relatórios com o Azure Lighthouse para exibir relatórios em todos os seus locatários.
- Os dados para trabalhos de backup de log atualmente não são exibidos nos relatórios.

## <a name="get-started"></a>Introdução

Siga estas etapas para começar a usar os relatórios.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. criar um espaço de trabalho Log Analytics ou usar um existente

Configure um ou mais espaços de trabalho Log Analytics para armazenar seus dados de relatório de backup. O local e a assinatura em que esse espaço de trabalho Log Analytics pode ser criado são independentes do local e da assinatura em que os cofres existem.

Para configurar um espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Por padrão, os dados em um espaço de trabalho Log Analytics são mantidos por 30 dias. Para ver os dados de um horizonte de tempo maior, altere o período de retenção do espaço de trabalho Log Analytics. Para alterar o período de retenção, consulte [gerenciar o uso e os custos com logs de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. definir configurações de diagnóstico para seus cofres

Azure Resource Manager recursos, como cofres dos serviços de recuperação, registram informações sobre operações agendadas e operações disparadas pelo usuário como dados de diagnóstico.

Na seção monitoramento do cofre dos serviços de recuperação, selecione **configurações de diagnóstico** e especifique o destino para os dados de diagnóstico do cofre dos serviços de recuperação. Para saber mais sobre como usar eventos de diagnóstico, confira [usar configurações de diagnóstico para cofres dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Painel Configurações de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O backup do Azure também fornece uma definição de Azure Policy interna, que automatiza a configuração de configurações de diagnóstico para todos os cofres em um determinado escopo. Para saber como usar essa política, consulte [definir configurações de diagnóstico de cofre em escala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics).

> [!NOTE]
> Depois de configurar o diagnóstico, pode levar até 24 horas para que o envio de dados inicial seja concluído. Depois que os dados começam a fluir para o espaço de trabalho Log Analytics, talvez você não veja os dados nos relatórios imediatamente, pois os dados do dia parcial atual não são mostrados nos relatórios. Para obter mais informações, consulte [convenções usadas em relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Recomendamos que você comece a exibir os relatórios dois dias depois de configurar seus cofres para enviar dados para Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. exibir relatórios no portal do Azure

Depois de configurar seus cofres para enviar dados para Log Analytics, exiba seus relatórios de backup acessando qualquer painel do cofre e selecionando **relatórios de backup**.

![Painel do cofre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecione este link para abrir a pasta de trabalho de relatório de backup.

> [!NOTE]
>
> - Atualmente, a carga inicial do relatório pode levar até 1 minuto.
> - O cofre dos serviços de recuperação é meramente um ponto de entrada para relatórios de backup. Depois que a pasta de trabalho de relatório de backup abrir no painel de um cofre, selecione o conjunto apropriado de espaços de trabalho de Log Analytics para ver os dados agregados em todos os seus cofres.

O relatório contém várias guias:

- **Resumo**: Use esta guia para obter uma visão geral de alto nível do seu espaço de backup. Você pode obter uma visão rápida do número total de itens de backup, o armazenamento em nuvem total consumido, o número de instâncias protegidas e a taxa de sucesso do trabalho por tipo de carga. Para obter informações mais detalhadas sobre um tipo de artefato de backup específico, vá para as respectivas guias.

   ![Guia Resumo](./media/backup-azure-configure-backup-reports/summary.png)

- **Itens de backup**: Use esta guia para ver informações e tendências sobre o armazenamento em nuvem consumidos em um nível de item de backup. Por exemplo, se você usar o SQL em um backup de VM do Azure, poderá ver o armazenamento em nuvem consumido para cada banco de dados SQL que está sendo submetido a backup. Você também pode optar por ver os dados para itens de backup de um status de proteção específico. Por exemplo, selecionar o bloco **proteção interrompida** na parte superior da guia filtra todos os widgets abaixo para mostrar dados somente para itens de backup no estado proteção interrompida.

   ![Guia itens de backup](./media/backup-azure-configure-backup-reports/backup-items.png)

- **Uso**: Use essa guia para exibir os principais parâmetros de cobrança para seus backups. As informações mostradas nesta guia estão em um nível de entidade de cobrança (contêiner protegido). Por exemplo, no caso de um servidor DPM cujo backup está sendo feito no Azure, você pode exibir a tendência de instâncias protegidas e o armazenamento em nuvem consumidos para o servidor DPM. Da mesma forma, se você usar o SQL no backup do Azure ou SAP HANA no backup do Azure, essa guia fornecerá informações relacionadas ao uso no nível da máquina virtual na qual esses bancos de dados estão contidos.

   ![Guia uso](./media/backup-azure-configure-backup-reports/usage.png)

- **Trabalhos**: Use essa guia para exibir tendências de longa execução em trabalhos, como o número de trabalhos com falha por dia e as principais causas de falha do trabalho. Você pode exibir essas informações em um nível de agregação e em um nível de item de backup. Selecione um item de backup específico em uma grade para exibir informações detalhadas sobre cada trabalho que foi disparado nesse item de backup no intervalo de tempo selecionado.

   ![Guia Trabalhos](./media/backup-azure-configure-backup-reports/jobs.png)

- **Políticas**: Use essa guia para exibir informações sobre todas as suas políticas ativas, como o número de itens associados e o armazenamento em nuvem total consumido por itens de backup em uma determinada política. Selecione uma política específica para exibir informações sobre cada um de seus itens de backup associados.

   ![Guia políticas](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportar para o Excel

Selecione o botão de seta para baixo no canto superior direito de qualquer widget, como uma tabela ou um gráfico, para exportar o conteúdo desse widget como uma planilha do Excel como está com filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, você pode aumentar o número de linhas exibidas na página usando a seta suspensa **linhas por página** na parte superior de cada grade.

## <a name="pin-to-dashboard"></a>Fixar no painel

Selecione o botão fixar na parte superior de cada widget para fixar o widget ao seu painel de portal do Azure. Esse recurso ajuda a criar painéis personalizados adaptados para exibir as informações mais importantes de que você precisa.

## <a name="cross-tenant-reports"></a>Relatórios de locatário cruzado

Se você usar o [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado a assinaturas em vários ambientes de locatário, você poderá usar o filtro de assinatura padrão. Selecione o botão filtro no canto superior direito do portal do Azure para escolher todas as assinaturas para as quais você deseja ver os dados. Isso permite que você selecione Log Analytics espaços de trabalho em seus locatários para exibir relatórios multilocatários.

## <a name="conventions-used-in-backup-reports"></a>Convenções usadas em relatórios de backup

- Os filtros funcionam da esquerda para a direita e de cima para baixo em cada guia. Ou seja, qualquer filtro se aplica somente a todos os widgets posicionados à direita desse filtro ou abaixo desse filtro.
- A seleção de um bloco colorido filtra os widgets abaixo do bloco para registros que pertencem ao valor desse bloco. Por exemplo, a seleção do bloco **proteção interrompida** na guia **itens de backup** filtra as grades e os gráficos abaixo para mostrar os dados de itens de backup no estado proteção interrompida.
- Blocos que não são coloridos não são clicáveis.
- Os dados do dia parcial atual não são mostrados nos relatórios. Assim, quando o valor selecionado do **intervalo de tempo** for dos **últimos 7 dias**, o relatório mostrará os registros dos últimos sete dias concluídos. O dia atual não está incluído.
- O relatório mostra detalhes dos trabalhos (além dos trabalhos de log) que foram *disparados* no intervalo de tempo selecionado.
- Os valores mostrados para o **armazenamento em nuvem** e as **instâncias protegidas** estão no *final* do intervalo de tempo selecionado.
- Os itens de backup exibidos nos relatórios são os itens que existem no *final* do intervalo de tempo selecionado. Os itens de backup que foram excluídos no meio do intervalo de tempo selecionado não são exibidos. A mesma convenção também se aplica a políticas de backup.

## <a name="query-load-times"></a>Tempos de carregamento de consulta

Os widgets no relatório de backup são alimentados por consultas Kusto, que são executadas nos espaços de trabalho do Log Analytics do usuário. Essas consultas normalmente envolvem o processamento de grandes quantidades de dados, com várias junções para habilitar informações mais ricas. Como resultado, os widgets podem não ser carregados instantaneamente quando o usuário exibe relatórios em um espaço de backup grande. Esta tabela fornece uma estimativa aproximada do tempo que os widgets diferentes podem tomar para carregar, com base no número de itens de backup e no intervalo de tempo para o qual o relatório está sendo exibido.

| **N º de fontes de dados**                         | **Horizonte de tempo** | **Tempos de carregamento aproximados**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| aproximadamente 5 K                       | 1 mês          | Blocos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| aproximadamente 5 K                       | 3 meses          | Blocos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| ~ 10 K                       | 3 meses          | Blocos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 1-2 minutos <br> Filtros de nível de relatório: 25-30 segundos|
| ~ 15 K                       | 1 mês          | Blocos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 50-60 segundos <br> Filtros de nível de relatório: 20-25 segundos|
| ~ 15 K                       | 3 meses          | Blocos: 20-30 segundos <br> Grades: 20-30 segundos <br> Gráficos: 2-3 minutos <br> Filtros de nível de relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a> O que aconteceu com os relatórios do Power BI? 

- O aplicativo de modelo de Power BI anterior para relatórios, que origina os dados de uma conta de armazenamento do Azure, está em um caminho de substituição. Recomendamos que você comece a enviar dados de diagnóstico do cofre para Log Analytics para exibir relatórios.

- * Além disso, o [esquema v1](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#v1-schema-vs-v2-schema) de envio de dados de diagnóstico para uma conta de armazenamento ou um espaço de trabalho da la também está em um caminho de substituição. Isso significa que, se você tiver escrito qualquer consulta ou automação personalizada com base no esquema v1, será aconselhável atualizar essas consultas para usar o esquema V2 com suporte no momento.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre monitoramento e relatórios com o backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
