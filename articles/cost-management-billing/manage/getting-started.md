---
title: Evitar e analisar encargos inesperados com o Gerenciamento de Custos e a Cobrança do Azure
description: Saiba como evitar encargos inesperados em sua fatura do Azure e usar os recursos de acompanhamento e gerenciamento de custos para sua conta do Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 61071655cfeaebd5a7e32a97abf208d03322164f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689908"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evite cobranças inesperadas com o gerenciamento de custo e a cobrança do Azure

Ao se inscrever no Azure, há várias coisas que podem ser feitas para você ter uma ideia melhor de seus gastos:

- Obtenha os custos estimados antes de adicionar serviços usando a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/), a tabela de preços do Azure ou durante a adição de serviços no portal do Azure.
- Monitore os custos com [orçamentos](../costs/tutorial-acm-create-budgets.md), [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) e [análise de custo](../costs/quick-acm-cost-analysis.md).
- Examine os encargos em sua fatura comparando-os com [arquivos de uso detalhado](download-azure-invoice-daily-usage-date.md).
- Integre os dados de cobrança e custo ao seu próprio sistema de relatórios usando APIs de [cobrança](https://docs.microsoft.com/rest/api/billing/) e [consumo](https://docs.microsoft.com/rest/api/consumption/).
- Use recursos e ferramentas adicionais para clientes do EA (Contrato Enterprise), do CSP (Provedor de Soluções na Nuvem) e do Azure Sponsorship.
- Use [alguns dos serviços mais populares do Azure gratuitamente por 12 meses](create-free-services.md) disponíveis com a [conta gratuita do Azure](https://azure.microsoft.com/free/). Juntamente com as recomendações listadas abaixo, consulte [Evitar ser cobrado na conta gratuita](avoid-charges-free-account.md).

Se você precisar cancelar sua assinatura do Azure, confira [Cancelar sua assinatura do Azure](cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obter custos estimados antes de adicionar serviços do Azure

Use uma das seguintes ferramentas para estimar o custo do uso de um serviço do Azure:
- Calculadora de preços do Azure
- Tabela de preços do Azure
- Portal do Azure

As imagens nas seções a seguir mostram os preços de exemplo em dólares americanos.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar custo online usando a calculadora de preços

Confira a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para obter uma estimativa do custo mensal do serviço que deseja adicionar. Altere a moeda para obter a estimativa em sua moeda local.

![Captura de tela do menu de calculadora de preços](./media/getting-started/pricing-calc.png)

Exiba o custo estimado de qualquer serviço interno do Azure. Por exemplo, na captura de tela abaixo, o custo de uma VM (Máquina Virtual) do Windows A1 é estimado como US$ 66,96 por mês em horas de computação se você deixá-la em execução o tempo todo:

![Captura de tela da calculadora de preços mostrando um custo estimado de VM Windows A1 por mês](./media/getting-started/pricing-calcvm.png)

Para obter mais informações sobre preços, confira as [Perguntas frequentes sobre Preços](https://azure.microsoft.com/pricing/faq/). Se desejar conversar com um vendedor do Azure, ligue para o número de telefone mostrado na parte superior da página de Perguntas frequentes.

### <a name="view-and-download-azure-price-sheet"></a>Exibir e baixar a tabela de preços do Azure

Caso tenha acesso ao Azure por meio de um EA (Contrato Enterprise) ou um MCA (Contrato de Cliente da Microsoft), você poderá exibir e baixar a tabela de preços de sua conta do Azure. A tabela de preços é um arquivo do Excel que contém os preços de todos os serviços do Azure. Para obter mais informações, confira [Exibir e baixar os preços do Azure](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Examinar os custos estimados no portal do Azure

Exiba o custo estimado por mês durante a adição de um serviço no portal do Azure. Por exemplo, quando você escolhe o tamanho de sua VM do Windows, vê o custo mensal estimado para as horas de computação:

![Exemplo: uma VM Windows A1 mostrando o custo estimado por mês](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorar custos ao usar os serviços do Azure
É possível monitorar custos com as seguintes ferramentas:

- Orçamento e alertas de custo
- Análise de custo

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Acompanhar os custos com orçamentos e alertas de custo

Crie [orçamentos](../costs/tutorial-acm-create-budgets.md) para gerenciar custos e crie [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) que notificam você e os stakeholders automaticamente de anomalias de gastos e gastos em excesso.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Explorar e analisar os custos com a análise de custos

Depois que os serviços do Azure estiverem em execução, verifique os custos regularmente para acompanhar seus gastos do Azure. Use a análise de custo para entender a procedência dos custos para o uso do Azure.

1. Visite a [página Gerenciamento de Custos + Cobrança no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Clique em **Análise de custo** no lado esquerdo da tela para ver o custo atual dividido por vários pivôs, como serviço, localização e assinatura. Depois de adicionar um serviço ou fazer uma compra, aguarde 24 horas para que os dados sejam exibidos. Por padrão, a análise de custo mostra o custo do escopo em que você está. Por exemplo, na captura de tela abaixo, o custo da conta de cobrança da Contoso é exibido. Use o item Escopo para alterar para outro escopo na análise de custo. Para obter mais informações sobre escopos, confira [Entender e trabalhar com escopos](../costs/understand-work-scopes.md#scopes)

    ![Captura de tela do modo de exibição de análise de custo no portal do Azure](./media/getting-started/cost-analysis.png)

4. Filtre por várias propriedades, como marcas, tipo de recurso e período de tempo. Clique em **Adicionar filtro** para adicionar o filtro de uma propriedade e selecione os valores a serem filtrados. Selecione **Exportar** para exportar a exibição para um arquivo de valores separados por vírgula (.csv).

5. Além disso, clique nos rótulos do gráfico para ver o histórico de gastos diário para esse rótulo. Por exemplo: Na captura de tela abaixo, um clique em máquinas virtuais exibe o custo diário da execução das VMs.

    ![Captura de tela da exibição do histórico de gastos no portal do Azure](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Otimizar e reduzir custos
Se você não estiver familiarizado com os princípios de gerenciamento de custos, leia [Como otimizar seu investimento em nuvem com o Gerenciamento de Custos do Azure](../costs/cost-mgt-best-practices.md).

No portal do Azure, também é possível otimizar e reduzir custos do Azure com desligamento automático para VMs e recomendações do Assistente.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere recursos de redução de custos, como o desligamento automático para VMs

Dependendo do cenário, você pode configurar o desligamento automático para suas VMs no portal do Azure. Para saber mais, confira [Desligamento automático de máquinas virtuais usando o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de tela da opção de desligamento automático no portal](./media/getting-started/auto-shutdown.png)

O desligamento automático não é igual a desligar a VM com as opções de energia padrão. O desligamento automático interrompe e desaloca suas VMs para parar as cobranças adicionais. Para saber mais, confira os estados de VM nas Perguntas frequentes sobre preços de [VMs Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Para obter mais recursos de redução de custos para os ambientes de desenvolvimento e teste, confira [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Ativar e examinar recomendações do Assistente do Azure

[O Assistente do Azure](../../advisor/advisor-overview.md) ajuda a reduzir os custos identificando recursos com baixa utilização. Pesquise **Assistente** no portal do Azure:

![Captura de tela do botão Azure Advisor no portal do Azure](./media/getting-started/advisor-button.png)

Selecione **Custo** no lado esquerdo. Você verá recomendações práticas na guia **Custo**:

![Captura de tela de exemplo de recomendação de custo do Advisor](./media/getting-started/advisor-action.png)

Examine o tutorial [Otimizar custos de recomendações](../costs/tutorial-acm-opt-recommendations.md) para obter um tutorial guiado sobre recomendações do Assistente para economia de custos.


## <a name="integrate-with-billing-and-consumption-apis"></a>Integração às APIs de cobrança e consumo

Use as APIs de [cobrança](https://docs.microsoft.com/rest/api/billing/) e [consumo](https://docs.microsoft.com/rest/api/consumption/) do Azure para obter dados de cobrança e custo de forma programática. Use a API RateCard junto com a API de Uso para obter seu uso cobrado. Para saber mais, confira [Obtenha informações sobre o consumo de recursos do Microsoft Azure](usage-rate-card-overview.md).

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Casos especiais e recursos adicionais

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes do EA, CSP e Sponsorship
Fale com seu gerente de conta ou parceiro do Azure para começar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| EA (Enterprise Agreement) | [Portal EA](https://ea.azure.com/), [documentos de ajuda](https://ea.azure.com/helpdocs) e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Programa do CSP (Provedor de Soluções na Nuvem) | Fale com seu provedor |
| Azure Sponsorship | [Portal do Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se estiver gerenciando TI em uma grande organização, recomendamos a leitura de [Azure enterprise scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) e [enterprise IT white paper](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (baixe o .pdf, somente em inglês).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Visualizações de custo do Contrato Enterprise no portal do Azure

As visualizações de custo Enterprise estão, no momento, em Visualização Pública. Itens a serem observados:

- Os custos de assinatura são baseados no uso e não incluem valores pré-pagos, excedentes, quantidades incluídas, ajustes e impostos. Os encargos reais são calculados no nível de registro.
- Os valores mostrados no portal do Azure podem ser diferentes daqueles no Enterprise Portal. Atualizações no Enterprise Portal podem levar alguns minutos antes de serem mostradas no portal do Azure.
- Se você não está vendo os custos, isso pode ser por um dos seguintes motivos:
    - Você não tem permissões no nível da assinatura. Para ver as exibições de custo empresarial, você precisa ser um Leitor de Cobrança, Leitor, Colaborador ou Proprietário no nível da assinatura.
    - Você é um Proprietário de Conta e seu Administrador de registro desabilitou a configuração “Encargos de exibição do AO”.  Contate o Administrador de registro para obter acesso aos custos.
    - Você é um administrador de departamento e seu administrador de registro desabilitou a configuração **Encargos de exibição do DA**.  Entre em contato com o Administrador de registro para obter acesso.
    - Você comprou o Azure por meio de um parceiro de canal e o parceiro não liberou informações sobre preços.  
- Se você atualizar as configurações relacionadas ao custo acessando o Enterprise Portal, haverá um atraso de alguns minutos antes que as alterações sejam mostradas no portal do Azure.
- Limite de gastos e diretrizes de fatura não se aplicam às assinaturas do Contrato Enterprise.

### <a name="check-your-subscription-and-access"></a>Verificar assinatura e acesso

Para exibir os custos, você precisa ter acesso no nível de conta ou de assinatura às informações de custo ou de cobrança. O acesso varia de acordo com o tipo de conta de cobrança. Para saber mais sobre contas de cobrança e verificar o tipo de sua conta de cobrança, confira [Exibir contas de cobrança no portal do Azure](view-all-accounts.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do MOSP (Programa do Microsoft Online Services), confira [Gerenciar o acesso às informações de cobrança para o Azure](manage-billing-access.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do EA (Contrato Enterprise), confira [Entender as funções administrativas do Contrato Enterprise do Azure no Azure](understand-ea-roles.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do MCA (Contrato de Cliente da Microsoft), confira [Entender as funções administrativas do Contrato de Cliente da Microsoft no Azure](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Solicitar um crédito do Contrato de Nível de Serviço para um incidente de serviço

O SLA (Contrato de Nível de Serviço) descreve os compromissos da Microsoft com relação ao tempo de atividade e à conectividade. Um incidente de serviço é relatado quando os serviços do Azure enfrentam um problema que afeta o tempo de atividade ou a conectividade, geralmente denominado *interrupção*. Se não alcançarmos e mantivermos os Níveis de Serviço para cada serviço, conforme descrito no SLA, você poderá ser qualificado para um crédito para uma parte de seus valores de serviço mensais.

Para solicitar um crédito:

1. Entre no [portal do Azure](https://portal.azure.com/). Se você tiver várias contas, use a que foi afetada pelo tempo de inatividade do Azure.
2. Crie uma solicitação de suporte.
3. Em **Tipo de problema**, selecione **Cobrança**.
4. Em **Tipo de problema**, selecione **Solicitação de Reembolso**.
5. Adicione detalhes para especificar que você está solicitando um crédito SLA, mencione a data/hora/fuso horário, além dos serviços afetados (VMs, sites etc.)
6. Verifique seus detalhes de contato e selecione **Criar** para enviar sua solicitação.

Os limites de SLA variam por serviço. Por exemplo, a Camada da Web do SQL tem um SLA de 99,9%, as VMs têm um SLA de 99,95% e a Camada Padrão do SQL tem um SLA de 99,99%.

Para alguns serviços, há pré-requisitos para o SLA aplicar. Por exemplo, as máquinas virtuais devem ter duas ou mais instâncias implantadas no mesmo conjunto de disponibilidade.

Para obter mais informações, confira [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) e a documentação [Resumo do SLA para serviços do Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="analyze-unexpected-charges"></a>Analisar encargos inesperados

A infraestrutura de recursos de nuvem que você criou para sua organização é provavelmente complexa. Muitos tipos de recursos do Azure podem ter diferentes tipos de encargos. Os recursos do Azure podem ser da propriedade de diferentes equipes em sua organização e ter tipos diferentes de modelo de cobrança que se aplicam a vários recursos. Para obter uma melhor compreensão dos encargos, comece sua análise usando uma ou mais estratégias nas seções a seguir.

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>Examine sua fatura e identifique o recurso responsável pelo encargo

A maneira como você compra os serviços do Azure ajuda a determinar a metodologia e as ferramentas disponíveis ao identificar o recurso associado a um encargo. Para determinar qual metodologia se aplica a você, primeiro [determine o tipo de oferta do Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Em seguida, identifique sua categoria de cliente na lista de [ofertas do Azure com suporte](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Os artigos a seguir fornecem etapas detalhadas que explicam como você deve examinar sua fatura com base no seu tipo de cliente. Em cada artigo, há instruções sobre como baixar um arquivo CSV que contém detalhes de uso e de custo para um determinado período de cobrança.

- [Processo de análise de fatura de pagamento conforme o uso](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Processo de análise de fatura do Contrato Enterprise](../understand/review-enterprise-agreement-bill.md)
- [Processo de análise do Contrato de Cliente da Microsoft](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Processo de análise do Contrato de Parceiro da Microsoft](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Sua fatura do Azure agrega encargos do mês por _medidor_. Medidores são usados para acompanhar o uso de um recurso ao longo do tempo e para calcular sua fatura. Quando você cria um único recurso do Azure, como uma máquina virtual, uma ou mais instâncias de medidor são criadas para o recurso.

Filtre o arquivo CSV de uso com base no _MeterName_, conforme mostrado na fatura que você deseja analisar, para ver todos os itens de linha aplicáveis ao medidor. A _InstanceID_ do item de linha corresponde ao recurso real do Azure que gerou o encargo.

Quando você tiver identificado o recurso em questão, poderá usar a Análise de custo no Gerenciamento de Custos do Azure para analisar ainda mais os custos relacionados ao recurso. Para saber mais sobre como usar a análise de custo, confira [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md).

### <a name="review-invoiced-charges-in-cost-analysis"></a>Examinar encargos faturados na Análise de custo

Para ver os detalhes de sua fatura no portal do Azure, navegue até a Análise de custo do escopo associado à fatura que você está analisado. Selecione a exibição **Detalhes da fatura**. Os detalhes da fatura mostram os encargos como foi visto na fatura.

[![Exemplo mostrando a seleção de detalhes da fatura](./media/getting-started/invoice-details.png)](./media/getting-started/invoice-details.png#lightbox)

Vendo os detalhes da fatura, é possível identificar o serviço que tem custos inesperados e determinar quais recursos estão diretamente associados ao recurso na Análise de custo. Por exemplo, se desejar analisar encargos do serviço de Máquinas Virtuais, navegue até a exibição **Custo acumulado**. Em seguida, defina a granularidade como **Diária** e filtre os encargos **Nome do serviço: máquinas virtuais** e agrupe-os por **Recurso**.

[![Exemplo mostrando os custos acumulados para máquinas virtuais](./media/getting-started/virtual-machines.png)](./media/getting-started/virtual-machines.png#lightbox)


### <a name="identify-spikes-in-cost-over-time"></a>Identificar picos de custo ao longo do tempo

Às vezes, talvez você não saiba quais custos recentes resultaram em alterações nos encargos cobrados. Para entender o que mudou, você pode usar a Análise de custo para [ver um detalhamento diário ou mensal dos custos ao longo do tempo](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Depois de criar a exibição, agrupe seus encargos por **Serviço** ou **Recurso** de para identificar as alterações. Você também pode alterar sua exibição para um gráfico de **Linha** para visualizar melhor os dados.

![Exemplo mostrando os custos ao longo do tempo na análise de custo](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>Determinar o preço do recurso e compreender o modelo de cobrança dele

Um único recurso pode acumular encargos entre vários produtos e serviços do Azure. Veja a página [Preço do Azure por produto](https://azure.microsoft.com/pricing/#product-pricing) para saber mais sobre o preço de cada serviço do Azure. Por exemplo, uma única VM (máquina virtual) criada no Azure pode ter os medidores a seguir criados para rastrear o uso. Cada um pode ter um preço diferente.

- Calcular horas
- Horas de Endereço IP
- Transferência de Dados de Entrada
- Transferência de Dados de Saída
- Disco Gerenciado Standard
- Operações do Disco Gerenciado Standard
- Disco de E/S Padrão
- E/S Padrão – Leitura de Blob de Blocos
- E/S Padrão – Gravação de Blob de Blocos
- Bloqueio de blobs padrão IO-Block

Quando a VM é criada, cada medidor começa a emitir registros de uso. O uso e o preço do medidor são rastreados no sistema de medição do Azure. Você pode ver os medidores que foram usados para calcular sua fatura no arquivo CSV de uso.

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>Encontrar as pessoas responsáveis pelo recurso e envolvê-las

Geralmente, a equipe responsável por um determinado recurso conhecerá as alterações que foram feitas em um recurso. O envolvimento dessa equipe é útil porque você identifica por que os encargos podem aparecer. Por exemplo, a equipe proprietária pode ter criado recentemente o recurso, atualizado o SKU dele (alterando a taxa do recurso) ou aumentado a carga no recurso devido às alterações de código. Continue lendo as seções a seguir para obter mais técnicas para determinar quem tem a propriedade de um recurso.

#### <a name="analyze-the-audit-logs-for-the-resource"></a>Analisar os logs de auditoria do recurso

Se você tiver permissões para exibir um recurso, deverá ser capaz de acessar os logs de auditoria dele. Examine os logs para encontrar o usuário responsável pelas alterações mais recentes em um recurso. Para saber mais, confira [Exibir e recuperar eventos do Log de atividades do Azure](../../azure-monitor/platform/activity-log-view.md).

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analisar as permissões do usuário para o escopo pai do recurso

As pessoas que têm acesso de gravação a uma assinatura ou grupo de recursos normalmente têm informações sobre os recursos que foram criados. Elas devem ter a capacidade de explicar a finalidade de um recurso ou indicar você à pessoa que saiba. Para identificar as pessoas com as permissões para um Escopo de assinatura, confira [Exibir atribuições de função](../../role-based-access-control/check-access.md#view-role-assignments). Você pode usar um processo semelhante para grupos de recursos.

### <a name="get-help-to-identify-charges"></a>Obter ajuda para identificar encargos

Se você tiver usado as estratégias anteriores e ainda não entendeu por que você recebeu um encargo ou se precisar de outra ajuda com problemas de cobrança, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como usar [limites de gastos](spending-limit.md) para impedir o gasto excessivo.
- Comece [analisando os custos do Azure](../costs/quick-acm-cost-analysis.md).
