---
title: Visão geral da Cobrança e do Gerenciamento de Custos do Azure | Microsoft Docs
description: Use os recursos de Cobrança e Gerenciamento de Custos do Azure para realizar tarefas administrativas de cobrança e gerenciar o acesso da cobrança aos custos. Além disso, use o recurso para monitorar e controlar os gastos do Azure e otimizar o uso de recursos.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.custom: ''
ms.openlocfilehash: 2f96208ff3f9664d82bfc1d9ddf9bc5b9aec37c3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879081"
---
# <a name="what-is-azure-cost-management-and-billing"></a>O que são a Cobrança e o Gerenciamento de Custos do Azure?

Com os produtos e serviços do Azure, você paga apenas pelo que usar. Ao criar e usar recursos do Azure, você será cobrado por eles. Use os recursos de Cobrança e Gerenciamento de Custos do Azure para realizar tarefas administrativas de cobrança e gerenciar o acesso da cobrança aos custos. Use também os recursos para monitorar e controlar os gastos do Azure e otimizar o uso de recursos do Azure.

## <a name="understand-azure-billing"></a>Entender a Cobrança do Azure

Os recursos da Cobrança do Azure são usados para examinar os custos faturados e gerenciar o acesso às informações de cobrança. Em organizações maiores, as equipes de suprimento e finanças geralmente realizam as tarefas de faturamento.

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você usa a conta de cobrança para gerenciar faturas, pagamentos e acompanhar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter se inscrito no Azure para seus projetos pessoais. Portanto, você pode ter uma assinatura individual do Azure com uma conta de cobrança. Você também pode ter acesso por meio do Contrato Enterprise de sua organização ou do Contrato de Cliente da Microsoft. Para cada cenário, você terá uma conta de cobrança separada.

### <a name="billing-accounts"></a>Contas de cobrança

Atualmente, o portal do Azure dá suporte aos seguintes tipos de contas de cobrança:

- **Programa Microsoft Online Services**: Uma conta de cobrança individual para um Programa dos Microsoft Online Services é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve em uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: Uma conta de cobrança para um Contrato Enterprise é criada quando sua organização assina um [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/) para usar o Azure.

- **Contrato de Cliente da Microsoft**: Uma conta de cobrança para um Contrato de Cliente da Microsoft é criada quando sua organização decide assinar um Contrato de Cliente da Microsoft por meio de um representante da Microsoft. Alguns clientes em regiões selecionadas, que se inscrevem por meio do site do Azure em uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou atualizam sua [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), também podem ter uma conta de cobrança para um Contrato de Cliente da Microsoft. Para obter mais informações, confira [Introdução à conta de cobrança para o Contrato de Cliente da Microsoft](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Escopos para contas de cobrança
Um escopo é um nó em uma conta de cobrança que você usa para exibir e gerenciar a cobrança. É nele que você gerencia dados de cobrança, pagamentos, faturas e realiza o gerenciamento geral da conta.

#### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

|Escopo  |Definição  |
|---------|---------|
|Conta de cobrança     | Representa um único proprietário (administrador da conta) de uma ou mais assinaturas do Azure. Um administrador da conta está autorizado a executar várias tarefas de cobrança, como criar assinaturas, exibir faturas ou alterar a cobrança de assinaturas.  |
|Subscription     |  Representa um agrupamento de recursos do Azure. Uma fatura é gerada no escopo da assinatura. Ela tem suas próprias formas de pagamento que são usadas para pagar a fatura.|


#### <a name="enterprise-agreement"></a>Contrato Enterprise

|Escopo  |Definição  |
|---------|---------|
|Conta de cobrança    | Representa um registro do Contrato Enterprise. A fatura é gerada no escopo da conta de cobrança. Ela é estruturada usando departamentos e contas de registro.  |
|department     |  Agrupamento opcional de contas de registro.      |
|Conta de registro     |  Representa um único proprietário da conta. As assinaturas do Azure são criadas no escopo da conta de registro.  |


#### <a name="microsoft-customer-agreement"></a>Contrato de Cliente da Microsoft

|Escopo  |Tarefas  |
|---------|---------|
|Conta de cobrança     |   Representa um contrato de cliente para vários produtos e serviços da Microsoft. A conta de cobrança é estruturada usando perfis de cobrança e seções da fatura.   |
|Perfil de faturamento     |  Representa uma fatura e suas formas de pagamento. A fatura é gerada nesse escopo. O perfil de cobrança pode ter várias seções de fatura.      |
|Seção da fatura     |   Representa um grupo de custos em uma fatura. As assinaturas e outras compras estão associadas ao escopo da seção da fatura.    |


## <a name="understand-azure-cost-management"></a>Entender o Gerenciamento de Custos do Azure
O gerenciamento de custos é o processo de efetivamente planejar e controlar os custos envolvidos em seus negócios. As tarefas de gerenciamento de custos são normalmente executadas por equipes de finanças, gerenciamento e aplicativos. A Cobrança e o Gerenciamento de Custos do Azure ajudam as organizações a planejar priorizando o custo. Também ajuda a analisar os custos de maneira eficaz e a tomar medidas para otimizar o gasto na nuvem. Para saber mais sobre como abordar o gerenciamento de custos como uma organização, consulte o artigo [práticas recomendadas do Azure Cost Management](./costs/cost-mgt-best-practices.md).

Assista ao [vídeo de visão geral do Gerenciamento de Custos do Azure](https://www.youtube.com/watch?v=el4yN5cHsJ0) para obter uma visão geral sobre como o Gerenciamento de Custos do Azure pode ajudar você a economizar dinheiro no Azure. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Embora relacionado, o faturamento difere do gerenciamento de custos. O faturamento é o processo de faturar os clientes por bens ou serviços e gerenciar o relacionamento comercial.

O gerenciamento de custos mostra os padrões de custo e uso da organização com análises avançadas. Os relatórios no Gerenciamento de Custos mostram os custos baseados em uso consumidos pelos serviços do Azure e de ofertas do Marketplace de terceiros. Os custos são baseados nos fatores e preços negociados na reserva e nos descontos do Benefício Híbrido do Azure. Coletivamente, os relatórios mostram seus custos internos e externos de uso e os encargos do Microsoft Azure Marketplace. Outros encargos, como compras de reserva, suporte e impostos ainda não são mostrados nos relatórios. Os relatórios ajudam você a entender seus gastos e uso de recursos e podem ajudar a encontrar anomalias de gastos. A análise preditiva também está disponível. O Gerenciamento de Custos usa grupos de gerenciamento, orçamentos e recomendações do Azure para mostrar claramente como suas despesas são organizadas e como você pode reduzir custos.

Você pode usar o portal do Azure ou várias APIs para automação de exportação para integrar dados de custo com sistemas e processos externos. A exportação automática de dados de faturamento e relatórios agendados também estão disponíveis.

### <a name="plan-and-control-expenses"></a>Plano e controle de despesas

As maneiras como o Gerenciamento de Custos o ajuda a planejar e controlar os custos incluem: Análise de custos, orçamentos, recomendações e exportação de dados de gerenciamento de custos.

Você usa a análise de custos para explorar e analisar seus custos organizacionais. Você pode visualizar os custos agregados por organização para entender onde os custos são acumulados e para identificar tendências de gastos. E você pode ver os custos acumulados ao longo do tempo para estimar tendências de custo mensais, trimestrais ou mesmo anuais em comparação a um orçamento.

Os orçamentos ajudam você a planejar e cumprir a responsabilidade financeira em sua organização. Eles ajudam a evitar que limites ou limites de custo sejam superados. Os orçamentos também podem ajudar você a informar os outros sobre seus gastos para gerenciar os custos de forma proativa. E com eles, você pode ver como os gastos progridem com o tempo.

As recomendações mostram como você pode otimizar e melhorar a eficiência identificando recursos ociosos e subutilizados. Ou, eles podem mostrar opções de recursos menos dispendiosas. Quando você age de acordo com as recomendações, você muda a maneira como usa seus recursos para economizar dinheiro. Para agir, você primeiro visualiza as recomendações de otimização de custos para visualizar possíveis ineficiências de uso. Em seguida, você age em uma recomendação para modificar o uso de recursos do Azure para uma opção mais econômica. Em seguida, você verifica a ação para garantir que a alteração realizada seja bem-sucedida.

Se você usar sistemas externos para acessar ou revisar dados de gerenciamento de custos, poderá exportar facilmente os dados do Azure. E você pode definir uma exportação agendada diariamente no formato CSV e armazenar os arquivos de dados no armazenamento do Azure. Então, você pode acessar os dados do seu sistema externo.

### <a name="cloudyn-deprecation"></a>Preterição do Cloudyn

O [Cloudyn](./cloudyn/overview.md) é um serviço do Azure relacionado ao Gerenciamento de Custos que será preterido até o final de 2020. Os recursos do Cloudyn existentes estão sendo integrados diretamente no portal do Azure, sempre que possível. Nenhum novo cliente está sendo integrado no momento, mas o suporte permanecerá para o produto até que ele seja totalmente preterido.
 
Assista ao [vídeo do Gerenciamento de Custos do Azure e do Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM) para saber mais sobre quando você deve usar o Gerenciamento de Custos do Azure ou o Cloudyn com base em suas necessidades de negócios. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

### <a name="additional-azure-tools"></a>Ferramentas do Azure adicionais

O Azure tem outras ferramentas que não fazem parte do conjunto de recursos de Cobrança e Gerenciamento de Custos do Azure. No entanto, elas têm um papel importante no processo de gerenciamento de custos. Para saber mais sobre essas ferramentas, consulte os links a seguir.

- [Calculadora de preço do Azure](https://azure.microsoft.com/pricing/calculator/): use essa ferramenta para estimar seus custos iniciais de nuvem.
- [Azure Migrate](../migrate/migrate-overview.md) - Avalie sua carga de trabalho atual do data center para obter insights sobre o que é necessário de uma solução de substituição do Azure.
- [Azure Advisor](../advisor/advisor-overview.md) - Identifique VMs não usadas e receba recomendações sobre compras de instâncias reservadas do Azure.
- [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) - Use suas licenças atuais do Windows Server ou do SQL Server no local para VMs no Azure para salvar.


## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com a Cobrança e o Gerenciamento de Custos, a próxima etapa é começar a usar o serviço.

- Comece a usar o Azure Cost Management para [analisar os custos](./costs/quick-acm-cost-analysis.md).
- Você também pode ler mais sobre [práticas recomendadas de gerenciamento de custos do Azure](./costs/cost-mgt-best-practices.md).
