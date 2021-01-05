---
title: Preços e custos associados
description: Saiba mais sobre os custos associados ao defender para IoT e como controlá-los.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2020
ms.author: shhazam
ms.openlocfilehash: a97bcbf5ba47289a2e68b0eaa587ea39d7fb705a
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832345"
---
# <a name="pricing-and-associated-costs"></a>Preços e custos associados

Este artigo explica o modelo de preços do defender para IoT, resume todos os custos associados e explica como gerenciá-los.

## <a name="pricing"></a>Preços

O modelo de preços do defender for IoT é composto por duas partes e é cobrado quando um hub IoT é [habilitado](quickstart-onboard-iot-hub.md) no defender para IOT:

- Custo por recursos de segurança internos do dispositivo com base na análise de logs do Hub IoT.

- Custo por recursos de segurança aprimorados por mensagem com base em mensagens de segurança de dispositivos IoT Edge ou folha.

Para obter mais informações, consulte [preços da central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Custos associados

O defender para IoT tem custos associados, que não fazem parte do preço direto:

- Custos de armazenamento Log Analytics

Você pode reduzir os custos associados ao optar por determinados recursos da solução. Cancele alterando suas configurações.

Para alterar suas configurações:

1. Abra o Hub IoT.

1. Em **segurança**, clique em **configurações**.

1. Clique em **coleta de dados**.

A tabela a seguir fornece um resumo dos custos associados e as implicações de cada opção.

| Opção | Uso | Comentário |
| --- | --- | --- |
| **Armazenamento Log Analytics** |  |
| Alertas e recomendação de dispositivo| Recomendação e alertas de segurança gerados pelo serviço | Não opcional |
| Dados brutos de segurança| Dados brutos de segurança de dispositivos IoT, coletados por agentes de segurança | Desabilitar _eventos de segurança de dispositivo bruto de armazenamento_ |
|

>[!Important]
> A recusa tem sérias implicações no defender para a disponibilidade do recurso de segurança de IoT.

| Recusar | Implicações |
| --- | --- |
| _Coleção de metadados de entrelaçamento_ | Desabilitar [alertas personalizados](quickstart-create-custom-alerts.md) |
| | Desabilitar IoT Edge recomendações de manifesto |
| | Desabilitar alertas e recomendações com base em identidade do dispositivo |
| _Armazenar eventos de segurança de dispositivo bruto_ | OS detalhes sobre as recomendações de linha de base do dispositivo não estão disponíveis |
| | Detalhes sobre investigações de [recomendação](concept-recommendations.md) e [alerta](concept-security-alerts.md) não estão disponíveis |
|

## <a name="see-also"></a>Consulte também

- Acessar seus [dados brutos de segurança](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Entender e explorar as [recomendações de segurança](concept-recommendations.md)
- Entender e explorar [alertas de segurança](concept-security-alerts.md)
