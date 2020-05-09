---
title: Como os descontos de reserva aplicam-se ao Azure Synapse Analytics | Microsoft Docs
description: Saiba como os descontos de reserva aplicam-se ao Azure Synapse Analytics para ajudá-lo a economizar dinheiro.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: 33a0a0a62814579dee10ae046338ff9f78d667cb
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627068"
---
# <a name="how-reservation-discounts-apply-to-azure-synapse-analytics"></a>Como os descontos de reserva aplicam-se ao Azure Synapse Analytics

Depois de comprar a capacidade reservada do Azure Synapse Analytics, o desconto de reserva é aplicado automaticamente às instâncias provisionadas que existem nessa região. O desconto de reserva aplica-se ao uso emitido pelo medidor de cDWU do Azure Synapse Analytics. O armazenamento e a rede são cobrados com base nas taxas pagas conforme o uso.

## <a name="reservation-discount-application"></a>Aplicação de desconto de reserva

O desconto da capacidade reservada do Azure Synapse Analytics é aplicado à execução de warehouses por hora. Caso você não tenha um warehouse implantado por hora, a capacidade reservada será desperdiçada para essa hora. Ela não é transferida.

Após a compra, é feita a correspondência da reserva adquirida com o uso do Azure Synapse Analytics emitido pela execução de warehouses em qualquer ponto no tempo. Se você desligar alguns warehouses, os descontos de reserva serão aplicados automaticamente a outros warehouses correspondentes.

Para os warehouses que não são executados por uma hora inteira, a reserva é aplicada automaticamente a outras instâncias correspondentes nessa hora.

## <a name="discount-examples"></a>Exemplos de desconto

Os exemplos a seguir mostram como o desconto de capacidade reservada do Azure Synapse Analytics se aplica, dependendo das implantações.

- **Exemplo 1**: Você compra 5 unidades de capacidade reservada de 100 cDWU. Você executa uma instância DW1500c do Azure Synapse Analytics por uma hora. Nesse caso, o uso é emitido para 15 unidades com uso de 100 cDWU. O desconto de reserva se aplica às 5 unidades utilizadas. Você é cobrado com base em taxas pagas conforme o uso para as 10 unidades restantes com uso de 100 cDWU utilizadas. Em outras palavras, a cobertura parcial é possível para várias reservas.

- **Exemplo 2**: Você compra 5 unidades de capacidade reservada de 100 cDWU. Você executa duas instâncias DW100c do Azure Synapse Analytics por uma hora. Nesse caso, dois eventos de uso são emitidos para 1 unidade com uso de 100 cDWU. Os dois eventos de uso obtêm descontos de capacidade reservada. As 3 unidades restantes de capacidade reservada de 100 cDWU são desperdiçadas e não são transferidas para uso futuro. Em outras palavras, uma reserva pode corresponder a várias instâncias do Azure Synapse Analytics.

- **Exemplo 3**: Você compra 1 unidade de capacidade reservada de 100 cDWU. Você executa duas instâncias DW100c do Azure Synapse Analytics. Cada uma é executada por 30 minutos. Nesse caso, os dois eventos de uso obtêm descontos de capacidade reservada. Nenhum uso é cobrado usando as taxas pagas conforme o uso.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

- Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](save-compute-costs-reservations.md)
- [Exibir transações de reserva](view-reservations.md)
- [Obter transações de reserva e utilização por meio da API](reservation-apis.md)
- [Gerenciar reservas](manage-reserved-vm-instance.md)
