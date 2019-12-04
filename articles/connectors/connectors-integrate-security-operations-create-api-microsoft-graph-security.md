---
title: Integrar e gerenciar as operações de segurança & segurança de Microsoft Graph
description: Melhorar a proteção contra ameaças do seu aplicativo, a detecção e a resposta com o Microsoft Graph Security & aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 7e9cc2d8d38af7e5e6cf26ccc3659ee58ef17e59
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789045"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Melhore a proteção contra ameaças integrando as operações de segurança com a Segurança do Microsoft Graph e os Aplicativos Lógicos do Azure

Com os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector da [Segurança do Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview), você pode melhorar o modo como seu aplicativo detecta, protege e responde às ameaças criando fluxos de trabalho automatizados para a integração de parceiros, serviços e produtos de segurança Microsoft. Por exemplo, você pode criar [Guias estratégicos de Central de Segurança do Azure](../security-center/security-center-playbooks.md) que monitoram e gerenciam entidades de segurança do Microsoft Graph, tais como alertas. Aqui estão alguns cenários compatíveis com o conector da Segurança do Microsoft Graph:

* Obtenha alertas com base em consultas ou pela ID do alerta. Por exemplo, você pode obter uma lista que inclui alertas de severidade alta.
* Atualize os alertas. Por exemplo, você pode atualizar atribuições de alertas, adicionar comentários a alertas ou marcar alertas.
* Monitore quando os alertas são criados ou alterados, criando [Assinaturas de alertas (webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Gerencie suas assinaturas de alertas. Por exemplo, você pode obter assinaturas ativas, estender o tempo de expiração para uma assinatura ou excluir assinaturas.

O fluxo de trabalho do aplicativo lógico pode usar ações que obtêm as respostas do conector da Segurança do Microsoft Graph e disponibilizam essa saída para outras ações no fluxo de trabalho. Você também pode fazer com que outras ações no fluxo de trabalho usem a saída das ações de conector da Segurança do Microsoft Graph. Por exemplo, se receber alertas de severidade alta por meio do conector da Segurança do Microsoft Graph, você poderá enviar os alertas em uma mensagem de email usando o conector do Outlook. 

Para saber mais sobre a Segurança do Microsoft Graph, confira a [Visão geral da API de Segurança do Microsoft Graph](https://aka.ms/graphsecuritydocs). Se ainda não estiver familiarizado com aplicativos lógicos, leia [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md). Se você estiver procurando pelo Microsoft Flow ou o PowerApps, consulte [O que é o Flow?](https://flow.microsoft.com/) ou [O que é o PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Para usar o conector da Segurança do Microsoft Graph, você precisa ter *dado explicitamente* seu consentimento de administrador do locatário do Azure AD (Active Directory), o que faz parte dos [requisitos de autenticação da Segurança do Microsoft Graph](https://aka.ms/graphsecurityauth). Esse consentimento requer o nome e a ID do aplicativo do conector da Segurança do Microsoft Graph, que você também pode encontrar no [portal do Azure](https://portal.azure.com):

   | Propriedade | Value |
   |----------|-------|
   | **Nome do Aplicativo** | `MicrosoftGraphSecurityConnector` |
   | **ID do Aplicativo** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Para dar consentimento ao conector, seu administrador de locatário do Azure AD pode seguir uma das seguintes etapas:

   * [Conceder consentimento do administrador de locatário para aplicativos do Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

   * Durante a primeira execução do aplicativo lógico, seu aplicativo pode solicitar consentimento do administrador do locatário do Azure AD por meio de [experiência de consentimento de aplicativo](../active-directory/develop/application-consent-experience.md).
   
* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar suas entidades de Segurança do Microsoft Graph, tais como alertas. Atualmente, esse conector não tem gatilhos. Assim, para usar uma ação da Segurança do Microsoft Graph, inicie seu aplicativo lógico com um gatilho, por exemplo, o gatilho **Recorrência**.

## <a name="connect-to-microsoft-graph-security"></a>Conectar à Segurança do Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com/) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, adicione o gatilho e quaisquer outras ações que você desejar antes de adicionar uma ação da Segurança do Microsoft Graph.

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que deseja adicionar uma ação da Segurança do Microsoft Graph, escolha **Nova etapa**.

   -ou-

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de mais (+) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "segurança do microsoft graph" como o filtro. Na lista de ações, selecione a ação desejada.

1. Entre com suas credenciais da Segurança do Microsoft Graph.

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="add-actions"></a>Adicionar ações

Aqui estão detalhes mais específicos sobre como usar as diversas ações disponíveis com o conector da Segurança do Microsoft Graph.

### <a name="manage-alerts"></a>Gerenciar alertas

Para filtrar, classificar ou obter os resultados mais recentes, forneça *somente* os [parâmetros de consulta ODATA compatíveis com o Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Não especifique* a URL base completa ou a ação HTTP, por exemplo, `https://graph.microsoft.com/v1.0/security/alerts`, ou a operação `GET` ou `PATCH`. Aqui está um exemplo específico que mostra os parâmetros para uma ação **Obter alertas** quando você quer uma lista com os alertas de severidade alta:

`Filter alerts value as Severity eq 'high'`

Para obter mais informações sobre as consultas que você pode usar com esse conector, confira a [documentação de referência de alertas da Segurança do Microsoft Graph](https://docs.microsoft.com/graph/api/alert-list). Para criar experiências aprimoradas com esse conector, saiba mais sobre os [alertas de propriedades de esquema](https://docs.microsoft.com/graph/api/resources/alert) compatíveis com o conector.

| Ação | Descrição |
|--------|-------------|
| **Obter alertas** | Obter alertas filtrados com base em uma ou mais [propriedades de alerta](https://docs.microsoft.com/graph/api/resources/alert), por exemplo: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Obter alerta por ID** | Obter um alerta específico com base na ID do alerta. | 
| **Atualizar alerta** | Atualizar um alerta específico com base na ID do alerta. <p>Para assegurar que você passe as propriedades necessárias e editáveis na sua solicitação, confira as [propriedades editáveis para alertas](https://docs.microsoft.com/graph/api/alert-update). Por exemplo, para atribuir um alerta para um analista de segurança para que ele possa investigar, você pode atualizar a propriedade **Atribuído a** do alerta. |
|||

### <a name="manage-alert-subscriptions"></a>Gerenciar assinaturas de alertas

O Microsoft Graph dá suporte a [*inscrições*](https://docs.microsoft.com/graph/api/resources/subscription) ou [*webhooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Para obter, atualizar ou excluir assinaturas, forneça os [parâmetros de consulta ODATA compatíveis com o Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) para o constructo da entidade do Microsoft Graph e inclua `security/alerts`, seguido da consulta ODATA. 
*Não inclua* a URL base, por exemplo, `https://graph.microsoft.com/v1.0`. Em vez disso, use o formato neste exemplo:

`security/alerts?$filter=status eq 'New'`

| Ação | Descrição |
|--------|-------------|
| **Criar assinaturas** | [Crie uma assinatura](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) que notifique você sobre quaisquer alterações. Você pode filtrar essa assinatura para os tipos de alertas específicos que você deseja. Por exemplo, você pode criar uma assinatura que notifica você sobre alertas de severidade alta. |
| **Obter assinaturas ativas** | [Obtenha assinaturas não expiradas](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Atualizar assinatura** | [Atualize uma assinatura](https://docs.microsoft.com/graph/api/subscription-update) fornecendo a respectiva ID. Por exemplo, para estender sua assinatura, você pode atualizar a respectiva propriedade `expirationDateTime`. | 
| **Excluir assinatura** | [Exclua uma assinatura](https://docs.microsoft.com/graph/api/subscription-delete) fornecendo a ID da assinatura. | 
||| 

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](https://aka.ms/graphsecurityconnectorreference) do conector.

## <a name="get-support"></a>Obter suporte

Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
