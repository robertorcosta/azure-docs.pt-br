---
title: Preços e custos associados
description: Saiba mais sobre os custos associados ao Azure Security Center para IoT e como controlá-los.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: fe117cf8d05ba3392b71858acf94d1fc88c1a527
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311572"
---
# <a name="pricing-and-associated-costs"></a>Preços e custos associados

Este artigo explica o modelo de preços de IoT do Azure Security Center, resume todos os custos associados e explica como gerenciá-los.

## <a name="pricing"></a>Preços

O azure Security Center para o modelo de preços de IoT é composto por duas partes, e é cobrado assim que um Hub ioT é [habilitado](quickstart-onboard-iot-hub.md) no Azure Security Center para IoT:

- Custo por dispositivo - recursos de segurança incorporados com base na análise de logs do IoT Hub.

- Custo por mensagem - recursos de segurança aprimorados com base em mensagens de segurança de dispositivos IoT Edge ou leaf.

Para obter mais informações, consulte [os preços do Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Custos associados

O Azure Security Center for IoT tem custos associados, que não fazem parte do preço direto:

- Custos de armazenamento do Log Analytics

Você pode reduzir os custos associados optando por determinados recursos de solução. Opte por alterar alterando suas configurações.

Para alterar suas configurações:

1. Abra o IoT Hub.

1. Em **Segurança,** clique **em Visão geral**.

1. Clique em **Configurações**.

A tabela a seguir fornece um resumo dos custos e implicações associados de cada opção.

|     | Uso | Comentário |
| --- | --- | --- |
| **Armazenamento de análise de log** |  |
| Recomendação e alertas de dispositivos| Recomendação de segurança e alertas gerados pelo serviço | Não é opcional |
| Dados de segurança brutos| Dados de segurança brutos de dispositivos IoT, coletados por agentes de segurança | Desativar _eventos de segurança do dispositivo bruto da loja_ |
|

>[!Important]
> A exclusão tem sérias implicações no Azure Security Center para disponibilidade de recursos de segurança ioT.

| Optar por sair | Implicações |
| --- | --- |
| _Coleta de metadados gêmeos_ | Desativar [alertas personalizados](quickstart-create-custom-alerts.md) |
| | Desativar recomendações de manifesto ioT edge |
| | Desativar recomendações e alertas baseados em identidade do dispositivo |
| _Armazenar eventos de segurança de dispositivos brutos_ | Detalhes sobre as recomendações da linha de base do sistema operacional do dispositivo não estão disponíveis |
| | Detalhes sobre [investigações de alerta](concept-security-alerts.md) e [recomendação](concept-recommendations.md) não estão disponíveis |
|

## <a name="see-also"></a>Confira também

- Acesse seus [dados de segurança brutos](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Entenda e explore [recomendações de segurança](concept-recommendations.md)
- Entenda e explore [alertas de segurança](concept-security-alerts.md)
