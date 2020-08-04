---
title: Fornecer detalhes de contato de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como fornecer detalhes de contato de segurança na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 5a2410a5ccaa10867e94b3a2ec10228a006fbe20
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534627"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Configurar notificações por email para alertas de segurança 

Para garantir que as pessoas certas em sua organização sejam notificadas sobre alertas de segurança em seu ambiente, insira seus endereços de email na página de configurações de **notificações de email** .

Ao configurar suas notificações, você pode configurar os emails a serem enviados a indivíduos específicos ou a qualquer pessoa com uma função específica do Azure para uma assinatura. 

Para evitar o alerta fadiga, a central de segurança limita o volume de emails enviados. Para cada assinatura, a central de segurança envia:

- um máximo de **quatro** emails por dia para alertas de **alta severidade**
- um máximo de **dois** emails por dia para alertas de **severidade média**
- um máximo de **um** email por dia para alertas de **baixa severidade**

## <a name="availability"></a>Disponibilidade

- Estado da versão: **Disponível para o público geral**
- Funções necessárias: **administrador de segurança** ou **proprietário da assinatura** 
- Nuvens: ✔ nuvens comerciais ✔ US Gov (parcial) ✘ National/soberanas (China gov, outros gov)


## <a name="set-up-email-notifications-for-alerts"></a>Configurar notificações por email para alertas<a name="email"></a>

Você pode enviar notificações por email para indivíduos ou para todos os usuários com funções específicas do Azure.

1. Na área de **configurações & de preços** da central de segurança, na assinatura relevante e selecione **notificações por email**.

1. Defina os destinatários para suas notificações:

    - Na lista suspensa, selecione entre as funções disponíveis.
    - E/ou insira endereços de email específicos separados por vírgulas. Não há nenhum limite para o número de endereços de email que você pode inserir.

1. Para aplicar as informações de contato de segurança à sua assinatura, selecione **salvar**.


## <a name="see-also"></a>Consulte também
Para saber mais sobre alertas de segurança, consulte o seguinte:

* [Alertas de segurança-um guia de referência](alerts-reference.md) – saiba mais sobre os alertas de segurança que você pode ver no módulo de proteção contra ameaças da central de segurança do Azure
* [Gerenciar e responder a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md) – saiba como gerenciar e responder a alertas de segurança