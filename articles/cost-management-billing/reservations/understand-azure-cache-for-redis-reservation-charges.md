---
title: Entenda como o desconto de reserva é aplicado ao Cache do Azure para Redis | Microsoft Docs
description: Saiba como o desconto de reserva é aplicado a instâncias do Cache do Azure para Redis.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 10fedaf5680976ae93984c9b2c607882f650019d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213274"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>Como o desconto de reserva é aplicado ao Cache do Azure para Redis

Depois de comprar uma capacidade reservada do Cache do Azure para Redis, o desconto de reserva é aplicado automaticamente às instâncias de cache que correspondem aos atributos e à quantidade da reserva. Uma reserva abrange somente os custos de computação do Cache do Azure para Redis. Você será cobrado por armazenamento e rede com as taxas normais. A capacidade reservada está disponível somente para caches do [nível Premium](/https://docs.microsoft.com/azure/azure-cache-for-redis/quickstart-create-redis).

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo ***usar ou perder***. Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão perdidas.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Desconto aplicado ao Cache do Azure para Redis

O desconto de capacidade reservada do Cache do Azure para Redis é aplicado aos seus caches por hora. A reserva que você compra corresponde ao uso de computação emitido pelos caches em execução. Quando esses caches não são executados a hora inteira, a reserva é aplicada automaticamente a outros caches que correspondem aos atributos de reserva. O desconto pode ser aplicado a caches que estão sendo executados simultaneamente. Se você não tiver caches que sejam executados durante a hora inteira e que correspondam aos atributos de reserva, você não obterá o benefício total do desconto de reserva para essa hora.

Os exemplos a seguir mostram que a aplicação do desconto de capacidade reservada do Cache do Azure para Redis depende do número de caches que você comprou e de quando estão em execução.

* **Exemplo 1**: Você compra uma cache capacidade reservada Cache do Azure para Redis para um cache de 6 GB. Se você estiver executando um cache de 13 GB que corresponda ao restante dos atributos da reserva, será cobrado pelo preço pago conforme o uso de 7 GB de sua utilização de computação do Cache do Azure para Redis e obterá o desconto de reserva por uma hora do uso de computação de 6 GB.

Para o restante desses exemplos, suponha que a capacidade reservada do Cache do Azure para Redis que você compra é para um cache de 26 GB e o restante dos atributos de reserva correspondem ao cache em execução.

* **Exemplo 2**: Você executa dois caches de 13 GB por uma hora. O desconto de reserva de 26 GB é aplicado ao uso de computação de ambos os caches.

* **Exemplo 3**: Você executa um de 26 GB das 13h00 às 13h30. Você executa outro cache de 26 GB das 13h30 às 14h00. Ambos são cobertas pelo desconto de reserva.

* **Exemplo 4**: Você executa um de 26 GB das 13h00 às 13h45. Você executa outro cache de 26 GB das 13h30 às 14h00. Você será cobrado o preço pago conforme o uso para a sobreposição de 15 minutos. O desconto de reserva se aplica ao uso de computação para o restante do tempo.

Para entender e visualizar o aplicativo de suas reservas do Azure nos relatórios de uso de faturamento, consulte [Entender o uso de reserva do Azure](/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco
Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
