---
title: Instâncias reservadas de VM de EA do Azure
description: Este artigo resume como as reservas do Azure para instâncias reservadas de VM podem ajudá-lo a economizar dinheiro com seu Registro Enterprise.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 26c132d42bfc89c9b6e887c8ca02232848513698
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75992069"
---
# <a name="azure-ea-vm-reserved-instances"></a>Instâncias reservadas de VM de EA do Azure

Este artigo resume como as reservas do Azure para instâncias reservadas de VM podem ajudá-lo a economizar dinheiro com seu Registro Enterprise. Para obter mais informações sobre reservas, confira [O que são as Reservas do Azure?](../reservations/save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Trocas e reembolsos de reserva

Você pode trocar uma reserva por outra do mesmo tipo. Também é possível pedir reembolso de uma reserva, de até US$ 50.000 por ano, se não precisar mais dela. O portal do Azure pode ser usado para trocar ou reembolsar uma reserva. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../reservations/exchange-and-refund-azure-reservations.md).

## <a name="reservation-costs-and-usage"></a>Custos e uso de reserva

Clientes do Contrato Enterprise podem exibir dados de custo e uso nas APIs Rest do portal do Azure. Com relação aos custos e ao uso de reservas, você pode:

- Obter dados de compra de reserva.
- Saber qual assinatura, grupo de recursos ou recurso usou uma reserva.
- Estornar a utilização da reserva.
- Calcular a economia de reserva.
- Obter dados de subutilização de reserva.
- Amortizar custos de reserva.

Para obter mais informações sobre custos de reserva e uso, confira [Obter uso e custos de reserva do Contrato Enterprise](../reservations/understand-reserved-instance-usage-ea.md).

Para saber mais sobre os preços, confira [Preços de Máquinas Virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Suporte para a API de instâncias reservadas

Você pode usar as APIs do Azure para obter programaticamente informações para sua organização sobre reservas de software ou serviço do Azure. Para obter mais informações, confira [APIs para automação de reserva do Azure](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Instâncias de máquinas virtuais reservadas do Azure

As instâncias reservadas podem reduzir os custos de sua máquina virtual em até 72% em relação aos preços pagos conforme o uso em todas as VMs ou em até 82% de economia quando combinadas com o benefício híbrido do Azure. É possível priorizar melhor as cargas de trabalho, o orçamento e a previsão com pagamento antecipado para os prazos de um ou de três anos. Você também pode trocar ou cancelar reservas conforme as necessidades do negócio mudam.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Como comprar instância de máquina virtual reservada

Para comprar uma instância de máquina virtual reservada do Azure, um administrador de registro do Azure Enterprise deve habilitar a opção de compra _Reservar instância_ na seção _Detalhes do registro_ da guia _Registro_ no [Portal do EA do Azure](https://ea.azure.com/).

Depois que o registro de EA estiver habilitado para adicionar instâncias reservadas, qualquer proprietário de conta com uma assinatura ativa associada ao registro EA poderá comprar uma instância de máquina virtual reservada no [portal do Azure](https://aka.ms/reservations). Para obter mais informações, consulte [Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias de Máquina Virtual Reservada](https://go.microsoft.com/fwlink/?linkid=861721).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Como exibir detalhes de compra da instância reservada

Você pode exibir seus detalhes de compra da instância reservada por meio do menu _Reservas_ no lado esquerdo do [portal do Azure](https://aka.ms/reservations) ou no [portal de EA do Azure](https://ea.azure.com/). Selecione **Relatórios** no menu do lado esquerdo e role para baixo até a seção _Encargos por serviços_ na guia _Resumo de uso_. Role até a parte inferior da seção e suas compras e uso da instância reservada serão listadas no final, como indicado pela designação de "1 ano" ou "3 anos" ao lado do nome do serviço, por exemplo: Standard_DS1_v2 Leste dos EUA 1 ano ou Standard_D2s_v3 Leste dos EUA 2 3 anos.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Como posso alterar a assinatura associada à instância reservada ou transferir meus benefícios de instância reservada para uma assinatura na mesma conta?

A qualquer momento, apenas uma assinatura pode receber benefícios de instância reservada. Você pode alterar a assinatura que recebe os benefícios da instância reservada:

- Fazendo logon no [Portal do Azure](https://aka.ms/reservations).
- Atualizando o escopo da assinatura aplicada, associando uma assinatura diferente à mesma conta.

### <a name="how-to-view-reserved-instance-usage-details"></a>Como exibir detalhes de uso da instância reservada

Você pode exibir os detalhes de uso da instância reservada no [portal do Azure](https://aka.ms/reservations) ou no [portal do EA do Azure](https://ea.azure.com/) (para clientes do EA que têm acesso para exibir as informações de cobrança) em _Relatórios_ > _Resumo de uso_ > _Encargos por serviços_. Suas instâncias reservadas podem ser identificadas como nomes de serviço que contêm "Reserva", por exemplo: Reservation-Base VM ou Virtual Machines Reservation-Windows Svr (1 Core).

Os detalhes de uso e o CSV de download de relatório avançado contêm informações adicionais de uso da instância reservada. O campo _Informações adicionais_ ajudará a identificar o uso da instância reservada.

Se você não usou o benefício híbrido do Azure para comprar instâncias de VM reservadas do Azure, as instâncias reservadas emitirão dois medidores (hardware e software). Se você usou o benefício híbrido do Azure para comprar a instância reservada, você não verá o medidor de software em seus detalhes de uso da instância reservada.

### <a name="reserved-instance-billing"></a>Cobrança de instância reservada

Para clientes corporativos, o compromisso monetário é usado para comprar instâncias de VM reservadas do Azure. Se o seu registro tiver saldo suficiente de compromisso monetário para cobrir a compra de instância reservada, o valor será deduzido do saldo de compromisso monetário e você não receberá uma fatura da compra.

Nos cenários em que os clientes do Azure EA usaram todo o seu compromisso monetário, ainda é possível comprar instâncias reservadas e essas compras serão faturadas na sua próxima fatura como excedente. O excedente da instância reservada, se houver, fará parte da fatura de excedente regular.

### <a name="reserved-instance-expiration"></a>Expiração da instância reservada

Você receberá notificações por email 30 dias antes da reserva e na expiração. Quando a reserva expirar, as VMs implantadas continuarão a ser executadas e faturadas de acordo com a taxa paga conforme o uso. Para saber mais, confira [Oferta de instâncias de Máquina Virtual Reservada.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre reservas do Azure, confira [O que são as Reservas do Azure?](../reservations/save-compute-costs-reservations.md).
- Para saber mais sobre os custos e o uso da reserva com Contrato Enterprise, confira [Obter custos e uso de reserva do Contrato Enterprise](../reservations/understand-reserved-instance-usage-ea.md).
- Para saber mais sobre os preços, confira [Preços de Máquinas Virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).
