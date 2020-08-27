---
title: Descontos de reserva para o Serviço de Aplicativo do Azure
description: Saiba como os descontos de reserva se aplicam aos Selos Isolados do Serviço de Aplicativo do Azure. Os descontos são aplicados automaticamente à Tarifa de Selo da região.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: f7d3134652d75cb4ce40b3e9366d3083f5d760df
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682613"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Como os descontos de reserva se aplicam aos Selos Isolados do Serviço de Aplicativo do Azure

Após a compra da capacidade reservada do Imposto de Selo Isolado do Serviço de Aplicativo, o desconto de reserva é aplicado automaticamente ao Imposto de Selo em uma região. O desconto de reserva aplica-se ao uso emitido pelo medidor de Imposto de Selo Isolado. Trabalhadores, outros front-ends e outros recursos associados ao selo continuam sendo cobrados na taxa regular.

## <a name="reservation-discount-application"></a>Aplicação de desconto de reserva

O desconto do Imposto de Selo Isolado do Serviço de Aplicativo é aplicado aos selos isolados em execução por hora. Caso você não tenha um selo implantado por hora, a capacidade reservada será desperdiçada para essa hora. Ela não é transferida.

Após a compra, a reserva comprada corresponderá a um selo isolado em execução em uma região específica. Se você desligar esse selo, os descontos de reserva serão aplicados automaticamente a outros selos em execução na região. Quando não existe nenhum selo, a reserva é aplicada ao próximo selo criado na região.

Quando os selos não forem executados durante uma hora inteira, a reserva se aplicará automaticamente a outros selos correspondentes na mesma região durante a mesma hora.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Escolha um tipo de selo – Windows ou Linux

Um Selo Isolado vazio emite o medidor de selo do Windows por padrão. Por exemplo, quando nenhum trabalho é implantado. Ele continuará emitindo o medidor quando os trabalhos do Windows forem implantados. O medidor mudará para o medidor de selo do Linux se você implantar um trabalho do Linux. O selo emite o medidor do Windows quando os trabalhos do Linux e do Windows são implantados.

Em decorrência disso, o medidor do selo pode alternar entre o Windows e o Linux durante a vida útil do selo. Enquanto isso, as reservas são específicas do sistema operacional. Será necessário comprar uma reserva compatível com os trabalhos que você planeja implantar no selo. Selos somente do Windows e selos mistos usam a reserva do Windows. Selos apenas com trabalhos do Linux usam a reserva do Linux.

A única vez em que você deve comprar uma reserva do Linux é quando planeja ter _apenas_ trabalhos do Linux no selo.

## <a name="discount-examples"></a>Exemplos de desconto

Os exemplos a seguir mostram como o desconto de instância reservada do Imposto de Selo Isolado se aplica, dependendo das implantações.

- **Exemplo 1**: você compra uma instância da capacidade de Selo Reservado Isolado em uma região sem Selos Isolados do Serviço de Aplicativo. Você implanta um novo selo na região e paga as taxas reservadas para esse selo.
- **Exemplo 2**: você compra uma instância da capacidade de Selo Reservado Isolado em uma região que já tem um Selo Isolados do Serviço de Aplicativo implantado. Você começa recebendo a taxa reservada do selo implantado.
- **Exemplo 3**: você compra uma instância da capacidade de Selo Reservado Isolado em uma região com um Selo Isolados do Serviço de Aplicativo já implantado. Você começa recebendo a taxa reservada no selo implantado. Posteriormente, exclua o selo e implante um novo. Você recebe a taxa reservada do novo selo. Os descontos não são transferidos para durações sem selos implantados.
- **Exemplo 4**: você compra uma instância da capacidade de Selo Reservado do Linux Isolado em uma região e implanta um novo selo na região. Quando o selo é implantado inicialmente sem trabalhos, ele emite o medidor de selo do Windows. Nenhum desconto é recebido. Quando o primeiro trabalho do Linux tiver implantado o selo, ele emitirá o medidor de Selo do Linux e o desconto de reserva será aplicado. Se um trabalho do Windows for implantado posteriormente no selo, o medidor de selo será revertido para o Windows. Você não receberá mais um desconto para a reserva de Selo Reservado do Linux Isolado.

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre como comprar previamente uma capacidade reservada do Selo Isolado do Serviço de Aplicativo para economizar dinheiro, confira [Pagar antecipadamente o Imposto de Selo Isolado do Serviço de Aplicativo do Azure com capacidade reservada](prepay-app-service-isolated-stamp.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerenciar Reservas no Azure](manage-reserved-vm-instance.md)
  - [Noções básicas sobre o uso de reserva para uma assinatura com taxas pagas conforme o uso](understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
