---
title: Evitar custos inesperados e gerenciar a cobrança no Azure
description: Saiba como evitar encargos inesperados em sua conta do Azure. Use recursos de gerenciamento e controle de custos para a sua conta do Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 6f7a29adfbd145be11f9f6c91e8e66dd229fed62
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375614"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evite cobranças inesperadas com o gerenciamento de custo e a cobrança do Azure

Ao se inscrever no Azure, há várias coisas que podem ser feitas para você ter uma ideia melhor de seus gastos:

- Obtenha os custos estimados antes de adicionar serviços usando a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/), a tabela de preços do Azure ou durante a adição de serviços no portal do Azure.
- Monitore os custos com [orçamentos](../cost-management/tutorial-acm-create-budgets.md), [alertas](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) e [análise de custo](../cost-management/quick-acm-cost-analysis.md).
- Examine os encargos em sua fatura comparando-os com [arquivos de uso detalhado](billing-download-azure-invoice-daily-usage-date.md).
- Integre os dados de cobrança e custo ao seu próprio sistema de relatórios usando APIs de [cobrança](https://docs.microsoft.com/rest/api/billing/) e [consumo](https://docs.microsoft.com/rest/api/consumption/).
- Use recursos e ferramentas adicionais para clientes do EA (Contrato Enterprise), do CSP (Provedor de Soluções na Nuvem) e do Azure Sponsorship.
- Use [alguns dos serviços mais populares do Azure gratuitamente por 12 meses](billing-create-free-services-included-free-account.md) disponíveis com a [conta gratuita do Azure](https://azure.microsoft.com/free/). Juntamente com as recomendações listadas abaixo, consulte [Evitar ser cobrado na conta gratuita](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obter custos estimados antes de adicionar serviços do Azure

Use uma das seguintes ferramentas para estimar o custo do uso de um serviço do Azure:
- Calculadora de preços do Azure
- Tabela de preços do Azure
- Portal do Azure

As imagens nas seções a seguir mostram os preços de exemplo em dólares americanos.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar custo online usando a calculadora de preços

Confira a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para obter uma estimativa do custo mensal do serviço que deseja adicionar. Altere a moeda para obter a estimativa em sua moeda local.

![Captura de tela do menu de calculadora de preços](./media/billing-getting-started/pricing-calc.png)

Exiba o custo estimado de qualquer serviço interno do Azure. Por exemplo, na captura de tela abaixo, o custo de uma VM (Máquina Virtual) do Windows A1 é estimado como US$ 66,96 por mês em horas de computação se você deixá-la em execução o tempo todo:

![Captura de tela da calculadora de preços mostrando um custo estimado de VM Windows A1 por mês](./media/billing-getting-started/pricing-calcvm.png)

Para obter mais informações sobre preços, confira as [Perguntas frequentes sobre Preços](https://azure.microsoft.com/pricing/faq/). Se desejar conversar com um vendedor do Azure, ligue para o número de telefone mostrado na parte superior da página de Perguntas frequentes.

### <a name="view-and-download-azure-price-sheet"></a>Exibir e baixar a tabela de preços do Azure

Caso tenha acesso ao Azure por meio de um EA (Contrato Enterprise) ou um MCA (Contrato de Cliente da Microsoft), você poderá exibir e baixar a tabela de preços de sua conta do Azure. A tabela de preços é um arquivo do Excel que contém os preços de todos os serviços do Azure. Para obter mais informações, confira [Exibir e baixar os preços do Azure](billing-ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Examinar os custos estimados no portal do Azure

Exiba o custo estimado por mês durante a adição de um serviço no portal do Azure. Por exemplo, quando você escolhe o tamanho de sua VM do Windows, vê o custo mensal estimado para as horas de computação:

![Exemplo: uma VM Windows A1 mostrando o custo estimado por mês](./media/billing-getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorar custos ao usar os serviços do Azure
É possível monitorar custos com as seguintes ferramentas:

- Orçamento e alertas de custo
- Análise de custo

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Acompanhar os custos com orçamentos e alertas de custo

Crie [orçamentos](../cost-management/tutorial-acm-create-budgets.md) para gerenciar custos e crie [alertas](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) que notificam você e os stakeholders automaticamente de anomalias de gastos e gastos em excesso. 

### <a name="costs"></a> Explorar e analisar os custos com a análise de custos

Depois que os serviços do Azure estiverem em execução, verifique os custos regularmente para acompanhar seus gastos do Azure. Use a análise de custo para entender a procedência dos custos para o uso do Azure. 

1. Visite a [página Gerenciamento de Custos + Cobrança no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Clique em **Análise de custo** no lado esquerdo da tela para ver o custo atual dividido por vários pivôs, como serviço, localização e assinatura. Depois de adicionar um serviço ou fazer uma compra, aguarde 24 horas para que os dados sejam exibidos. Por padrão, a análise de custo mostra o custo do escopo em que você está. Por exemplo, na captura de tela abaixo, o custo da conta de cobrança da Contoso é exibido. Use o item Escopo para alterar para outro escopo na análise de custo. Para obter mais informações sobre escopos, confira [Entender e trabalhar com escopos](../cost-management/understand-work-scopes.md#scopes)

    ![Captura de tela do modo de exibição de análise de custo no portal do Azure](./media/billing-getting-started/cost-analysis.png)

4. Filtre por várias propriedades, como marcas, tipo de recurso e período de tempo. Clique em **Adicionar filtro** para adicionar o filtro de uma propriedade e selecione os valores a serem filtrados. Selecione **Exportar** para exportar a exibição para um arquivo de valores separados por vírgula (.csv).

5. Além disso, clique nos rótulos do gráfico para ver o histórico de gastos diário para esse rótulo. Por exemplo: Na captura de tela abaixo, um clique em máquinas virtuais exibe o custo diário da execução das VMs.

    ![Captura de tela da exibição do histórico de gastos no portal do Azure](./media/billing-getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Otimizar e reduzir custos
Se você não estiver familiarizado com os princípios de gerenciamento de custos, leia [Como otimizar seu investimento em nuvem com o Gerenciamento de Custos do Azure](../cost-management/cost-mgt-best-practices.md).

No portal do Azure, também é possível otimizar e reduzir custos do Azure com desligamento automático para VMs e recomendações do Assistente.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere recursos de redução de custos, como o desligamento automático para VMs

Dependendo do cenário, você pode configurar o desligamento automático para suas VMs no portal do Azure. Para saber mais, confira [Desligamento automático de máquinas virtuais usando o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de tela da opção de desligamento automático no portal](./media/billing-getting-started/auto-shutdown.png)

O desligamento automático não é igual a desligar a VM com as opções de energia padrão. O desligamento automático interrompe e desaloca suas VMs para parar as cobranças adicionais. Para saber mais, confira os estados de VM nas Perguntas frequentes sobre preços de [VMs Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Para obter mais recursos de redução de custos para os ambientes de desenvolvimento e teste, confira [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Ativar e examinar recomendações do Assistente do Azure

[O Assistente do Azure](../advisor/advisor-overview.md) ajuda a reduzir os custos identificando recursos com baixa utilização. Pesquise **Assistente** no portal do Azure:

![Captura de tela do botão Azure Advisor no portal do Azure](./media/billing-getting-started/advisor-button.png)

Selecione **Custo** no lado esquerdo. Você verá recomendações práticas na guia **Custo**:

![Captura de tela de exemplo de recomendação de custo do Advisor](./media/billing-getting-started/advisor-action.png)

Examine o tutorial [Otimizar custos de recomendações](../cost-management/tutorial-acm-opt-recommendations.md) para obter um tutorial guiado sobre recomendações do Assistente para economia de custos.

## <a name="review-charges-against-your-latest-invoice"></a>Examinar os encargos em sua última fatura

No fim do período de cobrança, sua fatura estará disponível. [Baixe faturas e arquivos de uso detalhado](billing-download-azure-invoice-daily-usage-date.md) para compará-los e verificar se você foi cobrado corretamente. Para obter mais informações sobre como comparar seu uso diário com sua fatura, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

Caso esteja usando o Azure por meio de um MCA (Contrato de Cliente da Microsoft), você também poderá [comparar sua fatura com as transações](billing-mca-understand-your-bill.md#view-transactions-for-an-invoice-in-the-azure-portal) para entender os encargos em sua fatura.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integração às APIs de cobrança e consumo

Use as APIs de [cobrança](https://docs.microsoft.com/rest/api/billing/) e [consumo](https://docs.microsoft.com/rest/api/consumption/) do Azure para obter dados de cobrança e custo de forma programática. Use a API RateCard junto com a API de Uso para obter seu uso cobrado. Para saber mais, confira [Obtenha informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Casos especiais e recursos adicionais

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes do EA, CSP e Sponsorship
Fale com seu gerente de conta ou parceiro do Azure para começar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| EA (Enterprise Agreement) | [Portal EA](https://ea.azure.com/), [documentos de ajuda](https://ea.azure.com/helpdocs) e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Programa do CSP (Provedor de Soluções na Nuvem) | Fale com seu provedor |
| Azure Sponsorship | [Portal do Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se estiver gerenciando TI em uma grande organização, recomendamos a leitura de [Azure enterprise scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) e [enterprise IT white paper](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (baixe o .pdf, somente em inglês).

### <a name="EA"></a> Visualizações de custo do Contrato Enterprise no portal do Azure

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

Para exibir os custos, você precisa ter acesso no nível de conta ou de assinatura às informações de custo ou de cobrança. O acesso varia de acordo com o tipo de conta de cobrança. Para saber mais sobre contas de cobrança e verificar o tipo de sua conta de cobrança, confira [Exibir contas de cobrança no portal do Azure](billing-view-all-accounts.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do MOSP (Programa do Microsoft Online Services), confira [Gerenciar o acesso às informações de cobrança para o Azure](billing-manage-access.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do EA (Contrato Enterprise), confira [Entender as funções administrativas do Contrato Enterprise do Azure no Azure](billing-understand-ea-roles.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do MCA (Contrato de Cliente da Microsoft), confira [Entender as funções administrativas do Contrato de Cliente da Microsoft no Azure](billing-understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Solicitar um crédito do Contrato de Nível de Serviço para um incidente de serviço

O SLA (contrato de nível de serviço) descreve os compromissos da Microsoft com relação ao tempo de atividade e à conectividade. Um incidente de serviço é relatado quando os serviços do Azure enfrentam um problema que afeta o tempo de atividade ou a conectividade, geralmente denominado *interrupção*. Se não alcançarmos e mantivermos os Níveis de Serviço para cada serviço, conforme descrito no SLA, você poderá ser qualificado para um crédito para uma parte de seus valores de serviço mensais.

Para solicitar um crédito:

1. Entre no [Portal do Azure](https://portal.azure.com/). Se você tiver várias contas, use a que foi afetada pelo tempo de inatividade do Azure. 
2. Crie uma solicitação de suporte.
3. Em **Tipo de problema**, selecione **Cobrança**.
4. Em **Tipo de problema**, selecione **Solicitação de Reembolso**.
5. Adicione detalhes para especificar que você está solicitando um crédito SLA, mencione a data/hora/fuso horário, além dos serviços afetados (VMs, sites, etc.)
6. Verifique seus detalhes de contato e selecione **Criar** para enviar sua solicitação.

Os limites de SLA variam por serviço. Por exemplo, a Camada da Web do SQL tem um SLA de 99,9%, as VMs têm um SLA de 99,95% e a Camada Padrão do SQL tem um SLA de 99,99%.

Para alguns serviços, há pré-requisitos para o SLA aplicar. Por exemplo, as máquinas virtuais devem ter duas ou mais instâncias implantadas no mesmo conjunto de disponibilidade.

Para obter mais informações, confira [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) e a documentação [Resumo do SLA para serviços do Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como usar [limites de gastos](billing-spending-limit.md) para impedir o gasto excessivo.
- Comece [analisando os custos do Azure](../cost-management/quick-acm-cost-analysis.md).
