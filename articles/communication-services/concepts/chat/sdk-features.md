---
title: Visão geral da biblioteca de clientes de chat dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre a biblioteca de clientes de chat dos Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 705bd926c2ac6f414464254969b5c511c88891f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656100"
---
# <a name="chat-client-library-overview"></a>Visão geral da biblioteca de clientes de chat  

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

As bibliotecas de clientes de chat dos Serviços de Comunicação do Azure podem ser usadas para adicionar um chat sofisticado e em tempo real aos seus aplicativos.
    
## <a name="chat-client-library-capabilities"></a>Funcionalidades da biblioteca de clientes de chat 

A lista a seguir apresenta o conjunto de recursos que estão atualmente disponíveis nas bibliotecas de clientes de chat dos Serviços de Comunicação.  

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

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>Suporte para bibliotecas de clientes de chat JavaScript por sistema operacional e navegador 

A tabela a seguir representa o conjunto de navegadores e versões com suporte disponíveis no momento.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | Sistema operacional do iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Biblioteca de clientes de chat** | Firefox *, Chrome*, novo Microsoft Edge | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Observe há suporte para a última versão, além das duas versões anteriores.<br/>   

## <a name="next-steps"></a>Próximas etapas   

> [!div class="nextstepaction"] 
> [Introdução ao chat](../../quickstarts/chat/get-started.md)    

Os seguintes documentos podem ser do seu interesse:  
- Familiarize-se com os [conceitos de chat](../chat/concepts.md)