---
title: Como um desconto de compra prévia do Azure Databricks é aplicado
description: Saiba como um desconto de compra prévia do Azure Databricks se aplica ao seu uso.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c148351a4475bfdbee474a5e0951cc3b5717404e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995722"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Como o desconto de compra prévia do Azure Databricks é aplicado

É possível usar DBCU (unidades de confirmação do Azure Databricks) compradas previamente a qualquer momento durante a vigência da compra. Qualquer uso do Azure Databricks é deduzido das DBCUs compradas previamente de maneira automática.

Diferentemente das VMs, as unidades compradas previamente não expiram por hora. É possível usá-las a qualquer momento durante a vigência da compra. Para ter os descontos da compra prévia, não é necessário reimplantar ou atribuir um plano comprado previamente aos workspaces do Azure Databricks para o uso.

O desconto de compra prévia aplica-se apenas ao uso da DBU (unidade do Azure Databricks). Outros encargos, como computação, armazenamento e rede, são cobrados separadamente.

## <a name="pre-purchase-discount-application"></a>Aplicação do desconto de compra prévia

A compra prévia do Databricks aplica-se a todas as cargas de trabalho e camadas do Databricks. Pense na compra prévia como um pool de unidades de confirmação do Databricks pagas previamente. O uso é deduzido do pool, independentemente da carga de trabalho ou camada. O uso é deduzido na seguinte razão:

| **Carga de trabalho** | **Razão do aplicativo DBU – Camada Standard** | **Razão do aplicativo DBU – Camada Premium** |
| --- | --- | --- |
| Análise de dados | 0,4 | 0,55 |
| Engenharia de Dados | 0.15 | 0,30 |
| Engenharia de Dados Leve | 0,07 | 0,22 |

Por exemplo, quando uma quantidade de Análise de Dados – camada Standard é consumida, as unidades de confirmação do Databricks compradas previamente são deduzidas em 0,4 unidades. Quando uma quantidade de Engenharia de Dados Leve – camada Standard é usada, a unidade de confirmação do Databricks comprada previamente é deduzida em 0,07 unidades

## <a name="determine-plan-use"></a>Determinar o uso do plano

Para determinar o uso do plano de DBCU, acesse o portal do Azure > **Reservas** e clique no plano do Databricks comprado. Sua utilização até a data é mostrada com unidades restantes. Para obter mais informações sobre como determinar o uso de reserva, confira o artigo [Conferir uso de reserva](reservation-apis.md#see-reservation-usage).

## <a name="how-discount-application-shows-in-usage-data"></a>Como a aplicação do desconto é mostrada nos dados de uso

Quando o desconto de compra prévia é aplicado ao seu uso do Databricks, os encargos sob demanda são exibidos como zero nos dados de uso. Para obter mais informações sobre custos de reserva e uso, confira [Obter uso e custos de reserva do Contrato Enterprise](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximos passos

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre como comprar previamente o Azure Databricks para economizar dinheiro, confira [Otimizar custos do Azure Databricks com uma compra prévia](prepay-databricks-reserved-capacity.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerenciar Reservas no Azure](manage-reserved-vm-instance.md)
  - [Noções básicas sobre o uso de reserva para uma assinatura com taxas pagas conforme o uso](understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
