---
title: Visão geral da biblioteca de clientes de chamada dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral da biblioteca de clientes de chamada.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9cc00cfa9f44c69a5880d53c0b7ac623f60be16b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943240"
---
# <a name="calling-client-library-overview"></a>Visão geral da biblioteca de clientes de chamada

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Há duas famílias separadas de bibliotecas de clientes de chamada, para *clientes* e *serviços.* No momento, as bibliotecas de clientes disponíveis se destinam a experiências do usuário final: sites e aplicativos nativos.

As bibliotecas de cliente para serviços ainda não estão disponíveis e fornecem acesso aos planos de dados brutos de voz e vídeo, adequados para a integração com bots e outros serviços.

## <a name="calling-client-library-capabilities"></a>Funcionalidades da biblioteca de clientes de chamada

A lista a seguir apresenta o conjunto de recursos que estão disponíveis atualmente nas bibliotecas de clientes de chamada dos Serviços de Comunicação do Azure.

| Grupo de recursos | Funcionalidade                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Principais funcionalidades | Fazer uma chamada um-para-um entre dois usuários                                                                           | ✔️   | ✔️            | ✔️  
|                   | Fazer uma chamada de grupo com mais de dois usuários (até 350 usuários)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Promover uma chamada de um-para-um com dois usuários em uma chamada de grupo com mais de dois usuários                                 | ✔️   | ✔️            | ✔️ 
|                   | Ingressar em uma chamada de grupo depois que ela for iniciada                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Convidar outro participante de VoIP para ingressar em uma chamada de grupo em andamento                                                       | ✔️   | ✔️            | ✔️ 
|                   | Ativar/desativar mudo do microfone                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Alternar entre câmeras                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Ativar/desativar colocação em espera local                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Alto-falante ativo                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Escolher alto-falante para chamadas                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Escolher microfone para chamadas                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Mostrar o estado de um participante<br/>*Ocioso, Mídia inicial, Conectando, Conectado, Em espera, No lobby, Desconectado*         | ✔️   | ✔️            | ✔️           
|                   | Mostrar o estado de uma chamada<br/>*Mídia inicial, Realizando, Conectando, Tocando, Conectada, Em espera, Desconectando, Desconectada* | ✔️   | ✔️            | ✔️           
|                   | Mostrar se um participante está mudo                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Mostrar o motivo pelo qual um participante saiu de uma chamada                                                                       | ✔️   | ✔️            | ✔️     
| Compartilhamento de tela    | Compartilhar a tela inteira de dentro do aplicativo                                                                 | ✔️   | ❌            | ❌           
|                   | Compartilhar um aplicativo específico (na lista de aplicativos em execução)                                                | ✔️   | ❌            | ❌           
|                   | Compartilhar uma guia do navegador da Web na lista de guias abertas                                                                  | ✔️   | ❌            | ❌           
|                   | O participante pode exibir o compartilhamento de tela remoto                                                                            | ✔️   | ✔️            | ✔️         
| Lista de participantes            | Listar participantes                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Remover um participante                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Fazer uma chamada um-para-um com um participante PSTN                                                                     | ✔️   | ✔️            | ✔️   
|                   | Fazer uma chamada de grupo com participantes PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Promover uma chamada um-para-um com um participante PSTN em uma chamada de grupo                                                 | ✔️   | ✔️            | ✔️
|                   | Discar de uma chamada de grupo como um participante PSTN                                                                    | ✔️   | ✔️            | ✔️   
| Geral           | Participar de uma chamada de grupo contendo até 350 participantes                                                       |  ✔️  | ✔️            | ✔️    
|                   | Teste o microfone, o alto-falante e a câmera com um serviço de teste de áudio (disponível chamando 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Suporte ao navegador da biblioteca de clientes de chamada

A tabela a seguir representa o conjunto de navegadores e versões com suporte disponíveis no momento.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Biblioteca de clientes de chamada** | Chrome*, novo Edge | Chrome *, Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


*Observe há suporte para a versão mais recente do Chrome, além das duas versões anteriores.<br/>

**Observe que há suporte para as versões posteriores à 13.1 do Safari. Ainda não há suporte para vídeo de saída para o Safari no macOS, mas há suporte no iOS. Só há suporte para o compartilhamento de tela de saída no iOS desktop.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução a chamadas](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para obter mais informações, consulte os seguintes artigos:
- Familiarize-se com [fluxos de chamada](../call-flows.md) gerais
- Saiba mais sobre [tipos de chamada](../voice-video-calling/about-call-types.md)
- [Planeje sua solução PSTN](../telephony-sms/plan-solution.md)
