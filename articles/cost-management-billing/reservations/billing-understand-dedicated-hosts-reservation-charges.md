---
title: Noções básicas sobre o desconto de Instâncias Reservadas de Host Dedicado do Azure
description: Saiba como o desconto em Instâncias de VM Reservadas do Azure é aplicado aos Hosts Dedicados do Azure.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: a39cd7aa2c15fedeaf69408d8c8ae8c6b0848fed
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677398"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Como o desconto de reserva do Azure é aplicado aos Hosts Dedicados do Azure

Após comprar uma Instância Reservada de Host Dedicado do Azure, o desconto de reserva é aplicado automaticamente aos hosts dedicados correspondentes aos atributos e à quantidade da reserva. Uma reserva abrange os custos de computação dos hosts dedicados.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo "*usar ou perder*". Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você exclui um host dedicado, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Desconto de reserva para Hosts Dedicados

A instância reservada de Hosts Dedicados do Azure fornece um desconto no custo da infraestrutura de computação usada com seus hosts dedicados. O desconto se aplica aos seus hosts dedicados, independentemente de estarem sendo utilizados por máquinas virtuais ou não. A reserva não cobre custos adicionais, como licenciamento, uso de rede ou consumo de armazenamento por máquina virtual implantada no host dedicado.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda,  [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Usando Hosts Dedicados do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Preço de Hosts Dedicados](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gerenciar reservas do Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Entender o uso de reserva para a sua assinatura paga conforme o uso](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Entender o uso de reserva para seu registro de empresa](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Entender o uso de reserva para assinaturas de CSP](https://docs.microsoft.com/partner-center/azure-reservations)

