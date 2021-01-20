---
title: Introdução à interoperabilidade do Teams nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a participar de uma reunião do Teams com a biblioteca de clientes de Chat de Comunicação do Azure
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 1ad6b7241c7167c6da8952e7db2797fa275b7246
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251907"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Início Rápido: Ingressar seu aplicativo de chat em uma reunião do Teams

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Comece a usar os Serviços de Comunicação do Azure conectando sua solução de chat ao Microsoft Teams usando a biblioteca de clientes JavaScript. 

## <a name="prerequisites"></a>Pré-requisitos 

1. Uma  [implantação do Teams](https://docs.microsoft.com/deployoffice/teams-install). 
2. Um [aplicativo de chat](./get-started.md) funcional. 

## <a name="enable-teams-interoperability"></a>Habilitar a interoperabilidade do Teams 

Um usuário dos Serviços de Comunicação que participa de uma reunião do Teams como um usuário convidado pode acessar o chat da reunião somente quando ele se conectou à chamada de reunião do Teams. Confira a documentação sobre a [interoperabilidade do Teams](../voice-video-calling/get-started-teams-interop.md) para saber como adicionar um usuário dos Serviços de Comunicação a uma chamada de reunião do Teams.

Você precisa ser membro da organização proprietária de ambas as entidades para usar esse recurso.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- Confira nosso [exemplo de chat em destaque](../../samples/chat-hero-sample.md)
- Saiba mais sobre [como o chat funciona](../../concepts/chat/concepts.md)
