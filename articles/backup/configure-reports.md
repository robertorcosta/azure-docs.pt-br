---
title: Configurar relatórios de Backup do Azure
description: Configure e visualize relatórios para backup do Azure usando log analytics e workbooks do Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 497d70c1bcc577faa467720b959eb828e785a26a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672658"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

Um requisito comum para os admins de backup é obter insights sobre backups, com base em dados que abrangem um longo período de tempo. Poderia haver vários casos de uso para tal solução - alocar e prever o armazenamento em nuvem consumido, auditar backups e restaurações e identificar tendências-chave em diferentes níveis de granularidade.

Hoje, o Azure Backup fornece uma solução de relatórios que aproveita [os logs do Monitor do Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e [do Azure Workbooks,](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)ajudando você a obter insights ricos sobre seus backups em toda a sua propriedade de backup. Este artigo explica como configurar e visualizar relatórios de backup.

## <a name="supported-scenarios"></a>Cenários com suporte

* Os Relatórios de Backup são suportados para VMs Azure, SQL em VMs Azure, SAP HANA/ASE em VMs Azure, Azure Backup Agent (MARS), Azure Backup Server (MABS) e System Center DPM.
* Para cargas de trabalho dpm, os relatórios de backup são suportados para dpm versão 5.1.363.0 ou superior, e Agent Versão 2.0.9127.0 ou superior.
* Para cargas de trabalho MABS, os Relatórios de Backup são suportados para a versão 13.0.415.0 ou superior do MABS e para a versão do agente 2.0.9170.0 ou superior.
* Os Relatórios de backup podem ser visualizados em todos os itens de backup, cofres, assinaturas e regiões, desde que seus dados sejam enviados para um Espaço de Trabalho do Log Analytics (LA) ao que o usuário tenha acesso. Observe que para visualizar relatórios para um conjunto de cofres, você só precisa ter **acesso do leitor ao Espaço de Trabalho la** para o qual os cofres estão enviando seus dados. Você **não precisa** ter acesso aos cofres individuais.
* Se você é um usuário [do Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado às assinaturas de seus clientes, você pode usar esses relatórios com o Azure Lighthouse para visualizar relatórios em todos os seus inquilinos.
* Os dados para trabalhos de backup de log não são exibidos atualmente nos relatórios.

## <a name="getting-started"></a>Introdução

Para começar com o uso dos relatórios, siga as três etapas detalhadas abaixo:

1. **Crie um espaço de trabalho de Log Analytics (LA) (ou use um já existente):**

Você precisa configurar um ou mais espaços de trabalho la para armazenar seus dados de relatórios de backup. A localização e a assinatura onde este espaço de trabalho de LA pode ser criado é independente da localização e assinatura onde seus cofres existem. 

Consulte o seguinte artigo: [Crie um espaço de trabalho do Log Analytics no portal Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) para configurar um espaço de trabalho LA.

Por padrão, os dados em um espaço de trabalho la são retidos por 30 dias. Para ver dados para um horizonte de tempo mais longo, altere o período de retenção do Espaço de Trabalho la. Para alterar o período de retenção, consulte o seguinte artigo: [Gerenciar o uso e os custos com logs do Monitor Do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Configure as configurações de diagnóstico para seus cofres:**

Os recursos do Azure Resource Manager, como cofres do Recovery Services, registram informações sobre operações programadas e operações desencadeadas pelo usuário como dados de diagnóstico. 

Na seção de monitoramento do cofre dos Serviços de Recuperação, selecione **as configurações de Diagnóstico** e especifique o destino dos dados de diagnóstico do cofre do Recovery Services. [Saiba mais sobre o uso de eventos diagnósticos.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

![Lâmina de configurações de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O Azure Backup também fornece uma diretiva azure incorporada, que automatiza a configuração de configurações de diagnóstico para todos os cofres em um determinado escopo. Consulte o artigo a seguir para saber como usar esta política: [Configure configurações de diagnóstico de cofre em escala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> Uma vez configurado o diagnóstico, pode levar até 24 horas para que os dados iniciais sejam concluídos. Uma vez que os dados começam a fluir para o Espaço de Trabalho de LA, você pode não ser capaz de ver os dados nos relatórios imediatamente, uma vez que os dados para o dia parcial atual não são mostrados nos relatórios (mais detalhes [aqui](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)). Portanto, recomenda-se começar a visualizar os relatórios 2 dias após configurar seus cofres para enviar dados ao Log Analytics.

3. **Veja relatórios no portal Azure:**

Depois de configurar seus cofres para enviar dados para LA, visualize seus relatórios de backup navegando na lâmina de qualquer cofre e clicando no item do menu **Relatórios de backup.** 

![Painel do cofre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Clicar neste link abre a carteira de trabalho do relatório de backup.

> [!NOTE]
> * Atualmente, a carga inicial do relatório pode levar até 1 minuto.
> * O cofre dos Serviços de Recuperação é apenas um ponto de entrada para relatórios de backup. Uma vez que a Agenda de Relatórios de Backup é aberta a partir da lâmina de um cofre, você poderá ver dados agregados em todos os seus cofres (selecionando o conjunto apropriado de ESPAÇOS de trabalho de LA).

Abaixo está uma descrição das várias guias que o relatório contém:

* **Resumo** - A guia Resumo fornece uma visão geral de alto nível do seu patrimônio de backup. Na guia Resumo, você pode obter uma rápida olhada no número total de itens de backup, armazenamento total em nuvem consumido, número de instâncias protegidas e a taxa de sucesso do trabalho por tipo de trabalho. Para obter informações mais detalhadas sobre um tipo específico de artefato de backup, navegue até as respectivas guias.

![Guia Resumo](./media/backup-azure-configure-backup-reports/summary.png)

* **Itens de backup** - A guia Itens de backup permite que você veja informações e tendências sobre o armazenamento em nuvem consumidos em um nível de itens de backup. Por exemplo, se você estiver usando SQL no backup do Azure VM, você poderá ver o armazenamento em nuvem consumido para cada banco de dados SQL sendo feito backup. Você também pode optar por ver dados para itens de backup de um status de proteção específico. Por exemplo, clicando no bloco **de proteção Parado** na parte superior da guia, filtra todos os widgets abaixo para mostrar dados apenas para itens de backup no estado de proteção parada.

![Guia Itens de backup](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Uso** - A guia Uso ajuda você a visualizar os principais parâmetros de cobrança para seus backups. As informações mostradas nesta guia estão em um nível de entidade de cobrança (contêiner protegido). Por exemplo, no caso de um servidor DPM ser feito em backup no Azure, você pode visualizar a tendência de instâncias protegidas e armazenamento em nuvem consumidos para o servidor DPM. Da mesma forma, se você estiver usando SQL no Azure Backup ou SAP HANA no Azure Backup, esta guia lhe dá informações relacionadas ao uso no nível da máquina virtual em que esses bancos de dados estão contidos.

![Guia de uso](./media/backup-azure-configure-backup-reports/usage.png)

* **Empregos** - A guia Empregos permite que você veja tendências de longo prazo sobre empregos, como o número de empregos fracassados por dia e as principais causas de falha no emprego. Você pode visualizar essas informações em um nível agregado e em um nível de item de backup. Clicar em um item de backup específico em uma grade permite que você visualize informações detalhadas sobre cada trabalho que foi acionado nesse item de backup no intervalo de tempo selecionado.

![Guia Trabalhos](./media/backup-azure-configure-backup-reports/jobs.png)

* **Políticas** - A guia Políticas permite que você visualize informações sobre todas as suas políticas ativas, como o número de itens associados e o armazenamento total em nuvem consumido por itens copiados sob uma determinada política. Clicar em uma diretiva específica permite que você visualize informações sobre cada um de seus itens de backup associados.

![Guia políticas](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exportando para Excel

Clicar no botão de seta para baixo no canto superior direito de qualquer widget (tabela/gráfico) exporta o conteúdo desse widget como uma folha de Excel, como está com os filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, você pode aumentar o número de linhas exibidas na página usando a **queda de linhas por página** na parte superior de cada grade.

## <a name="pinning-to-dashboard"></a>Fixando-se no painel

Clique no ícone pin na parte superior de cada widget para fixar o widget no painel do portal Azure. Isso ajuda você a criar painéis personalizados sob medida para exibir as informações mais importantes que você precisa.

## <a name="cross-tenant-reports"></a>Relatórios de inquilinos cruzados

Se você é um usuário [do Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado a assinaturas em vários ambientes de inquilinos, você pode usar o filtro de assinatura padrão (clicando no ícone do filtro no canto superior direito do portal Azure) para escolher todas as assinaturas para as quais deseja ver os dados. Isso permitirá que você selecione espaços de trabalho de LA entre seus inquilinos para visualizar relatórios multi-locatários.

## <a name="conventions-used-in-backup-reports"></a>Convenções usadas em relatórios de backup

* Os filtros funcionam da esquerda para a direita e de cima para baixo em cada guia, ou seja, qualquer filtro só se aplica a todos os widgets que estão posicionados à direita desse filtro ou abaixo desse filtro. 
* Clicar em um azulejo colorido filtra os widgets abaixo do azulejo para registros relativos ao valor desse azulejo. Por exemplo, clicar no bloco *de proteção Parado* na guia Itens de backup filtra as grades e gráficos abaixo para mostrar dados de itens de backup no estado 'Proteção parada'.
* Os azulejos que não são coloridos não são clicáveis.
* Os dados do dia parcial atual não são mostrados nos relatórios. Assim, quando o valor selecionado do Intervalo de **Tempo** é, ***Últimos 7 dias,*** o relatório mostra registros dos últimos 7 dias concluídos (o que não inclui o dia atual).
* O relatório mostra detalhes de Jobs (além de trabalhos de registro) que foram **acionados** no intervalo de tempo selecionado. 
* Os valores mostrados para armazenamento em nuvem e instâncias protegidas estão no **final** do intervalo de tempo selecionado.
* Os itens de backup exibidos nos relatórios são os itens que existem no **final** do intervalo de tempo selecionado. Os itens de backup que foram excluídos no meio do intervalo de tempo selecionado não são exibidos. A mesma convenção também se aplica às Políticas de Backup.

## <a name="query-load-times"></a>Tempos de carga de consulta

Os widgets no Relatório de backup são alimentados por consultas kusto, que são executadas nos espaços de trabalho la do usuário. Como essas consultas normalmente envolvem o processamento de grandes quantidades de dados, com várias adesões para permitir insights mais ricos, os widgets podem não ser carregados instantaneamente quando o usuário está visualizando relatórios em uma grande propriedade de backup. A tabela abaixo fornece uma estimativa aproximada do tempo que diferentes widgets podem levar para carregar, com base no número de itens de backup e no intervalo de tempo para o qual o relatório está sendo visualizado:

| **# Datasources**                         | **Horizonte de Tempo** | **Load Times (aprox.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 mês          | Telhas: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| ~5 K                       | 3 meses          | Telhas: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| ~10 Mil                       | 3 meses          | Telhas: 15-20 seg <br> Grades: 15-20 seg <br> Gráficos: 1-2 min <br> Filtros de nível de relatório: 25-30 segundos|
| ~15 Mil                       | 1 mês          | Telhas: 15-20 seg <br> Grades: 15-20 seg <br> Gráficos: 50-60 seg <br> Filtros de nível de relatório: 20-25 segundos|
| ~15 Mil                       | 3 meses          | Telhas: 20-30 seg <br> Grades: 20-30 seg <br> Gráficos: 2-3 min <br> Filtros em nível de relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a>O que aconteceu com os Relatórios de Power BI?
* Nosso aplicativo de modelo power bi anterior para emissão de relatórios (que origem de dados de uma conta de armazenamento do Azure) está em um caminho de depreciação. Recomenda-se começar a enviar dados de diagnóstico do cofre para o Log Analytics, conforme descrito acima, para visualizar relatórios.

* Além disso, o esquema V1 de enviar dados de diagnóstico para uma conta de armazenamento ou um Espaço de Trabalho LA também está em um caminho de depreciação. Isso significa que, se você tiver escrito quaisquer consultas ou automações personalizadas com base no esquema V1, você é aconselhado a atualizar essas consultas para usar o esquema V2 suportado atualmente.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre monitoramento e emissão de relatórios com o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)