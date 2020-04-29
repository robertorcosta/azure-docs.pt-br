---
title: Pagar antecipadamente por computação com capacidade reservada-cache do Azure para Redis
description: Pagar antecipadamente pelo cache do Azure para recursos de computação Redis com capacidade reservada
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77530296"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Pagar antecipadamente pelo cache do Azure para recursos de computação Redis com capacidade reservada

O cache do Azure para Redis agora ajuda a economizar dinheiro prestando por recursos de computação em comparação com os preços pagos conforme o uso. Com o cache do Azure para capacidade reservada Redis, você faz um compromisso antecipado no cache por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para comprar o cache do Azure para a capacidade reservada do Redis, você precisa especificar a região do Azure, a camada de serviço e o termo.

Você não precisa atribuir a reserva a um cache do Azure específico para instâncias Redis. Um cache do Azure já em execução para Redis ou aqueles que foram implantados recentemente obterá automaticamente o benefício do preço reservado, até o tamanho do cache reservado. Ao comprar uma reserva, você está pagando antecipadamente os custos de computação por um período de um ou três anos. Assim que você comprar uma reserva, o cache do Azure para encargos de computação Redis que correspondam aos atributos de reserva não serão cobrados com as tarifas pagas conforme o uso. Uma reserva não abrange encargos de rede ou armazenamento associados ao cache. No final do prazo de reserva, o benefício de cobrança expira e o cache do Azure para Redis é cobrado pelo preço pago conforme o uso. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada de cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache).

Você pode comprar o cache do Azure para a capacidade reservada do Redis no [portal do Azure](https://portal.azure.com/). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar o cache do Azure para a capacidade reservada do Redis.

Para obter detalhes sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e [entender o uso de reserva do Azure para sua assinatura pré-paga](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-cache-size-before-purchase"></a>Determinar o tamanho de cache correto antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelo cache existente ou em breve para ser implantado em uma região específica e usando a mesma camada de serviço.

Por exemplo, vamos supor que você esteja executando uma finalidade geral, Gen5 – cache vCore de 32 e dois caches com otimização de memória, Gen5 – 16. Além disso, digamos que você planeja implantar no próximo mês uma finalidade geral adicional, Gen5 – servidor de banco de dados vCore de 32 e um servidor de banco de dados com otimização de memória, Gen5 – 16 vCore. Vamos supor que você saiba que precisará desses recursos por pelo menos 1 ano. Nesse caso, você deve comprar um vCores de 64 (2x32), reserva de 1 ano para o banco de dados único de uso geral-Gen5 e um 48 (2x16 + 16) reserva de 1 ano para a memória de banco de dados individual otimizada-Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Comprar o cache do Azure para capacidade reservada do Redis

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **todas as** > **reservas**de serviços.
3. Selecione **Adicionar** e, em seguida, no painel reservas de compra, selecione **cache do Azure para Redis** para comprar uma nova reserva para seus caches.
4. Preencha os campos obrigatórios. Bancos de dados novos ou existentes que correspondem aos atributos que você selecionar qualificar para obter o desconto de capacidade reservada. O número real de seu cache do Azure para instâncias Redis que obtém o desconto depende do escopo e da quantidade selecionada.


![Visão geral de preços reservados](media/cache-reserved-pricing/cache-reserved-price.png)


A tabela a seguir descreve os campos obrigatórios.

| Campo | Descrição |
| :------------ | :------- |
| Subscription   | A assinatura usada para pagar pelo cache do Azure para reserva de capacidade reservada do Redis. O método de pagamento na assinatura é cobrado pelos custos antecipados para o cache do Azure para reserva de capacidade reservada Redis. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.
| Escopo | O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: </br></br> **Compartilhado**, o desconto de reserva é aplicado ao cache do Azure para instâncias Redis em execução em qualquer assinatura em seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</br></br> **Assinatura única**, o desconto de reserva é aplicado ao cache do Azure para instâncias Redis nesta assinatura. </br></br> **Um grupo de recursos único**, o desconto de reserva é aplicado ao cache do Azure para instâncias Redis na assinatura selecionada e o grupo de recursos selecionado dentro dessa assinatura.
| Região | A região do Azure que é coberta pelo cache do Azure para reserva de capacidade reservada do Redis.
| Tipo de preço | A camada de serviço para o cache do Azure para servidores Redis.
| Termo | Um ano ou três anos
| Quantidade | A quantidade de recursos de computação que está sendo adquirida no cache do Azure para reserva de capacidade reservada Redis. A quantidade é um número de caches na região do Azure selecionada e na camada de serviço que estão sendo reservados e obterá o desconto de cobrança. Por exemplo, se você estiver executando ou planejando executar um cache do Azure para servidores Redis com a capacidade total de cache de 26 GB na região leste dos EUA, você deverá especificar a quantidade como 26 para maximizar o benefício de todos os caches.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Flexibilidade do tamanho do cache

A flexibilidade do tamanho do cache ajuda a escalar ou reduzir verticalmente dentro de uma camada de serviço e região, sem perder o benefício de capacidade reservada.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva é aplicado automaticamente ao cache do Azure para instâncias Redis que correspondem ao escopo e aos atributos de reserva. Você pode atualizar o escopo da reserva por meio do portal do Azure, do PowerShell, da CLI do Azure ou da API.

*  Para saber como os descontos de capacidade reservados são aplicados ao cache do Azure para Redis, consulte [entender o desconto de reserva do Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

    * [O que são as reservas do Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Gerenciar Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Compreender o desconto de Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Entender o uso de reserva para a sua assinatura paga conforme o uso](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Entender o uso de reserva para seu registro de empresa](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

