---
title: Configurar relatórios de Backup do Azure
description: Configurar e exibir relatórios para o backup do Azure usando Log Analytics e pastas de trabalho do Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: cefe81e53e89b8d7903469e836f3c5d2665febea
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582697"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

Um requisito comum para administradores de backup é obter informações sobre backups, com base nos dados que abrangem um longo período de tempo. Pode haver vários casos de uso para essa solução – alocando e prevendo o armazenamento em nuvem consumido, auditando backups e restaurações e identificando as principais tendências em diferentes níveis de granularidade.

Hoje, o backup do Azure fornece uma solução de relatório que aproveita [os logs de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e [pastas de trabalho do Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks), ajudando você a obter informações detalhadas sobre seus backups em todo o seu espaço de backup. Este artigo explica como configurar e exibir relatórios de backup.

## <a name="supported-scenarios"></a>Cenários com suporte

* Os relatórios de backup têm suporte para VMs do Azure, SQL em VMs do Azure, SAP HANA/ASE em VMs do Azure, agente de backup do Azure (MARS), Servidor de Backup do Azure (MABS) e System Center DPM.
* Para cargas de trabalho do DPM, os relatórios de backup têm suporte para o DPM versão 5.1.363.0 e superior, e a versão do agente 2.0.9127.0 e superior.
* Para cargas de trabalho do MABS, os relatórios de backup têm suporte para o MABS versão 13.0.415.0 e superior, e a versão do agente 2.0.9170.0 e superior.
* Os relatórios de backup podem ser exibidos em todos os itens de backup, cofres, assinaturas e regiões, desde que seus dados estejam sendo enviados para um espaço de trabalho de Log Analytics (LA) ao qual o usuário tenha acesso. 
* Se você for um usuário [Lighthouse do Azure](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado às assinaturas de seus clientes, poderá usar esses relatórios com o Azure Lighthouse para exibir relatórios em todos os seus locatários.
* Os dados para trabalhos de backup de log não são exibidos atualmente nos relatórios.

## <a name="getting-started"></a>Introdução

Para começar a usar os relatórios, siga as três etapas detalhadas abaixo:

1. **Criar um espaço de trabalho Log Analytics (LA) (ou usar um existente):**

Você precisa configurar um ou mais espaços de trabalho da LA para armazenar seus dados de relatório de backup. O local e a assinatura em que esse espaço de trabalho de LA pode ser criado são independentes do local e da assinatura em que os cofres existem. 

Consulte o seguinte artigo: [criar um log Analytics espaço de trabalho no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) para configurar um espaço de trabalho de la.

Por padrão, os dados em um espaço de trabalho LA são mantidos por 30 dias. Para ver os dados de um horizonte de tempo maior, altere o período de retenção do espaço de trabalho LA. Para alterar o período de retenção, consulte o seguinte artigo: [gerenciar o uso e os custos com os logs de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Defina as configurações de diagnóstico para seus cofres:**

Azure Resource Manager recursos, como cofres dos serviços de recuperação, registram informações sobre operações agendadas e operações disparadas pelo usuário como dados de diagnóstico. 

Na seção monitoramento do cofre dos serviços de recuperação, selecione **configurações de diagnóstico** e especifique o destino para os dados de diagnóstico do cofre dos serviços de recuperação. [Saiba mais sobre como usar eventos de diagnóstico](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Folha configurações de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O backup do Azure também fornece um Azure Policy interno, que automatiza a configuração de configurações de diagnóstico para todos os cofres em um determinado escopo. Consulte o seguinte artigo para saber como usar essa política: [definir configurações de diagnóstico de cofre em escala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

3. **Exibir relatórios no portal do Azure:**

Depois de configurar seus cofres para enviar dados para LA, exiba seus relatórios de backup navegando até a folha de um cofre e clicando no item de menu **relatórios de backup** . 

![Painel do cofre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Clicar nesse link abre a pasta de trabalho de relatório de backup.

> [!NOTE]
> Atualmente, a carga inicial do relatório pode levar até 1 minuto.

Abaixo está uma descrição das várias guias que o relatório contém:

* **Resumo** -a guia Resumo fornece uma visão geral de alto nível do seu espaço de backup. Na guia Resumo, você pode obter uma visão rápida do número total de itens de backup, armazenamento em nuvem total consumido, o número de instâncias protegidas e a taxa de sucesso do trabalho por tipo de carga. Para obter informações mais detalhadas sobre um tipo de artefato de backup específico, navegue até as respectivas guias.

![Guia Resumo](./media/backup-azure-configure-backup-reports/summary.png)

* **Itens de backup** -a guia itens de backup permite que você veja informações e tendências sobre o armazenamento em nuvem consumidos em um nível de item de backup. Por exemplo, se você estiver usando o SQL no backup de VM do Azure, poderá ver o armazenamento em nuvem consumido para cada banco de dados SQL que está sendo submetido a backup. Você também pode optar por ver os dados para itens de backup de um status de proteção específico. Por exemplo, clicar no bloco **proteção interrompida** na parte superior da guia filtra todos os widgets abaixo para mostrar dados somente para itens de backup no estado proteção interrompida.

![Guia itens de backup](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Uso** -a guia uso ajuda a exibir parâmetros de cobrança de chave para seus backups. As informações mostradas nesta guia estão em um nível de entidade de cobrança (contêiner protegido). Por exemplo, no caso de um servidor DPM cujo backup está sendo feito no Azure, você pode exibir a tendência de instâncias protegidas e o armazenamento em nuvem consumidos para o servidor DPM. Da mesma forma, se você estiver usando o SQL no backup do Azure ou SAP HANA no backup do Azure, essa guia fornecerá informações relacionadas ao uso no nível da máquina virtual em que esses bancos de dados estão contidos.

![Guia uso](./media/backup-azure-configure-backup-reports/usage.png)

* **Trabalhos** – a guia trabalhos permite exibir tendências de longa execução em trabalhos, como o número de trabalhos com falha por dia e as principais causas de falha do trabalho. Você pode exibir essas informações em um nível de agregação e em um nível de item de backup. Clicar em um item de backup específico em uma grade permite exibir informações detalhadas sobre cada trabalho que foi disparado nesse item de backup no intervalo de tempo selecionado.

![Guia Trabalho](./media/backup-azure-configure-backup-reports/jobs.png)

* **Políticas** – a guia políticas permite exibir informações sobre todas as suas políticas ativas, como o número de itens associados e o armazenamento em nuvem total consumido por itens de backup em uma determinada política. Clicar em uma determinada política permite exibir informações sobre cada um de seus itens de backup associados.

![Guia políticas](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exportando para o Excel

Clicar no botão de seta para baixo na parte superior direita de qualquer widget (tabela/gráfico) exporta o conteúdo desse widget como uma planilha do Excel, como está com filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, você pode aumentar o número de linhas exibidas na página usando o menu suspenso **linhas por página** na parte superior de cada grade.

## <a name="pinning-to-dashboard"></a>Fixação no painel

Clique no ícone de pino na parte superior de cada widget para fixar o widget ao seu painel de portal do Azure. Isso ajuda a criar painéis personalizados adaptados para exibir as informações mais importantes de que você precisa.

## <a name="cross-tenant-reports"></a>Relatórios de locatário cruzado

Se você for um usuário [Lighthouse do Azure](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado a assinaturas em vários ambientes de locatário, poderá usar o filtro de assinatura padrão (clicando no ícone de filtro no canto superior direito do portal do Azure) para escolher todas as assinaturas das quais deseja ver os dados. Isso permitirá que você selecione a LA espaços de trabalho em seus locatários para exibir relatórios multilocatários.

## <a name="conventions-used-in-backup-reports"></a>Convenções usadas em relatórios de backup

* Os filtros funcionam da esquerda para a direita e de cima para baixo em cada guia, ou seja, qualquer filtro se aplica somente a todos os widgets posicionados à direita desse filtro ou abaixo desse filtro. 
* Clicar em um bloco colorido filtra os widgets abaixo do bloco para registros pertencentes ao valor desse bloco. Por exemplo, clicar no bloco *proteção interrompida* na guia itens de backup filtra as grades e os gráficos abaixo para mostrar os dados de itens de backup no estado ' proteção interrompida '.
* Blocos que não são coloridos não são clicáveis.
* Os dados do dia parcial atual não são mostrados nos relatórios. Assim, quando o valor selecionado de **intervalo de tempo** for, ***últimos 7 dias***, o relatório mostrará os registros dos últimos 7 dias concluídos (que não incluem o dia atual).
* O relatório mostra detalhes dos trabalhos (além dos trabalhos de log) que foram **disparados** no intervalo de tempo selecionado. 
* Os valores mostrados para armazenamento em nuvem e instâncias protegidas estão no **final** do intervalo de tempo selecionado.
* Os itens de backup exibidos nos relatórios são os itens que existem no **final** do intervalo de tempo selecionado. Os itens de backup que foram excluídos no meio do intervalo de tempo selecionado não são exibidos. A mesma convenção também se aplica a políticas de backup.

## <a name="query-load-times"></a>Tempos de carregamento de consulta

Os widgets no relatório de backup são alimentados por consultas Kusto, que são executadas nos espaços de trabalho da LA do usuário. Como essas consultas normalmente envolvem o processamento de grandes quantidades de dados, com várias junções para habilitar informações mais avançadas, os widgets podem não ser carregados instantaneamente quando o usuário estiver exibindo relatórios em um grande espaço de backup. A tabela a seguir fornece uma estimativa aproximada do tempo que os widgets diferentes podem executar para carregar, com base no número de itens de backup e no intervalo de tempo para o qual o relatório está sendo exibido:

| **N º de fontes de fonte**                         | **Horizonte de tempo** | **Tempos de carregamento (aproximadamente)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| aproximadamente 5 K                       | 1 mês          | Blocos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| aproximadamente 5 K                       | 3 meses          | Blocos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| ~ 10 K                       | 3 meses          | Blocos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 1-2 minutos <br> Filtros de nível de relatório: 25-30 segundos|
| ~ 15 K                       | 1 mês          | Blocos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 50-60 segundos <br> Filtros de nível de relatório: 20-25 segundos|
| ~ 15 K                       | 3 meses          | Blocos: 20-30 segundos <br> Grades: 20-30 segundos <br> Gráficos: 2-3 minutos <br> Filtros de nível de relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a>O que aconteceu com os relatórios de Power BI?
* Nosso aplicativo de modelo de Power BI anterior para relatórios (que dados originados de uma conta de armazenamento do Azure) está em um caminho de substituição. É recomendável iniciar o envio de dados de diagnóstico do cofre para Log Analytics conforme descrito acima, para exibir relatórios.

* Além disso, o esquema v1 de envio de dados de diagnóstico para uma conta de armazenamento ou um espaço de trabalho da LA também está em um caminho de substituição. Isso significa que, se você tiver escrito qualquer consulta ou automação personalizada com base no esquema v1, será aconselhável atualizar essas consultas para usar o esquema V2 com suporte no momento.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre monitoramento e relatórios com o backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)