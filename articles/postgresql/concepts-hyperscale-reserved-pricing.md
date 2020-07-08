---
title: Preço de computação reservado-banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: Pagar antecipadamente pelo banco de dados do Azure para PostgreSQL – recursos de computação de Citus (hiperescala) com capacidade reservada
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: dcf38a3bcd41ba41bb7cf3b16d022a7a45734c4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85217843"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Pagar antecipadamente pelo banco de dados do Azure para PostgreSQL – recursos de computação de Citus (hiperescala) com capacidade reservada

O banco de dados do Azure para PostgreSQL – Citus (hiperescala) agora ajuda você a economizar dinheiro prestando por recursos de computação em comparação com os preços pagos conforme o uso. Com a capacidade reservada de Citus (hiperescala), você faz um compromisso antecipado no grupo de servidores de hiperescala (Citus) por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para adquirir a capacidade reservada do Citus (hiperescala), você precisa especificar a região do Azure, o termo de reserva e a frequência de cobrança.

> [!IMPORTANT]
> Esta página é sobre a capacidade reservada para o banco de dados do Azure para PostgreSQL – Citus (hiperescala). Consulte [esta página](/azure/postgresql/concept-reserved-pricing) para obter informações sobre a capacidade reservada para o banco de dados do Azure para PostgreSQL – servidor único.

Você não precisa atribuir a reserva a grupos de servidores Citus (hiperscale) específicos. Um grupo de servidores que já está executando o Citus (hiperscale) ou aqueles que foram implantados recentemente obterá automaticamente o benefício do preço reservado. Ao comprar uma reserva, você está pagando pelos custos de computação por um período de um ou três anos. Assim que você comprar uma reserva, os encargos de computação de hiperescala (Citus) que correspondam aos atributos de reserva não serão mais cobrados com as tarifas pagas conforme o uso. Uma reserva não abrange os encargos de software, rede ou armazenamento associados aos grupos de servidores de hiperescala (Citus). No final do prazo de reserva, o benefício de cobrança expira e os grupos de servidores de hiperescala (Citus) são cobrados pelo preço pago conforme o uso. As reservas não são renovadas de autorenovação. Para obter informações sobre preços, consulte a [oferta de capacidade reservada do banco de dados do Azure para PostgreSQL – Citus (hiperescala)](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

Você pode comprar a capacidade reservada de Citus (hiperescala) no [portal do Azure](https://portal.azure.com/). Pague pela reserva [antecipadamente ou com pagamentos mensais](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem adquirir capacidade reservada de Citus (hiperescala).

Para obter detalhes sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e [entender o uso de reserva do Azure para sua assinatura pré-paga](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).

## <a name="determine-the-right-server-group-size-before-purchase"></a>Determinar o tamanho do grupo de servidores correto antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelo coordenador ou nós de trabalho existentes ou em breve para serem implantados em grupos de servidores de hiperescala (Citus) em uma região específica.

Por exemplo, vamos supor que você esteja executando um grupo de servidores de hiperescala (Citus) com 16 coordenador vCore e três nós de trabalho vCore. Além disso, vamos supor que você planeja implantar no próximo mês um grupo de servidores adicional de hiperescala (Citus) com um coordenador vCore de 32 e dois nós de trabalho vCore. Vamos supor que você precisará desses recursos por pelo menos um ano.

Nesse caso, você deve comprar uma reserva de um ano para

* Total de 16 vCores + 32 vCores = 48 vCores para nós de coordenador
* Total de três nós x 8 vCores + 2 nós x 4 vCores = 24 + 8 = 32 vCores para nós de trabalho

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Comprar a capacidade reservada do banco de dados do Azure para PostgreSQL

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel reservas de compra, selecione **banco de dados do Azure para PostgreSQL** para comprar uma nova reserva para seus bancos de dados PostgreSQL.
4. Selecione tipo de computação de hiperescala (Citus) para comprar e clique no botão **selecionar** .
5. Examine a quantidade do tipo de computação selecionado na guia **produtos** .
4. Vá para a guia **comprar + revisão** para concluir a compra.

A tabela a seguir descreve os campos obrigatórios.

| Campo        | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription | A assinatura usada para pagar pela reserva de capacidade reservada do banco de dados do Azure para PostgreSQL. O método de pagamento na assinatura é cobrado pelos custos antecipados para a reserva de capacidade reservada do banco de dados do Azure para PostgreSQL. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura corporativa, os encargos são deduzidos do saldo de compromisso monetário do registro ou cobrados como excedentes. Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.                                                                                  |
| Escopo        | O escopo da reserva vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <br><br> **Compartilhado,** o desconto de reserva de vCore é aplicado aos grupos de servidores de hiperescala (Citus) em execução em qualquer assinatura em seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro.  Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta. <br><br> **Assinatura única,** o desconto de reserva vCore é aplicado aos grupos de servidores de hiperescala (Citus) nesta assinatura. <br><br> **Um grupo de recursos único,** o desconto de reserva é aplicado aos grupos de servidores de hiperescala (Citus) na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura. |
| Região       | A região do Azure coberta pela reserva de capacidade reservada do banco de dados do Azure para PostgreSQL – Citus (hiperescala).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Termo         | Um ou três anos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Quantidade     | A quantidade de recursos de computação que está sendo adquirida na reserva de capacidade reservada (Citus) de hiperescala. Em particular, o número de vCores do nó de operador ou coordenador na região do Azure selecionada que está sendo reservada e que obterá o desconto de cobrança. Por exemplo, se você estiver executando (ou planejando) grupos de servidores de hiperescala (Citus) com a capacidade total de computação do nó de coordenador 64 vCores e 32 nó de trabalho vCores na região leste dos EUA, você deverá especificar a quantidade como 64 e 32 para nós de coordenador e de trabalho, respectivamente, para maximizar o benefício para todos os servidores.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Flexibilidade de tamanho do vCore

a flexibilidade do tamanho vCore ajuda você a escalar ou reduzir verticalmente os nós de coordenador e de trabalho em uma região, sem perder o benefício de capacidade reservada.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva vCore é aplicado automaticamente ao número de grupos de servidores de hiperescala (Citus) que correspondem ao banco de dados do Azure para atributos e escopo de reserva de capacidade reservada do PostgreSQL. Você pode atualizar o escopo da reserva de capacidade reservada do banco de dados do Azure para PostgreSQL – Citus (hiperescala) por meio de portal do Azure, PowerShell, CLI ou por meio da API.

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

* [O que são Reservas do Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Gerenciar Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Compreender o desconto de Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Entender o uso de reserva para a sua assinatura paga conforme o uso](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Entender o uso de reserva para seu registro de empresa](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
