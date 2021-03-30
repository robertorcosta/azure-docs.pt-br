---
title: Conceitos de chat nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de chat dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e71917d917491037100b4ee666cbeb030b0a4786
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656134"
---
# <a name="chat-concepts"></a>Conceitos de chat 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

As bibliotecas de clientes de chat dos Serviços de Comunicação do Azure podem ser usadas para adicionar o bate-papo com texto em tempo real aos seus aplicativos. Esta página resume os principais conceitos e funcionalidades de chat.    

Confira a [Visão geral da biblioteca de clientes de chat dos Serviços de Comunicação](./sdk-features.md) para saber mais sobre as funcionalidades e as linguagens específicas da biblioteca de clientes.    

## <a name="chat-overview"></a>Visão geral do chat    

As conversas de chat acontecem em grupos de mensagens de chat. Uma conversa de chat pode conter muitas mensagens e muitos usuários. Cada mensagem pertence a uma só conversa, e um usuário pode fazer parte de uma ou várias conversas. Todo usuário na conversa de chat é chamado de participante. Somente os participantes da conversa podem enviar e receber mensagens e adicionar ou remover outros usuários em uma conversa de chat. Os Serviços de Comunicação armazenam o histórico de chat até que você execute uma operação de exclusão na conversa ou na mensagem de chat ou até que não haja participantes restantes na conversa de chat, quando ela se torna órfã e é colocada na fila para exclusão. 
    
## <a name="service-limits"></a>Limites de serviço   

- O número máximo de participantes permitidos em uma conversa de chat é 250.   
- O tamanho máximo permitido da mensagem é de aproximadamente 28 KB.  
- Para conversas de chat com mais de 20 participantes, não há suporte para os recursos de indicador de digitação e confirmações de leitura.    
- 
## <a name="chat-architecture"></a>Arquitetura de chat    

Há duas partes principais na arquitetura de chat: 1) Serviço confiável e 2) Aplicativo cliente.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagrama mostrando a arquitetura de chat dos Serviços de Comunicação."::: 

 - **Serviço confiável:** para gerenciar corretamente uma sessão de chat, você precisará de um serviço que ajude você a se conectar aos Serviços de Comunicação usando a cadeia de conexão do recurso. Esse serviço é responsável por criar conversas de chat, gerenciar listas de participantes da conversa e fornecer tokens de acesso aos usuários. Encontre mais informações sobre tokens de acesso em nosso guia de início rápido sobre [tokens de acesso](../../quickstarts/access-tokens.md).   
 - **Aplicativo cliente:**  o aplicativo cliente se conecta ao seu serviço confiável e recebe os tokens de acesso usados para se conectar diretamente aos Serviços de Comunicação. Depois que essa conexão é feita, o aplicativo cliente pode enviar e receber mensagens.   
É recomendável gerar tokens de acesso usando a camada de serviço confiável. Nesse cenário, o lado do servidor seria responsável por criar e gerenciar usuários e emitir os tokens deles.   
        
## <a name="message-types"></a>Tipos de Mensagem    

O chat dos Serviços de Comunicação compartilha as mensagens geradas pelo usuário, bem como as mensagens geradas pelo sistema, chamadas **atividades de conversa**. As atividades de conversa são geradas quando uma conversa de chat é atualizada. Quando você chamar `List Messages` ou `Get Messages` em uma conversa de chat, o resultado conterá as mensagens de texto geradas pelo usuário, bem como as mensagens do sistema em ordem cronológica. Isso ajuda você a identificar quando um participante foi adicionado ou removido ou quando o tópico da conversa de chat foi atualizado. Os tipos de mensagem compatíveis são:  
    
 - `Text`: uma mensagem de texto sem formatação composta e enviada por um usuário como parte de uma conversa de chat. 
 - `RichText/HTML`: uma mensagem de texto formatada. Observe que os usuários dos Serviços de Comunicação não podem enviar mensagens RichText no momento. Há suporte para esse tipo de mensagem nas mensagens enviadas de usuários do Teams para os usuários dos Serviços de Comunicação em cenários de interoperabilidade do Teams.   
 - `ThreadActivity/ParticipantAdded`: uma mensagem do sistema que indica que um ou mais participantes foram adicionados à conversa do chat. Por exemplo:  


``` 
{   
            "id": "1613589626560",  
            "type": "participantAdded", 
            "sequenceId": "7",  
            "version": "1613589626560", 
            "content":  
            {   
                "participants": 
                [   
                    {   
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",    
                        "displayName": "Jane",  
                        "shareHistoryTime": "1970-01-01T00:00:00Z"  
                    }   
                ],  
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:26Z" 
        }   
``` 

- `ThreadActivity/ParticipantRemoved`: mensagem do sistema que indica que um participante foi removido do thread de chat. Por exemplo:   

``` 
{   
            "id": "1613589627603",  
            "type": "participantRemoved",   
            "sequenceId": "8",  
            "version": "1613589627603", 
            "content":  
            {   
                "participants": 
                [   
                    {   
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",    
                        "displayName": "Jane",  
                        "shareHistoryTime": "1970-01-01T00:00:00Z"  
                    }   
                ],  
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:27Z" 
        }   
``` 

- `ThreadActivity/TopicUpdate`: mensagem do sistema que indica que o tópico da conversa foi atualizado. Por exemplo:   
``` 
{   
            "id": "1613589623037",  
            "type": "topicUpdated", 
            "sequenceId": "2",  
            "version": "1613589623037", 
            "content":  
            {   
                "topic": "New topic",   
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:23Z" 
        }   
``` 

## <a name="real-time-signaling"></a>Sinalização em tempo real  

A biblioteca de clientes de chat JavaScript inclui a sinalização em tempo real. Isso permite que os clientes escutem atualizações em tempo real e mensagens de entrada para uma conversa de chat sem precisar sondar as APIs. Os eventos disponíveis incluem:

 - `ChatMessageReceived`: quando uma nova mensagem é enviada a uma conversa de chat. Esse evento não é enviado para as mensagens do sistema geradas automaticamente discutidas no tópico anterior.   
 - `ChatMessageEdited`: quando uma mensagem é editada em uma conversa de chat. 
 - `ChatMessageDeleted`: quando uma mensagem é excluída de uma conversa de chat.   
 - `TypingIndicatorReceived`: quando outro participante tenta digitar uma mensagem em uma conversa de chat.   
 - `ReadReceiptReceived`: quando outro participante leu a mensagem enviada pelo usuário em uma conversa de chat.     
 - `ChatThreadCreated`: quando uma conversa de chat é criada por um usuário da comunicação. 
 - `ChatThreadDeleted`: quando uma conversa de chat é excluída por um usuário da comunicação. 
 - `ChatThreadPropertiesUpdated`: quando as propriedades da conversa de chat são atualizadas. Atualmente, damos suporte apenas à atualização do tópico da conversa.   
 - `ParticipantsAdded`: quando um usuário é adicionado como participante a uma conversa de chat.  
 - `ParticipantsRemoved`: quando um participante existente é removido da conversa de chat.


## <a name="chat-events"></a>Eventos de chat  

A sinalização em tempo real permite que os usuários conversem em tempo real. Seus serviços podem usar a Grade de Eventos do Azure para assinar eventos relacionados ao chat. Para obter mais detalhes, confira [Informações conceituais sobre a manipulação de eventos](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?tabs=event-grid-event-schema).


## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Como usar os Serviços Cognitivos com a biblioteca de clientes de chat para habilitar recursos inteligentes 

Use as [APIs Cognitivas do Azure](../../../cognitive-services/index.yml) com a biblioteca de clientes de chat para adicionar recursos inteligentes aos seus aplicativos. Por exemplo, você pode:  

- Permitir que os usuários conversem entre si em diferentes idiomas.  
- Ajudar um agente de suporte a priorizar tíquetes detectando um sentimento negativo de um problema recebido de um cliente.   
- Analisar as mensagens de entrada quanto à detecção de chave e o reconhecimento de entidade e solicitar informações relevantes ao usuário no seu aplicativo com base no conteúdo da mensagem.

Um modo de conseguir isso é fazendo com que o serviço confiável funcione como um participante de uma conversa de chat. Digamos que você deseje habilitar a tradução de idioma. Esse serviço será responsável por escutar as mensagens trocadas por outros participantes [1], chamar APIs cognitivas para traduzir o conteúdo para o idioma desejado [2, 3] e enviar o resultado traduzido como uma mensagem na conversa de chat [4].

Dessa forma, o histórico de mensagens conterá as mensagens originais e traduzidas. No aplicativo cliente, você poderá adicionar a lógica para mostrar a mensagem original ou traduzida. Confira [este guia de início rápido](../../../cognitive-services/translator/quickstart-translator.md) para entender como usar as APIs Cognitivas para traduzir um texto em idiomas diferentes. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagrama mostrando os Serviços Cognitivos interagindo com os Serviços de Comunicação."::: 

## <a name="next-steps"></a>Próximas etapas   

> [!div class="nextstepaction"] 
> [Introdução ao chat](../../quickstarts/chat/get-started.md)    

Os seguintes documentos podem ser do seu interesse:  
- Familiarize-se com a [biblioteca de clientes de chat](sdk-features.md)