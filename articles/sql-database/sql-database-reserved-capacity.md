---
title: Economizar custos
description: Aprenda a comprar a capacidade reservada do Banco de Dados SQL do Azure para economizar nos custos de seu computador.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979983"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Economizar custos para recursos de computação do Banco de Dados SQL com capacidade reservada do Banco de Dados SQL do Azure

Economize dinheiro com o Banco de Dados SQL do Azure comprometendo-se com uma reserva de recursos de computação em comparação com os preços de pagamento à medida que você vai. Com a capacidade reservada do Banco de Dados Azure SQL, você assume um compromisso para o uso do Banco de Dados SQL por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para comprar a capacidade reservada do Banco de Dados SQL, é necessário especificar a região do Azure, o tipo de implantação, o nível de desempenho e o prazo.


Você não precisa atribuir a reserva a instâncias específicas do Banco de Dados SQL (bancos de dados individuais, pools elásticos ou instâncias gerenciadas). As instâncias de banco de dados SQL correspondentes, que já estão em execução ou recém-implantadas, receberão automaticamente o benefício. Ao comprar uma reserva, você se compromete a usar os custos de computação por um período de um ou três anos. Assim que você compra uma reserva, as cobranças de computação do Banco de Dados SQL que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento conforme o uso. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância do banco de dados SQL. No final do período de reserva, o benefício de cobrança expira e os Bancos de Dados SQL são cobrados pelo preço de pré-pagamento. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Você pode comprar capacidade reservada do Banco de Dados SQL do Azure na [portal do Azure](https://portal.azure.com). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/monthly-payments-reservations.md). Capacidade reservada comprar o banco de dados SQL:

- Você deve estar na função de proprietário por pelo menos uma assinatura enterprise ou individual com taxas de pagamento.
- Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
- Para o programa Provedor de Solução de Nuvem (CSP), apenas os agentes admin ou agentes de vendas podem adquirir a capacidade reservada do Banco de Dados SQL.

Os detalhes sobre como os clientes corporativos e clientes Pay-As-You-Go são cobrados para compras de reservas, veja [entender o uso da reserva do Azure para sua inscrição na Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e entenda o uso da reserva do [Azure para sua assinatura Pay-As-You-Go.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-sql-size-before-purchase"></a>Determinar o tamanho correto do SQL antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelos bancos de dados existentes ou prestes a serem implantados e/ou pools elásticos ou instâncias gerenciadas dentro de uma região específica e usando o mesmo nível de desempenho e geração de hardware.

Por exemplo, vamos supor que você esteja executando uma finalidade geral, o pool elástico Gen5 - 16 vCore e dois bancos de dados únicos Gen5 - 4 vCore essenciais para os negócios. Além disso, suponhamos que você planeje implantar, no próximo mês, uma finalidade geral adicional, o pool elástico Gen5 - 16 vCore e um pool elástico de v5 core Gen5 - 32 para negócios críticos. Além disso, vamos supor que você saiba que precisará desses recursos por pelo menos 1 ano. Neste caso você deve comprar uma reserva de um ano de 32 (2x16) vCores para banco de dados individual/pool elástico de uso geral – Gen5 e uma reserva de um ano de 40 (2x4 + 32) vCores para banco de dados individual/pool elástico comercialmente crítico – Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Comprar capacidade reservada do banco de dados do SQL

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione Todas as**reservas** **de serviços** > .
3. Selecione **Adicionar** e, em seguida, no painel De compras reservas, selecione **SQL Database** para comprar uma nova reserva para Banco de Dados SQL.
4. Preencha os campos necessários. Bancos de dados individuais, pools elásticos ou instâncias gerenciadas novos ou existentes que correspondem aos atributos que você selecionou se qualificam para obter o desconto de capacidade reserva. O número real de suas instâncias do Banco de Dados SQL que recebem o desconto depende do escopo e da quantidade selecionada.
    ![Captura de tela antes de enviar a compra de capacidade reservada do Banco de Dados SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

A tabela a seguir descreve os campos necessários.

| Campo      | Descrição|
|------------|--------------|
|Subscription|A assinatura usada para pagar pela reserva de capacidade reservada do Banco de Dados SQL. O método de pagamento na assinatura é cobrado pelos custos iniciais da reserva de capacidade reservada do Banco de Dados SQL. O tipo de assinatura deve ser um contrato corporativo (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para uma assinatura individual com preços de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na assinatura.|
|Escopo       |O escopo da reserva vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <br/><br/>**Compartilhado,** o desconto de reserva vCore é aplicado às instâncias do Banco de Dados SQL em execução em quaisquer assinaturas dentro do seu contexto de faturamento. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.<br/><br/>**Assinatura única**, o desconto de reserva vCore é aplicado às instâncias do Banco de Dados SQL nesta assinatura. <br/><br/>**Grupo de recursos únicos**, o desconto de reserva é aplicado às instâncias do Banco de Dados SQL na assinatura selecionada e no grupo de recursos selecionado dentro dessa assinatura.|
|Região      |A região do Azure abrangida pela reserva de capacidade reservada do Banco de Dados SQL.|
|Tipo de implantação|O tipo de recurso de SQL para o qual você deseja comprar a reserva.|
|Nível de desempenho|A camada de serviço para as instâncias do Banco de Dados SQL.
|Termo        |Um ano ou três anos.|
|Quantidade    |A quantidade de recursos computacionais que estão sendo adquiridos dentro do Banco de Dados SQL reservou reserva de capacidade. A quantidade é um número de vCores na região azure selecionada e nível performance que estão sendo reservados e receberão o desconto de faturamento. Por exemplo, se você estiver executando ou planejando executar instâncias do Banco de Dados SQL com a capacidade total de computação de gen5 16 vCores na região leste dos EUA, então você especificaria a quantidade como 16 para maximizar o benefício para todas as instâncias. |

1. Examine o custo do banco de dados SQL reservadas reserva de capacidade na **custos** seção.
1. Selecione **Comprar**.
1. Selecione **Exibir essa Reserva** para ver o status de sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilidade de tamanho do vCore

A flexibilidade de tamanho do vCore ajuda você a aumentar ou diminuir dentro de uma região e nível de desempenho sem perder o benefício de capacidade reservada. A capacidade reservada do Banco de Dados SQL também lhe oferece a flexibilidade para transferir temporariamente seus bancos de dados frequentes entre pools e bancos de dados individuais como parte de suas operações normais (dentro da mesma região e nível de desempenho) sem perder o benefício de capacidade reservada. Ao manter um buffer não aplicado em sua reserva, é possível gerenciar com eficiência os picos de desempenho sem exceder o orçamento.

## <a name="limitation"></a>Limitações

Não é possível reservar bancos de dados SQL baseados em DTU (básico, padrão ou premium).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva vCore é aplicado automaticamente ao número de instâncias do Banco de Dados SQL que correspondem ao escopo e aos atributos de reserva de capacidade reservados do Banco de Dados SQL. Você pode atualizar o escopo da reserva de capacidade reservada do Banco de Dados SQL por meio do [Portal do Azure](https://portal.azure.com), PowerShell, CLI ou por meio da API.

Para saber como gerenciar a reserva de capacidade reserva do Banco de Dados SQL, confira [gerenciar a capacidade reserva de Banco de Dados SQL](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gerenciar reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Compreender o desconto de Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
