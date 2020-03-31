---
title: Integre e gerencie operações de segurança & Microsoft Graph Security
description: Melhore a proteção, detecção e resposta do seu aplicativo com o Microsoft Graph Security & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598826"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Melhore a proteção contra ameaças integrando as operações de segurança com a Segurança do Microsoft Graph e os Aplicativos Lógicos do Azure

Com os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector da [Segurança do Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview), você pode melhorar o modo como seu aplicativo detecta, protege e responde às ameaças criando fluxos de trabalho automatizados para a integração de parceiros, serviços e produtos de segurança Microsoft. Por exemplo, você pode criar [Guias estratégicos de Central de Segurança do Azure](../security-center/security-center-playbooks.md) que monitoram e gerenciam entidades de segurança do Microsoft Graph, tais como alertas. Aqui estão alguns cenários que são suportados pelo conector Microsoft Graph Security:

* Obtenha alertas com base em consultas ou pela ID do alerta. Por exemplo, você pode obter uma lista que inclui alertas de severidade alta.

* Atualize os alertas. Por exemplo, você pode atualizar atribuições de alertas, adicionar comentários a alertas ou marcar alertas.

* Monitore quando os alertas são criados ou alterados, criando [Assinaturas de alertas (webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).

* Gerencie suas assinaturas de alertas. Por exemplo, você pode obter assinaturas ativas, estender o tempo de expiração para uma assinatura ou excluir assinaturas.

O fluxo de trabalho do aplicativo lógico pode usar ações que obtêm as respostas do conector da Segurança do Microsoft Graph e disponibilizam essa saída para outras ações no fluxo de trabalho. Você também pode fazer com que outras ações no fluxo de trabalho usem a saída das ações de conector da Segurança do Microsoft Graph. Por exemplo, se receber alertas de severidade alta por meio do conector da Segurança do Microsoft Graph, você poderá enviar os alertas em uma mensagem de email usando o conector do Outlook. 

Para saber mais sobre a Segurança do Microsoft Graph, confira a [Visão geral da API de Segurança do Microsoft Graph](https://aka.ms/graphsecuritydocs). Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Se você está procurando o Microsoft Flow ou powerApps, veja [o que é flow?](https://flow.microsoft.com/) [What is PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Para usar o conector da Segurança do Microsoft Graph, você precisa ter *dado explicitamente* seu consentimento de administrador do locatário do Azure AD (Active Directory), o que faz parte dos [requisitos de autenticação da Segurança do Microsoft Graph](https://aka.ms/graphsecurityauth). Esse consentimento requer o nome e a ID do aplicativo do conector da Segurança do Microsoft Graph, que você também pode encontrar no [portal do Azure](https://portal.azure.com):

  | Propriedade | Valor |
  |----------|-------|
  | **Nome do aplicativo** | `MicrosoftGraphSecurityConnector` |
  | **ID de aplicação** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Para conceder consentimento para o conector, o administrador do inquilino Azure AD pode seguir essas etapas:

  * [Conceder consentimento do administrador de locatário para aplicativos do Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

  * Durante a primeira execução do aplicativo lógico, seu aplicativo pode solicitar consentimento do administrador do locatário do Azure AD por meio de [experiência de consentimento de aplicativo](../active-directory/develop/application-consent-experience.md).
   
* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar suas entidades de Segurança do Microsoft Graph, tais como alertas. Para usar um gatilho microsoft graph security, você precisa de um aplicativo de lógica em branco. Para usar uma ação de segurança do Gráfico microsoft, você precisa de um aplicativo lógico que comece com o gatilho apropriado para o seu cenário.

## <a name="connect-to-microsoft-graph-security"></a>Conectar à Segurança do Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com/) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos de lógica em branco, adicione o gatilho e quaisquer outras ações que você deseja antes de adicionar uma ação de segurança do Gráfico do Microsoft.

   -ou-

   Para aplicativos lógicos existentes, a última etapa em que você deseja adicionar uma ação de segurança do Gráfico do Microsoft, selecione **Nova etapa**.

   -ou-

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição (+) que aparece e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, insira "segurança do microsoft graph" como o filtro. Na lista de ações, selecione a ação desejada.

1. Entre com suas credenciais da Segurança do Microsoft Graph.

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="add-triggers"></a>Adicionar gatilhos

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo Logic Apps cria uma instância lógica do aplicativo e começa a executar o fluxo de trabalho do seu aplicativo.

> [!NOTE] 
> Quando um gatilho dispara, o gatilho processa todos os novos alertas. Se nenhum alerta for recebido, o gatilho será ignorado. A próxima pesquisa de gatilhos acontecerá com base no intervalo de recorrência que você especificar nas propriedades do gatilho.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico quando novos alertas são enviados para o seu aplicativo.

1.  No portal Azure ou Visual Studio, crie um aplicativo de lógica em branco, que abre o Logic App Designer. Este exemplo usa o portal do Azure.

1.  No designer, na caixa de pesquisa, digite "microsoft graph security" como seu filtro. Na lista de gatilhos, selecione este gatilho: **Em todos os novos alertas**

1.  No gatilho, forneça informações sobre os alertas que você deseja monitorar. Para obter mais propriedades, abra a lista **Adicionar novo parâmetro** e selecione um parâmetro para adicionar essa propriedade ao gatilho.

   | Propriedade | Property (JSON) | Obrigatório | Type | Descrição |
   |----------|-----------------|----------|------|-------------|
   | **Intervalo** | `interval` | Sim | Integer | Um inteiro positivo que descreve a frequência na qual o fluxo de trabalho é executado com base na frequência. Aqui estão os intervalos mínimos e máximos: <p><p>– Mês: 1 a 16 meses <br>–Dia: 1 a 500 dias <br>– Hora: 1 a 12.000 horas <br>– Minuto: 1 a 72.000 minutos <br>– Segundo: 1 a 9.999.999 segundos <p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. |
   | **Freqüência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo**, **Minuto**, **Hora**, **Dia**, **Semana** ou **Mês** |
   | **Fuso horário** | `timeZone` | Não | String | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que você deseja aplicar. |
   | **Horário de início** | `startTime` | Não | String | Forneça uma data e hora de início neste formato: <p><p>AAAA-MM-DDThh:mm:ss se você selecionar um fuso horário <p>-ou- <p>AAAA-MM-DDThh:mm:ssZ se você não selecionar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 14:00, então especifique "2017-09-18T14:00:00" e selecione um fuso horário como o Horário Padrão do Pacífico. Ou, especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** Este tempo de início tem um máximo de 49 anos no futuro e deve seguir a [especificação de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem uma [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não selecionar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto que, para agendamentos complexos, o gatilho não é disparado antes da hora de início. [*Quais são as maneiras que posso usar a data e hora de início?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Quando terminar, na barra de ferramentas do designer, selecione **Salvar**.

1.  Agora, continue a adicionar uma ou mais ações ao aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

## <a name="add-actions"></a>Adicionar ações

Aqui estão detalhes mais específicos sobre como usar as diversas ações disponíveis com o conector da Segurança do Microsoft Graph.

### <a name="manage-alerts"></a>Gerenciar alertas

Para filtrar, classificar ou obter os resultados mais recentes, forneça *somente* os [parâmetros de consulta ODATA compatíveis com o Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Não especifique* a URL base completa ou a ação HTTP, por exemplo, `https://graph.microsoft.com/v1.0/security/alerts`, ou a operação `GET` ou `PATCH`. Aqui está um exemplo específico que mostra os parâmetros para uma ação **Obter alertas** quando você quer uma lista com os alertas de severidade alta:

`Filter alerts value as Severity eq 'high'`

Para obter mais informações sobre as consultas que você pode usar com esse conector, confira a [documentação de referência de alertas da Segurança do Microsoft Graph](https://docs.microsoft.com/graph/api/alert-list). Para criar experiências aprimoradas com esse conector, saiba mais sobre os [alertas de propriedades de esquema](https://docs.microsoft.com/graph/api/resources/alert) compatíveis com o conector.

| Ação | Descrição |
|--------|-------------|
| **Obter alertas** | Obter alertas filtrados com base em uma `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`ou mais propriedades de [alerta,](https://docs.microsoft.com/graph/api/resources/alert)por exemplo, . | 
| **Obter alerta por ID** | Obter um alerta específico com base na ID do alerta. | 
| **Atualizar alerta** | Atualizar um alerta específico com base na ID do alerta. Para assegurar que você passe as propriedades necessárias e editáveis na sua solicitação, confira as [propriedades editáveis para alertas](https://docs.microsoft.com/graph/api/alert-update). Por exemplo, para atribuir um alerta para um analista de segurança para que ele possa investigar, você pode atualizar a propriedade **Atribuído a** do alerta. |
|||

### <a name="manage-alert-subscriptions"></a>Gerenciar assinaturas de alertas

O Microsoft Graph dá suporte a [*inscrições*](https://docs.microsoft.com/graph/api/resources/subscription) ou [*webhooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Para obter, atualizar ou excluir assinaturas, forneça os [parâmetros de consulta ODATA compatíveis com o Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) para o constructo da entidade do Microsoft Graph e inclua `security/alerts`, seguido da consulta ODATA. *Não inclua* a URL base, por exemplo, `https://graph.microsoft.com/v1.0`. Em vez disso, use o formato neste exemplo:

`security/alerts?$filter=status eq 'New'`

| Ação | Descrição |
|--------|-------------|
| **Criar assinaturas** | [Crie uma assinatura](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) que notifique você sobre quaisquer alterações. Você pode filtrar essa assinatura para os tipos de alertas específicos que você deseja. Por exemplo, você pode criar uma assinatura que notifica você sobre alertas de severidade alta. |
| **Obter assinaturas ativas** | [Obtenha assinaturas não expiradas](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Atualizar assinatura** | [Atualize uma assinatura](https://docs.microsoft.com/graph/api/subscription-update) fornecendo a respectiva ID. Por exemplo, para estender sua assinatura, você pode atualizar a respectiva propriedade `expirationDateTime`. | 
| **Excluir assinatura** | [Exclua uma assinatura](https://docs.microsoft.com/graph/api/subscription-delete) fornecendo a ID da assinatura. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Gerenciar indicadores de inteligência de ameaças

Para filtrar, classificar ou obter os resultados mais recentes, forneça *somente* os [parâmetros de consulta ODATA compatíveis com o Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Não especifique* a URL base completa ou a ação HTTP, por exemplo, `https://graph.microsoft.com/beta/security/tiIndicators`, ou a operação `GET` ou `PATCH`. Aqui está um exemplo específico que mostra os parâmetros para uma ação `DDoS` **Get tiIndicators** quando você deseja uma lista que tenha o tipo de ameaça:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Para obter mais informações sobre as consultas que você pode usar com este conector, consulte ["Parâmetros de consulta opcional" na documentação de referência do indicador de inteligência de ameaças do Microsoft Graph Security](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http). Para construir experiências aprimoradas com este conector, saiba mais sobre o [indicador de inteligência de ameaças de propriedades de esquema](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta) que o conector suporta.

| Ação | Descrição |
|--------|-------------|
| **Obtenha indicadores de inteligência de ameaças** | Obter tiIndicators filtrados com base em uma ou mais [propriedades tiIndicator,](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta)por exemplo,`threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Obtenha indicador de inteligência de ameaças por ID** | Obtenha um tiIndicator específico com base no ID tiIndicator. | 
| **Criar indicador de inteligência de ameaças** | Crie um novo tiIndicator postando na coleção tiIndicators. Para ter certeza de que você passa as propriedades necessárias em sua solicitação, consulte as [propriedades necessárias para criar o tiIndicator](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http). |
| **Envie vários indicadores de inteligência de ameaças** | Crie vários novos indicadores ao postar uma coleção de tiIndicators. Para ter certeza de que você passa as propriedades necessárias em sua solicitação, consulte as [propriedades necessárias para enviar vários tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http). |
| **Atualizar indicador de inteligência de ameaças** | Atualize um tiIndicator específico com base no ID tiIndicator. Para garantir que você passe as propriedades necessárias e editáveis em sua solicitação, consulte as [propriedades editáveis para tiIndicator](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http). Por exemplo, para atualizar a ação para aplicar se o indicador for correspondido de dentro da ferramenta de segurança targetProduct, você pode atualizar a propriedade de **ação** do tiIndicator. |
| **Atualizar múltiplos indicadores de inteligência de ameaças** | Atualize vários tiIndicators. Para garantir que você passe as propriedades necessárias em sua solicitação, consulte as [propriedades necessárias para atualizar vários tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http). |
| **Exclua o indicador de inteligência de ameaças por ID** | Exclua um tiIndicator específico com base no ID tiIndicator. |
| **Exclua vários indicadores de inteligência de ameaças por IDs** | Exclua vários indicadores por seus IDs. Para ter certeza de que você passa as propriedades necessárias em sua solicitação, consulte as [propriedades necessárias para excluir vários tiIndicators por IDs](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http). |
| **Exclua vários indicadores de inteligência de ameaças por IDs externos** | Exclua vários tiIndicators pelos IDs externos. Para garantir que você passe as propriedades necessárias em sua solicitação, consulte as [propriedades necessárias para excluir vários tiIndicators por IDs externos](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http). |
|||

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](https://aka.ms/graphsecurityconnectorreference) do conector.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
