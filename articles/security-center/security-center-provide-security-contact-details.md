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
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: aa35d1325e339af515c8bfc052d1af524b464e09
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91446019"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Configurar notificações por email para alertas de segurança 

Para garantir que as pessoas certas na sua organização sejam notificadas sobre alertas de segurança no ambiente, insira os endereços de email delas na página de configurações **Notificações por email**.

Ao configurar suas notificações, você pode configurar os emails a serem enviados a indivíduos específicos ou a qualquer pessoa com uma função específica do Azure em uma assinatura. 

Para evitar o excesso de alertas, a Central de Segurança limita o volume de emails enviados. Para cada assinatura, a Central de Segurança envia:

- um máximo de **quatro** emails por dia para alertas de **alta severidade**
- um máximo de **dois** emails por dia para alertas de **severidade média**
- um máximo de **um** email por dia para alertas de **baixa severidade**


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Como configurar os detalhes do contato que receberá os emails sobre os alertas de segurança." :::

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Preço:|Gratuita|
|Funções e permissões necessárias:|**Administrador de Segurança**<br>**Proprietário da assinatura** |
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) US Gov (parcial)<br>![Não](./media/icons/no-icon.png) China Gov, outros Gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>Configurar notificações por email para alertas <a name="email"></a>

Você pode enviar notificações por email a indivíduos ou a todos os usuários com funções específicas do Azure.

1. Na área **Preços e configurações** da Central de Segurança, escolha a assinatura relevante e selecione **Notificações por email**.

1. Defina os destinatários das notificações:

    - Na lista suspensa, selecione uma das funções disponíveis.
    - E/ou insira endereços de email específicos separados por vírgulas. Não há um limite para o número de endereços de email que podem ser inseridos.

1. Para aplicar as informações de contato de segurança à sua assinatura, selecione **Salvar**.


## <a name="see-also"></a>Confira também
Para saber mais sobre alertas de segurança, confira o seguinte:

* [Alertas de segurança – um guia de referência](alerts-reference.md): saiba mais sobre os alertas de segurança que você poderá ver no módulo Proteção contra Ameaças da Central de Segurança do Azure
* [Gerenciar alertas de segurança e responder a eles na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerenciar alertas de segurança e responder a eles
* [Automação de fluxo de trabalho](workflow-automation.md): automatizar respostas para alertas com a lógica de notificação personalizada