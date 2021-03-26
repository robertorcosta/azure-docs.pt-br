---
title: Visão geral do SDK de chat para os Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre o SDK de chat dos Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b275c3af2e92dc5af677120b5082751d19676b2e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110807"
---
# <a name="chat-sdk-overview"></a>Visão geral do SDK de chat 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Os SDKs de chat dos Serviços de Comunicação do Azure podem ser usados para adicionar um chat sofisticado e em tempo real aos seus aplicativos.
    
## <a name="chat-sdk-capabilities"></a>Funcionalidades do SDK de chat    

A lista a seguir apresenta o conjunto de recursos que estão atualmente disponíveis nos SDKs de chat dos Serviços de Comunicação.  

| Grupo de recursos | Funcionalidade | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Principais funcionalidades | Criar uma conversa de chat entre dois ou mais usuários (até 250 usuários)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Atualizar o tópico de uma conversa de chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Adicionar ou remover participantes de uma conversa de chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Escolher se deseja compartilhar o histórico de mensagens de chat com o participante que está sendo adicionado                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Obter uma lista de participantes em uma conversa de chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Excluir uma conversa de chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Dado um usuário de comunicação, obtenha a lista de conversas de chat do qual o usuário faz parte                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Obter informações de uma conversa de chat específica                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Enviar e receber mensagens em uma conversa de chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Editar o conteúdo de uma mensagem enviada                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Excluir uma mensagem                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Confirmações de leitura de mensagens que foram lidas por outros participantes em um chat <br/> *Não disponível quando há mais de 20 participantes em uma conversa de chat*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Receber notificação quando os participantes estiverem digitando ativamente uma mensagem em uma conversa de chat <br/> *Não disponível quando há mais de 20 membros em uma conversa de chat*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Obter todas as mensagens de uma conversa de chat <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Enviar emojis Unicode como parte do conteúdo da mensagem                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Sinalização em tempo real (habilitada pelo pacote de sinalização proprietário**)|  Assine para obter atualizações em tempo real de mensagens de entrada e outras operações em seu aplicativo de chat. Para ver uma lista de atualizações com suporte para sinalização em tempo real, confira [Conceitos de chat](concepts.md#real-time-signaling)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Suporte à Grade de Eventos             | Use a integração com a Grade de Eventos do Azure e configure seu serviço de comunicação para executar a lógica de negócios com base na atividade de chat ou para conectar um serviço de notificação por push personalizado   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Monitoramento        | Usar as métricas de solicitação de API emitidas no portal do Azure para criar painéis, monitorar a integridade de seu aplicativo de chat e definir alertas para detectar anormalidades      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Configurar o recurso de Serviços de Comunicação para receber logs operacionais de chat para fins de monitoramento e diagnóstico          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


**O pacote de sinalização proprietário é implementado usando soquetes Web. Ele fará o fallback para a sondagem longa se não houver suporte para soquetes Web.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Suporte ao SDK de chat do JavaScript por sistema operacional e navegador    

A tabela a seguir representa o conjunto de navegadores e versões com suporte disponíveis no momento.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | Sistema operacional do iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **SDK de chat** | Firefox *, Chrome*, novo Edge | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Observe há suporte para a última versão, além das duas versões anteriores.<br/>   

## <a name="next-steps"></a>Próximas etapas   

> [!div class="nextstepaction"] 
> [Introdução ao chat](../../quickstarts/chat/get-started.md)    

Os seguintes documentos podem ser do seu interesse:  
- Familiarize-se com os [conceitos de chat](../chat/concepts.md)