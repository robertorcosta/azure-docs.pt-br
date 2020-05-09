---
title: Diretrizes de privacidade para assistentes de voz no Windows
titleSuffix: Azure Cognitive Services
description: As instruções para habilitar a ativação de voz para um agente de voz por padrão.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997508"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Diretrizes de privacidade para assistentes de voz no Windows

É importante que os usuários recebam informações claras sobre como seus dados de voz são coletados e usados e é importante que eles recebam controle sobre se e como essa coleção acontece. Essas facetas principais de privacidade – *divulgação* e *consentimento* – são especialmente importantes para os assistentes de voz integrados ao Windows, considerando a natureza sempre ouvindo de seu uso.

Os desenvolvedores que criam assistentes de voz no Windows devem incluir elementos de interface do usuário claros em seus aplicativos que refletem os recursos de escuta do assistente.

> [!NOTE]
> A falha em fornecer a divulgação e o consentimento apropriados para um aplicativo assistente, incluindo após as atualizações do aplicativo, pode fazer com que o assistente fique indisponível para ativação de voz até que os problemas de privacidade sejam resolvidos. 

## <a name="minimum-requirements-for-feature-inclusion"></a>Requisitos mínimos para inclusão de recursos

Os usuários do Windows podem ver e controlar a disponibilidade de seus aplicativos **`Settings > Privacy > Voice activation`** assistente no.

 > [!div class="mx-imgBorder"]
 > [![privacidade – listagem de aplicativos](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Uma entrada de configuração de privacidade de ativação do Windows Voice para um aplicativo assistente")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Para se tornar qualificado para inclusão nessa lista, um aplicativo deve:

1. Diga aos seus usuários de forma proeminente que ele escutará por uma palavra-chave, mesmo quando o aplicativo não estiver em execução e qual é a palavra-chave
1. Inclua uma descrição de como os dados de voz de um usuário serão usados, incluindo um link ou referência a políticas de privacidade relevantes
1. Informar aos usuários que, além das configurações no aplicativo, os usuários podem exibir e modificar suas opções de privacidade no **`Settings > Privacy > Voice activation`**, incluindo opcionalmente um link de protocolo `ms-settings:privacy-voiceactivation` para o acesso direto

Depois de atender a esses requisitos e obter aprovação da Microsoft, um aplicativo de assistente será exibido na lista de aplicativos de ativação de voz depois `Windows.ApplicationModel.ConversationalAgent` que ele estiver registrado com as APIs e os usuários poderão conceder consentimento ao aplicativo para a ativação de palavra-chave. Por padrão, essas duas configurações são `Off` e exigem que o usuário visite manualmente a página de configurações para habilitar o.

> [!NOTE]
> Em todos os casos, a permissão de ativação de voz requer permissão de microfone. Se um aplicativo de assistente não tiver acesso ao microfone, ele não será elegível para ativação de voz e aparecerá nas configurações de privacidade de ativação de voz em um estado desabilitado.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Requisitos adicionais para inclusão no consentimento do microfone

Os autores do assistente que desejam tornar mais fácil e suave que seus usuários aceitem a ativação de voz podem fazer isso atendendo a alguns requisitos adicionais aos anteriores. Depois de atenderem a `On` eles, uma configuração padrão de ativação de voz do aplicativo de assistente, que não é desbloqueada, usará como padrão uma vez (e somente uma vez) o acesso ao microfone será concedido ao aplicativo. Isso elimina a necessidade de uma viagem extra para as configurações antes de ativar um assistente para voz.

Os requisitos adicionais são que um aplicativo assistente deve:

1. **Antes** de solicitar o consentimento do microfone (por exemplo, usando `AppCapability.RequestAccessAsync` a API), forneça uma indicação proeminente ao usuário que o aplicativo assistente gostaria de escutar na voz de um usuário para uma palavra-chave, mesmo quando o aplicativo não está em execução e gostaria do consentimento do usuário
2. Incluir todas as informações relevantes para o uso de dados e políticas de privacidade **antes** de solicitar acesso `Windows.ApplicationModel.ConversationalAgent` ao microfone ou usar as APIs
3. Evite qualquer diretiva ou palavra à esquerda (por exemplo, "clique em Sim no prompt a seguir") no fluxo da experiência que divulga o comportamento de captura de áudio e solicitando permissão

Depois que esses requisitos forem atendidos, um aplicativo assistente qualificado será exibido na lista de aplicativos qualificados para ativação de voz `enabled` em um estado assim que o acesso ao microfone for concedido.

> [!NOTE]
> A ativação de voz acima do bloqueio não está qualificada para habilitação automática com acesso ao microfone e ainda exigirá que um usuário visite a página de privacidade ativação de voz para habilitar o acesso de bloqueio acima para um assistente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre as práticas recomendadas para assistentes de voz no Windows](windows-voice-assistants-best-practices.md)