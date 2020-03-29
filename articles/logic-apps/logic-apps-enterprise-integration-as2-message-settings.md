---
title: Configurações de mensagem AS2
description: Guia de referência para as configurações de envio e recebimento de AS2 em Aplicativos de Lógica Do Azure com o Pacote de Integração Corporativa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793037"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referência para configurações de mensagens AS2 em aplicativos azure logic com pacote de integração corporativa

Esta referência descreve as propriedades que você pode definir para especificar como um contrato AS2 lida com mensagens enviadas e recebidas entre parceiros comerciais. Configure essas propriedades com base no seu acordo com o parceiro que troca mensagens com você.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>configurações de recebimento AS2

![Selecione "Receber configurações"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Substituir as propriedades de mensagem** | Não | Substitui as propriedades nas mensagens recebidas com as configurações da propriedade. |
| **Mensagem deve ser assinada** | Não | Especifica se todas as mensagens recebidas devem ser assinadas digitalmente. Se você precisar assinar, na lista **de certificados,** selecione um certificado público de parceiro convidado existente para validar a assinatura nas mensagens. Se você não tem um certificado, saiba mais sobre [como adicionar certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Mensagem deve ser criptografada** | Não | Especifica se todas as mensagens recebidas devem ser criptografadas. Mensagens não criptografadas são rejeitadas. Se você precisar de criptografia, na lista **de certificados,** selecione um certificado privado de parceiro host existente para descriptografar mensagens recebidas. Se você não tem um certificado, saiba mais sobre [como adicionar certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Mensagem deve ser compactada** | Não | Especifica se todas as mensagens recebidas devem ser compactadas. Mensagens não compactadas são rejeitadas. |
| **Proibir duplicatas de ID de mensagem** | Não | Especifica se permite mensagens com IDs duplicados. Se você não permitir ids duplicados, selecione o número de dias entre as verificações. Você também pode escolher se deve suspender as duplicatas. |
| **Texto MDN** | Não | Especifica a notificação padrão de eliminação de mensagem (MDN) que você deseja enviar ao remetente da mensagem. |
| **Enviar MDN** | Não | Especifica se deve enviar MDNs síncronos para mensagens recebidas.  |
| **Enviar MDN assinada** | Não | Especifica se deve enviar MDNs assinados para mensagens recebidas. Se você precisar assinar, a partir da lista **MIC Algorithm,** selecione o algoritmo a ser usado para assinar mensagens. |
| **Enviar MDN assíncrona** | Não | Especifica se deve enviar MDNs de forma assíncrona. Se você selecionar MDNs assíncronos, na caixa **URL,** especifique a URL para onde enviar os MDNs. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Configurações de envio AS2

![Selecione "Enviar configurações"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Habilitar a assinatura de mensagens** | Não | Especifica se todas as mensagens de saída devem ser assinadas digitalmente. Se você precisar de assinatura, selecione esses valores: <p>- Na lista **algoritmo de assinatura,** selecione o algoritmo a ser usado para assinar mensagens. <br>- Na lista **de certificados,** selecione um certificado privado de parceiro host existente para assinar mensagens. Se você não tem um certificado, saiba mais sobre [como adicionar certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Habilitar a criptografia de mensagem** | Não | Especifica se todas as mensagens de saída devem ser criptografadas. Se você precisar de criptografia, selecione esses valores: <p>- Na lista **algoritmo de criptografia,** selecione o algoritmo de certificado público de parceiro convidado para usar para criptografar mensagens. <br>- Na lista **de certificados,** selecione um certificado privado de parceiro convidado existente para criptografar mensagens de saída. Se você não tem um certificado, saiba mais sobre [como adicionar certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Habilitar a compactação de mensagem** | Não | Especifica se todas as mensagens de saída devem ser compactadas. |
| **Desdobrar cabeçalhos HTTP** | Não | Coloca o `content-type` cabeçalho HTTP em uma única linha. |
| **Transmitir nome do arquivo no cabeçalho MIME** | Não | Especifica se deve incluir o nome do arquivo no cabeçalho MIME. |
| **Solicitar MDN** | Não | Especifica se deve receber notificações de eliminação de mensagens (MDNs) para todas as mensagens de saída. |
| **Solicitar MDN assinada** | Não | Especifica se deve receber MDNs assinados para todas as mensagens de saída. Se você precisar assinar, a partir da lista **MIC Algorithm,** selecione o algoritmo a ser usado para assinar mensagens. |
| **Solicitar MDN assíncrona** | Não | Especifica se deve receber MDNs de forma assíncrona. Se você selecionar MDNs assíncronos, na caixa **URL,** especifique a URL para onde enviar os MDNs. |
| **Habilitar NRR** | Não | Especifica se deve exigir o recibo de não-repúdio (NRR). Este atributo de comunicação fornece evidências de que os dados foram recebidos conforme abordado. |
| **Formato de algoritmo SHA2** | Não | Especifica o formato de algoritmo MIC para usar para assinar nos cabeçalhos para as mensagens AS2 ou MDN de saída |
||||

## <a name="next-steps"></a>Próximas etapas

[Trocar mensagens AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
