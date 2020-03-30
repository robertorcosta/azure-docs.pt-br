---
title: Capacidade reservada no Azure Cosmos DB para otimizar custos
description: Aprenda a comprar a capacidade reservada do Azure Cosmos DB para economizar nos custos de computação.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505958"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Otimizar o custo com a capacidade reservada no Azure Cosmos DB

A capacidade reservada do Azure Cosmos DB ajuda você a economizar dinheiro ao se comprometer com uma reserva do Azure Cosmos DB por um período de um ou três anos. Com a capacidade reservada do Azure Cosmos DB, você pode obter um desconto na taxa de transferência provisionada para recursos do Cosmos DB. Exemplos de recursos são bancos de dados e contêineres (tabelas, coleções e gráficos).

A capacidade reservada do Azure Cosmos DB pode reduzir significativamente os custos do Cosmos DB&mdash;até 65% nos preços regulares, com um compromisso antecipado de um a três anos. A capacidade reservada proporciona um desconto de cobrança e não afeta o estado do runtime dos seus recursos do Azure Cosmos DB.

Capacidade reservada do Azure Cosmos DB cobre a taxa de transferência provisionada para seus recursos. Ela não cobre encargos de armazenamento e rede. Assim que você compra uma reserva, as cobranças de taxa de transferência que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento conforme o uso. Para obter mais informações sobre reservas, confira o artigo [Reservas do Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md).

Você pode comprar a capacidade reservada do Azure Cosmos DB no [portal do Azure](https://portal.azure.com). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/monthly-payments-reservations.md). Para comprar uma capacidade reservada:

* Você deve estar na função Proprietário para pelo menos uma assinatura Enterprise ou individual com taxas de pagamento.  
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa CSP (Provedor de Solução de Nuvem), apenas os agentes admin ou agentes de vendas podem comprar a capacidade reservada do Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinar a taxa de transferência necessária antes da compra

O tamanho da compra de capacidade reservada deve ser baseado na quantidade total de throughput que os recursos azure Cosmos DB existentes ou em breve serão usados de hora em hora. Por exemplo: Compre 30.000 RU/s de capacidade reservada se esse for o seu padrão de uso por hora consistente. Neste exemplo, qualquer throughput provisionado acima de 30.000 RU/s será cobrado usando sua taxa de Pay-as-you-go. Se o throughput provisionado for inferior a 30.000 RU/s em uma hora, então a capacidade extra reservada para essa hora será desperdiçada.

Calculamos recomendações de compra com base no seu padrão de uso por hora. O uso nos últimos 7, 30 e 60 dias é analisado, e recomenda-se a compra de capacidade reservada que maximize suas economias. Você pode ver os tamanhos de reserva recomendados no portal Azure usando as seguintes etapas:

1. Faça login no [portal Azure](https://portal.azure.com).  

2. Selecione **Todos os serviços** > **que as reservas** > **adicionam**.

3. No painel **De compras de reservas,** escolha **Azure Cosmos DB**.

4. Selecione a guia **Recomendada** para exibir as reservas recomendadas:

Você pode filtrar recomendações pelos seguintes atributos:

- **Prazo** (1 ano ou 3 anos)
- **Frequência de faturamento** (Mensal ou Inicial)
- **Tipo de throughput** (RU's vs Multi-master RU's)

Além disso, você pode escopo de recomendações para estar dentro de um único grupo de recursos, assinatura única ou toda a sua inscrição no Azure. 

Aqui está uma recomendação de exemplo:

![Recomendações de Capacidade Reservada](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

Esta recomendação para comprar uma reserva de 30.000 RU/s indica que, entre as reservas de 3 anos, um tamanho de reserva de 30.000 RU/s maximizará a economia. Neste caso, a recomendação é calculada com base nos últimos 30 dias de uso do Azure Cosmos DB. Se este cliente espera que os últimos 30 dias de uso do Azure Cosmos DB sejam representativos do uso futuro, eles maximizarão a economia comprando uma reserva de 30.000 RU/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Comprar capacidade reservada do Azure Cosmos DB

1. Faça login no [portal Azure](https://portal.azure.com).  

2. Selecione **Todos os serviços** > **que as reservas** > **adicionam**.  

3. No painel **de reservas de compras,** escolha **a Azure Cosmos DB** para comprar uma nova reserva.  

4. Preencha os campos obrigatórios conforme descrito na tabela a seguir:

   ![Preencher o formulário de capacidade reservada](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Campo  |Descrição  |
   |---------|---------|
   |Escopo   |   Opção que controla quantas assinaturas podem usar o benefício de cobrança associado com a reserva. Também controla como a reserva será aplicada a assinaturas específicas. <br/><br/>  Se você selecionar **Compartilhado**, o desconto de reserva será aplicado às instâncias do Azure Cosmos DB em execução em qualquer assinatura dentro do seu contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes que pagam, o escopo compartilhado é todas as assinaturas individuais com taxas de pagamento criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **Assinatura única**, o desconto de reserva será aplicado a instâncias do Azure Cosmos DB na assinatura selecionada. <br/><br/> Se você selecionar **Grupo de recursos individuais,** o desconto de reserva será aplicado às instâncias do Azure Cosmos DB na assinatura selecionada e no grupo de recursos selecionado dentro dessa assinatura. <br/><br/> Você pode alterar o escopo de reserva após comprar a capacidade reservada.  |
   |Subscription  |   Assinatura usada para pagar pela capacidade reservada do Azure Cosmos DB. O método de pagamento na assinatura selecionada é usado na cobrança dos custos. A assinatura deve ser um dos seguintes tipos: <br/><br/>  Contrato Empresarial (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma assinatura Enterprise, os encargos são deduzidos do saldo de compromisso monetário da inscrição ou cobrados como excesso de poder. <br/><br/> Assinatura individual com taxas de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): Para uma assinatura individual com taxas de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na assinatura.    |
   | Grupo de recursos | Grupo de recursos ao qual o desconto de capacidade reservada é aplicado. |
   |Termo  |   Um ano ou três anos.   |
   |Tipo de throughput   |  O throughput é provisionado como unidades de solicitação. Você pode comprar uma reserva para o throughput provisionado para ambas as configurações - grava ções em uma única região, bem como gravações de várias regiões. O tipo de throughput tem dois valores para escolher: 100 RU/s por hora e 100 RU/s multimestres por hora.|
   | Unidades de capacidade reservada| A quantidade de taxa de transferência que você deseja reservar. Você pode calcular esse valor determinando a taxa de transferência necessária para todos os seus recursos do Cosmos DB (por exemplo, bancos de dados ou contêineres) por região. Em seguida, multiplique-o pelo número de regiões que você associará com o seu banco de dados Cosmos. Por exemplo: se você tiver cinco regiões com 1 milhão de RU/s em todas as regiões, selecione 5 milhões de RU/s para compra de capacidade de reserva. |


5. Após preencher o formulário, calcula-se o preço necessário para adquirir a capacidade reservada. A saída também mostra a porcentagem de desconto que você recebe com as opções escolhidas. Clique em Next Click **Select**

6. No painel **de reservas de compras,** revise o desconto e o preço da reserva. Esse preço de reserva se aplica aos recursos do Azure Cosmos DB com a taxa de transferência provisionada entre todas as regiões.  

   ![Resumo da capacidade reservada](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Selecione **Revisão + compre** e compre **agora.** Quando a compra for bem-sucedida, você verá a seguinte página:

Depois de comprar uma reserva, ela será aplicada imediatamente a qualquer recurso existente do Azure Cosmos DB para corresponder aos termos da reserva. Se você não tiver nenhum recurso existente do Azure Cosmos DB, a reserva será aplicada quando implantar uma nova instância do Cosmos DB que corresponde aos termos da reserva. Em ambos os casos, o período da reserva começa imediatamente após a compra com êxito.

Quando sua reserva expirar, as instâncias do Azure Cosmos DB continuarão sendo executadas e cobradas com as taxas normais de pagamento conforme o uso.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Próximas etapas

O desconto da reserva é aplicado automaticamente aos recursos do Azure Cosmos DB que correspondem ao escopo e aos atributos da reserva. Você pode atualizar o escopo da reserva por meio do portal do Azure, do PowerShell, da CLI do Azure ou da API.

*  Para saber como os descontos da capacidade reservada são aplicados ao Azure Cosmos DB, confira [Entender o desconto de reserva do Azure](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

   * [O que são reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Gerenciar reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Entender o uso de reserva para seu registro de empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Entender o uso de reserva para a sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Reservas do Azure no programa CSP do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
