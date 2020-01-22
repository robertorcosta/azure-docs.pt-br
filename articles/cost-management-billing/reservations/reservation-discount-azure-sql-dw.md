---
title: Como os descontos de reserva se aplicam ao SQL Data Warehouse do Azure | Microsoft Docs
description: Saiba como os descontos de reserva se aplicam ao SQL Data Warehouse do Azure para ajudá-lo a economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: 24a673dc7949d5ce05aa1a701cc8b939a766fd99
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313919"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Como os descontos de reserva se aplicam ao SQL Data Warehouse do Azure

Após a compra da capacidade reservada do SQL Data Warehouse do Azure, o desconto de reserva é aplicado automaticamente aos data warehouses existentes nessa região. O desconto de reserva aplica-se ao uso emitido pelo medidor de cDWU do SQL Data Warehouse. O armazenamento e a rede são cobrados com base nas taxas pagas conforme o uso.

## <a name="reservation-discount-application"></a>Aplicação de desconto de reserva

O desconto de capacidade reservada do SQL Data Warehouse é aplicado aos warehouses em execução por hora. Caso você não tenha um warehouse implantado por hora, a capacidade reservada será desperdiçada para essa hora. Ela não é transferida.

Após a compra, é feita a correspondência da reserva adquirida com o uso do SQL Data Warehouse emitido pela execução de warehouses em qualquer ponto no tempo. Se você desligar alguns warehouses, os descontos de reserva serão aplicados automaticamente a outros warehouses correspondentes.

Para os warehouses que não são executados por uma hora inteira, a reserva é aplicada automaticamente a outras instâncias correspondentes nessa hora.

## <a name="discount-examples"></a>Exemplos de desconto

Os exemplos a seguir mostram como o desconto de capacidade reservada do SQL Data Warehouse se aplica, dependendo das implantações.

- **Exemplo 1**: você compra 5 unidades de 100 cDWU de capacidade reservada. Você executa uma instância do SQL Data Warehouse de DW1500c por uma hora. Nesse caso, o uso é emitido para 15 unidades com uso de 100 cDWU. O desconto de reserva se aplica às 5 unidades utilizadas. Você é cobrado com base em taxas pagas conforme o uso para as 10 unidades restantes com uso de 100 cDWU utilizadas. Em outras palavras, a cobertura parcial é possível para várias reservas.

- **Exemplo 2**: você compra 5 unidades de 100 cDWU de capacidade reservada. Você executa duas instâncias do SQL Data Warehouse de DW100c por uma hora. Nesse caso, dois eventos de uso são emitidos para 1 unidade com uso de 100 cDWU. Os dois eventos de uso obtêm descontos de capacidade reservada. As 3 unidades restantes de capacidade reservada de 100 cDWU são desperdiçadas e não são transferidas para uso futuro. Em outras palavras, uma única reserva pode ser correspondida a várias instâncias de SQL Data Warehouse.

- **Exemplo 3**: você compra uma unidade de 100 cDWU de capacidade reservada. Você executa duas instâncias do SQL Data Warehouse de DW100c. Cada uma é executada por 30 minutos. Nesse caso, os dois eventos de uso obtêm descontos de capacidade reservada. Nenhum uso é cobrado usando as taxas pagas conforme o uso.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contate-nos

- Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](save-compute-costs-reservations.md)
- [Exibir transações de reserva](view-reservations.md)
- [Obter transações de reserva e utilização por meio da API](reservation-apis.md)
- [Gerenciar reservas](manage-reserved-vm-instance.md)
