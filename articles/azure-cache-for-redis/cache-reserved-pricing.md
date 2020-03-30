---
title: Pré-pago para computação com capacidade reservada - Cache Azure para Redis
description: Pré-pago pelo Cache Azure para recursos de computação Redis com capacidade reservada
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530296"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Pré-pago pelo Cache Azure para recursos de computação Redis com capacidade reservada

O Azure Cache for Redis agora ajuda você a economizar dinheiro pagando antecipadamente por recursos de computação em comparação com os preços de pagamento à medida que você vai. Com o Cache Do Azure para capacidade reservada Redis, você faz um compromisso inicial no cache por um ou três anos para obter um desconto significativo nos custos de computação. Para comprar o Cache Do Azure para capacidade reservada Redis, você precisa especificar a região do Azure, o nível de serviço e o prazo.

Você não precisa atribuir a reserva a um cache azure específico para instâncias Redis. Um cache Azure já em execução para Redis ou aqueles que são recém-implantados obterá automaticamente o benefício de preços reservados, até o tamanho do cache reservado. Ao comprar uma reserva, você está pagando antecipadamente os custos de computação por um período de um ou três anos. Assim que você compra uma reserva, as taxas de cálculo do Azure Cache for Redis que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento à medida que você vai. Uma reserva não cobre taxas de rede ou armazenamento associadas ao cache. No final do prazo de reserva, o benefício de faturamento expira e o Cache Azure para Redis é cobrado pelo preço de pagamento. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte o [Cache Do Azure para oferta de capacidade reservada Redis](https://azure.microsoft.com/pricing/details/cache).

Você pode comprar o Azure Cache para a capacidade reservada Redis no [portal Azure](https://portal.azure.com/). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário por pelo menos uma assinatura enterprise ou individual com taxas de pagamento.
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa CSP (Cloud Solution Provider, provedor de soluções em nuvem), apenas os agentes de administradores ou agentes de vendas podem comprar o Cache Azure para a capacidade reservada Redis.

Para obter detalhes sobre como os clientes corporativos e clientes Pay-As-You-Go são cobrados para compras de reserva, consulte entender o [uso da reserva do Azure para sua inscrição na Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e entenda o uso da reserva do [Azure para sua assinatura Pay-As-You-Go.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-cache-size-before-purchase"></a>Determine o tamanho certo do cache antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelo cache existente ou em breve a ser implantado em uma região específica e usando o mesmo nível de serviço.

Por exemplo, vamos supor que você esteja executando um propósito geral, gen5 – 32 vCore cache, e dois caches de memória otimizados, Gen5 – 16 vCore caches. Além disso, vamos supor que você planeje implantar no próximo mês um propósito geral adicional, o servidor de banco de dados Gen5 – 32 vCore, e uma memória otimizada, gen5 – 16 vCore servidor de banco de dados. Vamos supor que você saiba que você vai precisar desses recursos por pelo menos 1 ano. Neste caso, você deve comprar um vCores 64 (2x32), reserva de 1 ano para um único banco de dados de propósito geral - Gen5 e uma reserva de 48 (2x16 + 16) vCore 1 ano para memória de banco de dados único otimizada - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Comprar cache azure para capacidade reservada Redis

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Selecione Todas as**reservas** **de serviços** > .
3. Selecione **Adicionar** e, em seguida, no painel De compras reservas, selecione **Azure Cache for Redis** para comprar uma nova reserva para seus caches.
4. Preencha os campos necessários. Bancos de dados existentes ou novos que correspondam aos atributos selecionados se qualificam para obter o desconto de capacidade reservado. O número real do cache do Azure para instâncias Redis que recebem o desconto depende do escopo e da quantidade selecionados.


![Visão geral dos preços reservados](media/cache-reserved-pricing/cache-reserved-price.png)


A tabela a seguir descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscription   | A assinatura usada para pagar o Cache Do Azure para reserva de capacidade reservada Redis. O método de pagamento na assinatura é cobrado dos custos iniciais do Cache Do Azure para reserva de capacidade reservada Redis. O tipo de assinatura deve ser um contrato corporativo (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para uma assinatura individual com preços de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na assinatura.
| Escopo | O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: </br></br> **Compartilhado**, o desconto de reserva é aplicado ao Cache do Azure para instâncias Redis em execução em quaisquer assinaturas dentro do seu contexto de faturamento. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</br></br> **Assinatura única**, o desconto de reserva é aplicado ao Cache Do Azure para instâncias Redis nesta assinatura. </br></br> **Grupo de recursos únicos**, o desconto de reserva é aplicado ao Cache do Azure para instâncias Redis na assinatura selecionada e no grupo de recursos selecionado dentro dessa assinatura.
| Região | A região do Azure que é coberta pelo Cache Azure para reservas de capacidade reservadas redis.
| Tipo de preço | O nível de serviço do Cache Azure para servidores Redis.
| Termo | Um ano ou três anos
| Quantidade | A quantidade de recursos de computação que estão sendo comprados dentro do Cache Do Azure para reserva de capacidade reservada Redis. A quantidade é uma série de caches na região e nível de serviço selecionados do Azure que estão sendo reservados e receberão o desconto de faturamento. Por exemplo, se você estiver executando ou planejando executar um cache Azure para servidores Redis com a capacidade total de cache de 26 GB na região leste dos EUA, então você especificaria quantidade como 26 para maximizar o benefício para todos os caches.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Flexibilidade do tamanho do cache

A flexibilidade do tamanho do cache ajuda a escalar para cima ou para baixo dentro de um nível de serviço e região, sem perder o benefício de capacidade reservado.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva é aplicado automaticamente ao Cache do Azure para instâncias Redis que correspondem ao escopo e atributos de reserva. Você pode atualizar o escopo da reserva por meio do portal do Azure, do PowerShell, da CLI do Azure ou da API.

*  Para saber como os descontos de capacidade reservados são aplicados ao Cache Azure para Redis, consulte [Entenda o desconto de reserva do Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

    * [O que são Reservas do Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Gerenciar reservas do Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Compreender o desconto de Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Entender o uso de reserva para a sua assinatura paga conforme o uso](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Entender o uso de reserva para seu registro de empresa](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

