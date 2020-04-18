---
title: Configurar relatórios de Backup do Azure
description: Configure e visualize relatórios para backup do Azure usando as regras de registro e adoção do Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617812"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

Um requisito comum para os admins de backup é obter insights sobre backups com base em dados que abrangem um longo período de tempo. Os casos de uso para tal solução incluem:

 - Alocação e previsão de armazenamento em nuvem consumido.
 - Auditoria de backups e restaurações.
 - Identificar tendências-chave em diferentes níveis de granularidade.

Hoje, o Azure Backup fornece uma solução de relatórios que usa [registros do Monitor Do Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e livros de trabalho do [Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks). Esses recursos ajudam você a obter insights ricos sobre seus backups em toda a sua propriedade de backup. Este artigo explica como configurar e visualizar relatórios de backup do Azure.

## <a name="supported-scenarios"></a>Cenários com suporte

* Os relatórios de backup são suportados para VMs Azure, SQL em VMs Azure, SAP HANA/ASE em VMs Azure, agente Microsoft Azure Recovery Services (MARS), Microsoft Azure Backup Server (MABS) e DPM (System Center Data Protection Manager).
* Para cargas de trabalho dpm, os relatórios de backup são suportados para dpm versão 5.1.363.0 ou superior e Agent Versão 2.0.9127.0 ou superior.
* Para cargas de trabalho MABS, os relatórios de backup são suportados para a versão 13.0.415.0 e superior ao MABS e à Versão do Agente 2.0.9170.0 ou superior.
* Os relatórios de backup podem ser visualizados em todos os itens de backup, cofres, assinaturas e regiões, desde que seus dados sejam enviados para um espaço de trabalho do Log Analytics ao que o usuário tenha acesso. Para visualizar relatórios para um conjunto de cofres, você só precisa ter acesso do leitor ao espaço de trabalho do Log Analytics para o qual os cofres estão enviando seus dados. Você não precisa ter acesso aos cofres individuais.
* Se você é um usuário [do Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado às assinaturas de seus clientes, você pode usar esses relatórios com o Azure Lighthouse para ver relatórios em todos os seus inquilinos.
* Os dados para trabalhos de backup de log atualmente não são exibidos nos relatórios.

## <a name="get-started"></a>Introdução

Siga estas etapas para começar a usar os relatórios.

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Crie um espaço de trabalho do Log Analytics ou use um já existente

Configure um ou mais espaços de trabalho do Log Analytics para armazenar seus dados de relatórios de backup. A localização e a assinatura onde este espaço de trabalho do Log Analytics pode ser criado é independente da localização e assinatura onde seus cofres existem. 

Para configurar um espaço de trabalho do Log Analytics, consulte [Criar um espaço de trabalho do Log Analytics no portal Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Por padrão, os dados em um espaço de trabalho do Log Analytics são retidos por 30 dias. Para ver dados por um horizonte de tempo mais longo, altere o período de retenção do espaço de trabalho do Log Analytics. Para alterar o período de retenção, consulte [Gerenciar o uso e os custos com logs do Monitor Do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Configure as configurações de diagnóstico para seus cofres

Os recursos do Azure Resource Manager, como cofres do Recovery Services, registram informações sobre operações programadas e operações desencadeadas pelo usuário como dados de diagnóstico. 

Na seção de monitoramento do cofre dos Serviços de Recuperação, selecione **configurações de Diagnóstico** e especifique o destino dos dados de diagnóstico do cofre do Recovery Services. Para saber mais sobre o uso de eventos [diagnósticos, consulte Usar configurações de diagnóstico para cofres dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Painel Configurações de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O Azure Backup também fornece uma política incorporada do Azure, que automatiza a configuração das configurações de diagnóstico para todos os cofres em um determinado escopo. Para saber como usar essa política, consulte [Configure configurações de diagnóstico de cofre em escala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics).

> [!NOTE]
> Depois de configurar o diagnóstico, pode levar até 24 horas para que os dados iniciais sejam concluídos. Depois que os dados começarem a fluir para o espaço de trabalho do Log Analytics, você pode não ver os dados nos relatórios imediatamente porque os dados do dia parcial atual não são mostrados nos relatórios. Para obter mais informações, consulte [Convenções usadas em relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Recomendamos que você comece a visualizar os relatórios dois dias depois de configurar seus cofres para enviar dados ao Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Veja relatórios no portal Azure

Depois de configurar seus cofres para enviar dados ao Log Analytics, visualize seus relatórios de backup indo ao painel de qualquer cofre e selecionando **Relatórios de backup**.

![Painel do cofre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecione este link para abrir a agenda de trabalho do relatório backup.

> [!NOTE]
> * Atualmente, a carga inicial do relatório pode levar até 1 minuto.
> * O cofre dos Serviços de Recuperação é apenas um ponto de entrada para relatórios de backup. Depois que a pasta de trabalho do relatório backup for aberta a partir do painel de um cofre, selecione o conjunto apropriado de espaços de trabalho do Log Analytics para ver os dados agregados em todos os seus cofres.

O relatório contém várias guias:

* **Resumo**: Use esta guia para obter uma visão geral de alto nível da sua propriedade de backup. Você pode obter uma rápida olhada no número total de itens de backup, armazenamento total na nuvem consumido, o número de instâncias protegidas e a taxa de sucesso do trabalho por tipo de carga de trabalho. Para obter informações mais detalhadas sobre um tipo específico de artefato de backup, acesse as respectivas guias.

   ![Guia Resumo](./media/backup-azure-configure-backup-reports/summary.png)

* **Itens de backup**: Use esta guia para ver informações e tendências sobre o armazenamento em nuvem consumidos em um nível de backup. Por exemplo, se você usar o SQL em um backup do Azure VM, poderá ver o armazenamento em nuvem consumido para cada banco de dados SQL que está sendo feito backup. Você também pode optar por ver dados para itens de backup de um status de proteção específico. Por exemplo, selecionar o azulejo parado de **proteção** na parte superior da guia filtra todos os widgets abaixo para mostrar dados apenas para itens de backup no estado Proteção Parada.

   ![Guia Itens de backup](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Utilização**: Use esta guia para visualizar os principais parâmetros de cobrança de seus backups. As informações mostradas nesta guia estão em um nível de entidade de cobrança (contêiner protegido). Por exemplo, no caso de um servidor DPM ser feito em backup no Azure, você pode visualizar a tendência de instâncias protegidas e armazenamento em nuvem consumidos para o servidor DPM. Da mesma forma, se você usar SQL no Azure Backup ou SAP HANA no Azure Backup, esta guia lhe dá informações relacionadas ao uso no nível da máquina virtual em que esses bancos de dados estão contidos.

   ![Guia de uso](./media/backup-azure-configure-backup-reports/usage.png)

* **Empregos**: Use esta guia para ver tendências de longo prazo sobre empregos, como o número de empregos fracassados por dia e as principais causas de falha no emprego. Você pode visualizar essas informações em um nível agregado e em um nível de backup. Selecione um item de backup específico em uma grade para exibir informações detalhadas sobre cada trabalho que foi acionado nesse item de backup no intervalo de tempo selecionado.

   ![Guia Trabalhos](./media/backup-azure-configure-backup-reports/jobs.png)

* **Políticas**: Use esta guia para exibir informações sobre todas as suas políticas ativas, como o número de itens associados e o armazenamento total em nuvem consumido por itens copiados sob uma determinada política. Selecione uma diretiva específica para exibir informações sobre cada um de seus itens de backup associados.

   ![Guia políticas](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportar para o Excel

Selecione o botão de seta para baixo no canto superior direito de qualquer widget, como uma tabela ou gráfico, para exportar o conteúdo desse widget como uma folha de Excel como está com filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, você pode aumentar o número de linhas exibidas na página usando a seta suspensa **Linhas por página** na parte superior de cada grade.

## <a name="pin-to-dashboard"></a>Fixar no painel

Selecione o botão de pino na parte superior de cada widget para fixar o widget no painel do portal Azure. Esse recurso ajuda você a criar painéis personalizados sob medida para exibir as informações mais importantes que você precisa.

## <a name="cross-tenant-reports"></a>Relatórios de inquilinos cruzados

Se você usar [o Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado a assinaturas em vários ambientes de inquilinos, você pode usar o filtro de assinatura padrão. Selecione o botão de filtro no canto superior direito do portal Azure para escolher todas as assinaturas para as quais você deseja ver os dados. Isso permite selecionar espaços de trabalho do Log Analytics entre seus inquilinos para visualizar relatórios multilocatários.

## <a name="conventions-used-in-backup-reports"></a>Convenções usadas em relatórios de backup

* Os filtros funcionam da esquerda para a direita e de cima para baixo em cada guia. Ou seja, qualquer filtro só se aplica a todos os widgets que estão posicionados à direita desse filtro ou abaixo desse filtro. 
* A seleção de um azulejo colorido filtra os widgets abaixo do azulejo para registros que dizem respeito ao valor desse azulejo. Por exemplo, selecionar o bloco **de proteção Parado** na guia **Itens de backup** filtra as grades e gráficos abaixo para mostrar dados de itens de backup no estado Proteção Parada.
* Ladrilhos que não são coloridos não são clicáveis.
* Os dados do dia parcial atual não são mostrados nos relatórios. Assim, quando o valor selecionado do Intervalo de **Tempo** é **de 7 dias,** o relatório mostra registros dos últimos sete dias concluídos. O dia atual não está incluído.
* O relatório mostra detalhes dos trabalhos (além dos trabalhos de registro) que foram *acionados* no intervalo de tempo selecionado. 
* Os valores mostrados para armazenamento em **nuvem** e **instâncias protegidas** estão no *final* do intervalo de tempo selecionado.
* Os itens de backup exibidos nos relatórios são os itens que existem no *final* do intervalo de tempo selecionado. Os itens de backup que foram excluídos no meio do intervalo de tempo selecionado não são exibidos. A mesma convenção também se aplica às políticas de backup.

## <a name="query-load-times"></a>Tempos de carga de consulta

Os widgets no relatório Backup são alimentados por consultas kusto, que são executadas nos espaços de trabalho log analytics do usuário. Essas consultas geralmente envolvem o processamento de grandes quantidades de dados, com várias adesões para permitir insights mais ricos. Como resultado, os widgets podem não ser carregados instantaneamente quando o usuário visualiza relatórios em uma grande propriedade de backup. Esta tabela fornece uma estimativa aproximada do tempo que diferentes widgets podem levar para carregar, com base no número de itens de backup e no intervalo de tempo para o qual o relatório está sendo visualizado.

| **# Fontes de dados**                         | **Horizonte de tempo** | **Tempos aproximados de carga**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 mês          | Telhas: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| ~5 K                       | 3 meses          | Telhas: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| ~10 Mil                       | 3 meses          | Telhas: 15-20 seg <br> Grades: 15-20 seg <br> Gráficos: 1-2 min <br> Filtros de nível de relatório: 25-30 segundos|
| ~15 Mil                       | 1 mês          | Telhas: 15-20 seg <br> Grades: 15-20 seg <br> Gráficos: 50-60 seg <br> Filtros de nível de relatório: 20-25 segundos|
| ~15 Mil                       | 3 meses          | Telhas: 20-30 seg <br> Grades: 20-30 seg <br> Gráficos: 2-3 min <br> Filtros em nível de relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a> O que aconteceu com os relatórios do Power BI? 
* O aplicativo de modelo Power BI anterior para emissão de relatórios, que forneceu dados de uma conta de armazenamento do Azure, está em um caminho de depreciação. Recomendamos que você comece a enviar dados de diagnóstico do cofre para o Log Analytics para visualizar relatórios.

* Além disso, o esquema V1 de enviar dados de diagnóstico para uma conta de armazenamento ou um espaço de trabalho do Log Analytics também está em um caminho de depreciação. Se você escreveu quaisquer consultas ou automações personalizadas com base no esquema V1, é aconselhável atualizar essas consultas para usar o esquema V2 suportado atualmente.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre monitoramento e emissão de relatórios com o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
