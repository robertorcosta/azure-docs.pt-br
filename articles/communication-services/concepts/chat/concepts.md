---
title: Conceitos de chat nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de chat dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 1dd3781b18f82a96f388b0e619ce62b45752a870
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292465"
---
# <a name="chat-concepts"></a>Conceitos de chat

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

As bibliotecas de clientes de chat dos Serviços de Comunicação do Azure podem ser usadas para adicionar o bate-papo com texto em tempo real aos seus aplicativos. Esta página resume os principais conceitos e funcionalidades de chat.

Confira a [Visão geral da biblioteca de clientes de chat dos Serviços de Comunicação](./sdk-features.md) para saber mais sobre as funcionalidades e as linguagens específicas da biblioteca de clientes.

## <a name="chat-overview"></a>Visão geral do chat 

As conversas de chat acontecem em grupos de mensagens de chat. Uma conversa de chat pode conter muitas mensagens e muitos usuários. Cada mensagem pertence a uma só conversa, e um usuário pode fazer parte de uma ou várias conversas. 

Cada usuário na conversa de chat é chamado de membro. É possível ter até 250 membros em uma conversa de chat. Somente os membros da conversa podem enviar e receber mensagens ou adicionar/remover membros em uma conversa de chat. O tamanho máximo permitido da mensagem é de aproximadamente 28 KB. Recupere todas as mensagens em uma conversa de chat usando a operação `List/Get Messages`. Os Serviços de Comunicação armazenam o histórico de chat até que você execute uma operação de exclusão no thread ou na mensagem do chat ou até que não haja membros restantes no thread de chat, quando ele se torna órfão e é processado para exclusão.   

Para conversas de chat com mais de 20 membros, as confirmações de leitura e os recursos do indicador de digitação ficam desabilitados. 

## <a name="chat-architecture"></a>Arquitetura de chat

Há duas partes principais na arquitetura de chat: 1) Serviço confiável e 2) Aplicativo cliente.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagrama mostrando a arquitetura de chat dos Serviços de Comunicação.":::

 - **Serviço confiável:** para gerenciar corretamente uma sessão de chat, você precisará de um serviço que ajude você a se conectar aos Serviços de Comunicação usando a cadeia de conexão do recurso. Esse serviço é responsável por criar conversas de chat, gerenciar as associações de conversa e fornecer tokens de acesso aos usuários. Encontre mais informações sobre tokens de acesso em nosso guia de início rápido sobre [tokens de acesso](../../quickstarts/access-tokens.md).

 - **Aplicativo cliente:**  o aplicativo cliente se conecta ao seu serviço confiável e recebe os tokens de acesso usados para se conectar diretamente aos Serviços de Comunicação. Depois que essa conexão é feita, o aplicativo cliente pode enviar e receber mensagens.
    
## <a name="message-types"></a>Tipos de Mensagem

O chat dos Serviços de Comunicação compartilha as mensagens geradas pelo usuário, bem como as mensagens geradas pelo sistema, chamadas **atividades de conversa**. As atividades de conversa são geradas quando uma conversa de chat é atualizada. Quando você chamar `List Messages` ou `Get Messages` em uma conversa de chat, o resultado conterá as mensagens de texto geradas pelo usuário, bem como as mensagens do sistema em ordem cronológica. Isso ajuda você a identificar quando um membro foi adicionado ou removido ou quando o tópico da conversa de chat foi atualizado. Os tipos de mensagem compatíveis são:  

 - `Text`: mensagem real redigida e enviada pelo usuário como parte da conversa de chat. 
 - `ThreadActivity/AddMember`: mensagem do sistema que indica que um ou mais membros foram adicionados à conversa de chat. Por exemplo:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: mensagem do sistema que indica que um membro foi removido da conversa de chat. Por exemplo:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`: mensagem do sistema que indica que o tópico foi atualizado. Por exemplo:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>Sinalização em tempo real 

A biblioteca de clientes de chat JavaScript inclui a sinalização em tempo real. Isso permite que os clientes escutem atualizações em tempo real e mensagens de entrada para uma conversa de chat sem precisar sondar as APIs. Os eventos disponíveis incluem:

 - `ChatMessageReceived`: quando uma nova mensagem é enviada a uma conversa de chat da qual o usuário é membro. Esse evento não é enviado para as mensagens do sistema geradas automaticamente discutidas no tópico anterior.  
 - `ChatMessageEdited`: quando uma mensagem é editada em uma conversa de chat da qual o usuário é membro. 
 - `ChatMessageDeleted`: quando uma mensagem é excluída em uma conversa de chat da qual o usuário é membro. 
 - `TypingIndicatorReceived`: quando outro membro está digitando uma mensagem em uma conversa de chat da qual o usuário é membro. 
 - `ReadReceiptReceived`: quando outro membro leu a mensagem enviada pelo usuário em uma conversa de chat. 

## <a name="chat-events"></a>Eventos de chat 

A sinalização em tempo real permite que os usuários conversem em tempo real. Seus serviços podem usar a Grade de Eventos do Azure para assinar eventos relacionados ao chat. Para obter mais detalhes, confira [Informações conceituais sobre a manipulação de eventos](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Como usar os Serviços Cognitivos com a biblioteca de clientes de chat para habilitar recursos inteligentes

Use as [APIs Cognitivas do Azure](https://docs.microsoft.com/azure/cognitive-services/) com a biblioteca de clientes de chat para adicionar recursos inteligentes aos seus aplicativos. Por exemplo, você pode:

- Permitir que os usuários conversem entre si em diferentes idiomas. 
- Ajudar um agente de suporte a priorizar tíquetes detectando um sentimento negativo de um problema recebido de um cliente.
- Analisar as mensagens de entrada quanto à detecção de chave e o reconhecimento de entidade e solicitar informações relevantes ao usuário no seu aplicativo com base no conteúdo da mensagem.

Um modo de conseguir isso é fazendo com que o serviço confiável funcione como um membro de uma conversa de chat. Digamos que você deseje habilitar a tradução de idioma. Esse serviço será responsável por ouvir as mensagens trocadas por outros membros [1], chamar APIs cognitivas para traduzir o conteúdo para o idioma desejado [2, 3] e enviar o resultado traduzido como uma mensagem na conversa de chat [4]. 

Dessa forma, o histórico de mensagens conterá as mensagens originais e traduzidas. No aplicativo cliente, você poderá adicionar a lógica para mostrar a mensagem original ou traduzida. Confira [este guia de início rápido](https://docs.microsoft.com/azure/cognitive-services/translator/quickstart-translate) para entender como usar as APIs Cognitivas para traduzir um texto em idiomas diferentes. 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagrama mostrando a arquitetura de chat dos Serviços de Comunicação.":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao chat](../../quickstarts/chat/get-started.md)

Os seguintes documentos podem ser do seu interesse:

- Familiarize-se com a [biblioteca de clientes de chat](sdk-features.md)
