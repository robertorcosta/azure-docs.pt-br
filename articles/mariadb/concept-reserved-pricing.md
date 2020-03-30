---
title: Pré-pago para computação com capacidade reservada - Banco de Dados Azure para MariaDB
description: Pré-pago pelo Banco de Dados Azure para recursos de computação Do MariaDB com capacidade reservada
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a6bd3cab41c69075f5170a8a3aad4f059d970e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159040"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Pré-pago pelo Banco de Dados Azure para recursos de computação Do MariaDB com capacidade reservada

O Azure Database for MariaDB agora ajuda você a economizar dinheiro pagando antecipadamente por recursos de computação em comparação com os preços de pagamento. Com o Banco de Dados Azure para a capacidade reservada do MariaDB, você assume um compromisso inicial com os servidores DoMB por um ou três anos para obter um desconto significativo nos custos de computação. Para comprar o Banco de Dados Azure para a capacidade reservada do MariaDB, você precisa especificar a região do Azure, o tipo de implantação, o nível de desempenho e o prazo. </br>

Você não precisa atribuir a reserva ao Banco de Dados Azure específico para servidores MariaDB. Um banco de dados Azure já em execução para MariaDB ou aqueles que estão recém-implantados, obterá automaticamente o benefício de preços reservados. Ao comprar uma reserva, você está pagando antecipadamente os custos de computação por um período de um ou três anos. Assim que você compra uma reserva, o banco de dados Azure para taxas de cálculo DoMDB que correspondem aos atributos de reserva não são mais cobrados nas taxas de pagamento à medida que você vai. Uma reserva não cobre taxas de software, rede ou armazenamento associadas ao servidor MariaDB Database. No final do prazo de reserva, o benefício de faturamento expira, e o Banco de Dados Azure para MariaDB é cobrado pelo preço de pagamento à medida que você vai. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte o [Banco de Dados Azure para oferta de capacidade reservada MariaDB](https://azure.microsoft.com/pricing/details/mariadb/). </br>

Você pode comprar o Banco de Dados Azure para a capacidade reservada do MariaDB no [portal Azure](https://portal.azure.com/). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário por pelo menos uma assinatura enterprise ou individual com taxas de pagamento.
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes de administradorou agentes de vendas podem comprar o Banco de Dados Azure para capacidade reservada Do MariaDB. </br>

Os detalhes sobre como os clientes corporativos e clientes Pay-As-You-Go são cobrados para compras de reservas, veja [entender o uso da reserva do Azure para sua inscrição na Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e entenda o uso da reserva do [Azure para sua assinatura Pay-As-You-Go.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-server-size-before-purchase"></a>Determine o tamanho certo do servidor antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pela instância de bancos de dados existentes ou em breve em uma região específica e usando o mesmo nível de desempenho e geração de hardware.</br>

Por exemplo, vamos supor que você esteja executando um propósito geral, gen5 – 32 vCore MariaDB database e dois bancos de dados gen5 – 16 vCore MariaDB. Além disso, vamos supor que você planeje implantar no próximo mês um propósito geral adicional, o servidor de banco de dados Gen5 – 32 vCore, e uma memória otimizada, gen5 – 16 vCore servidor de banco de dados. Vamos supor que você saiba que você vai precisar desses recursos por pelo menos 1 ano. Neste caso, você deve comprar um vCores 64 (2x32), reserva de 1 ano para um único banco de dados de propósito geral - Gen5 e uma reserva de 48 (2x16 + 16) vCore 1 ano para memória de banco de dados único otimizada - Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Comprar banco de dados Do Zure para a capacidade reservada do MariaDB

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Selecione Todas as**reservas** **de serviços** > .
3.  Selecione **Adicionar** e, em seguida, no painel De compras reservas, selecione **Azure Database para MariaDB** para comprar uma nova reserva para seus bancos de dados MariaDB.
4.  Preencha os campos necessários. Bancos de dados existentes ou novos que correspondam aos atributos selecionados se qualificam para obter o desconto de capacidade reservado. O número real do seu Banco de Dados Azure para servidores MariaDB que recebem o desconto depende do escopo e quantidade selecionados.


![Visão geral dos preços reservados](media/concepts-reserved-pricing/mariadb-reserved-price.png)


A tabela a seguir descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscription   | A assinatura era usada para pagar o Banco de Dados Do Azure para reserva de capacidade reservada do MariaDB. O método de pagamento na assinatura é cobrado dos custos iniciais do Banco de Dados Do Azure para a reserva de capacidade reservada Do MariaDB. O tipo de assinatura deve ser um contrato corporativo (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para uma assinatura individual com preços de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na assinatura.
| Escopo | O escopo da reserva vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: </br></br> **Compartilhado**, o desconto de reserva vCore é aplicado ao Banco de Dados Do Azure para servidores MariaDB em execução em quaisquer assinaturas dentro do seu contexto de faturamento. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</br></br> **Assinatura única**, o desconto de reserva vCore é aplicado ao Banco de Dados Do Azure para servidores MariaDB nesta assinatura. </br></br> **Grupo de recursos únicos**, o desconto de reserva é aplicado ao Banco de Dados Do Azure para servidores MariaDB na assinatura selecionada e no grupo de recursos selecionado dentro dessa assinatura.
| Região | A região do Azure que é coberta pelo Banco de Dados Azure para reserva de capacidade reservada do MariaDB.
| Tipo de implantação | O banco de dados Do Azure para o tipo de recurso MariaDB para o que você deseja comprar a reserva.
| Nível de desempenho | O nível de serviço do Banco de Dados Azure para servidores MariaDB.
| Termo | Um ano
| Quantidade | A quantidade de recursos computacionais que estão sendo adquiridos no Banco de Dados Do Azure para reserva de capacidade reservada do MariaDB. A quantidade é um número de vCores na região azure selecionada e nível performance que estão sendo reservados e receberão o desconto de faturamento. Por exemplo, se você estiver executando ou planejando executar um banco de dados Azure para servidores MariaDB com a capacidade total de computação de gen5 16 vCores na região leste dos EUA, então você especificaria quantidade como 16 para maximizar o benefício para todos os servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Flexibilidade de tamanho do vCore

A flexibilidade de tamanho do vCore ajuda você a aumentar ou diminuir dentro de uma região e nível de desempenho sem perder o benefício de capacidade reservada. 

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva vCore é aplicado automaticamente ao número de servidores Azure Database para servidores MariaDB que correspondem ao Banco de Dados Azure para escopo e atributos de reserva de capacidade reservada do MariaDB. Você pode atualizar o escopo do banco de dados Azure para reserva de capacidade reservada do MariaDB através do portal Azure, PowerShell, CLI ou através da API. </br></br>
Para saber como gerenciar o Banco de Dados Azure para capacidade reservada do MariaDB, consulte gerenciar o Banco de Dados Azure para a capacidade reservada do MariaDB.

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

* [O que são reservas do Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Gerenciar reservas do Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Compreender o desconto de Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Entender o uso de reserva para a sua assinatura paga conforme o uso](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Entender o uso de reserva para seu registro de empresa](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
