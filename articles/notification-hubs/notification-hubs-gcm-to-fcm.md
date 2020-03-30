---
title: Hubs de notificação do Azure e a migração do Google Firebase Cloud Messaging (FCM)
description: Descreve como o Azure Notification Hubs aborda a migração do Google GCM para o FCM.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127033"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Hubs de notificação do Azure e migração de mensagens na nuvem do Google Firebase

## <a name="current-state"></a>Estado atual

Quando o Google anunciou sua migração do Google Cloud Messaging (GCM) para o Firebase Cloud Messaging (FCM), serviços de push como o nosso tiveram que ajustar a forma como enviamos notificações para dispositivos Android para acomodar a mudança.

Atualizamos nosso backend de serviço, então publicamos atualizações para nossa API e SDKs conforme necessário. Com nossa implementação, tomamos a decisão de manter a compatibilidade com os esquemas de notificação da GCM existentes para minimizar o impacto do cliente. Isso significa que atualmente enviamos notificações para dispositivos Android usando FCM no Modo Legado FCM. Em última análise, queremos adicionar suporte verdadeiro para FCM, incluindo os novos recursos e formato de carga. Essa é uma mudança de longo prazo e a migração atual está focada em manter a compatibilidade com aplicativos e SDKs existentes. Você pode usar os SDKs GCM ou FCM em seu aplicativo (juntamente com nosso SDK) e garantir que a notificação seja enviada corretamente.

Alguns clientes receberam recentemente um e-mail do Google alertando sobre aplicativos usando um ponto final do GCM para notificações. Isso foi apenas um aviso, e nada está quebrado – as notificações do seu aplicativo para Android ainda são enviadas ao Google para processamento e o Google ainda os processa. Alguns clientes que especificaram o ponto final do GCM explicitamente em sua configuração de serviço ainda estavam usando o ponto final depreciado. Já tínhamos identificado essa lacuna e estávamos trabalhando para corrigir o problema quando o Google enviou o e-mail.

Substituímos aquele ponto final depreciado e a correção é implantada.

## <a name="going-forward"></a>Daqui para frente

O FAQ fcm do Google diz que você não precisa fazer nada. No [FCM FAQ,](https://developers.google.com/cloud-messaging/faq)o Google disse que "os SDKs clientes e os tokens GCM continuarão a funcionar indefinidamente. No entanto, você não será capaz de segmentar a versão mais recente do Google Play Services em seu aplicativo para Android, a menos que você migre para o FCM."

Se o seu aplicativo usar a biblioteca GCM, vá em frente e siga as instruções do Google para atualizar para a biblioteca FCM em seu aplicativo. Nosso SDK é compatível com qualquer um, então você não terá que atualizar nada em seu aplicativo do nosso lado (desde que você esteja atualizado com a nossa versão SDK).

## <a name="questions-and-answers"></a>Perguntas e respostas

Aqui estão algumas respostas para perguntas comuns que ouvimos dos clientes:

**Q:** O que eu preciso fazer para ser compatível com a data de corte (a data de corte atual do Google é 29 de maio e pode mudar)?

**A:** Nada. Manteremos a compatibilidade com o esquema de notificação gcm existente. Sua chave GCM continuará funcionando normalmente, assim como qualquer SDKs e bibliotecas GCM usadas pelo seu aplicativo.

Se/quando você decidir atualizar para os SDKs fcm e bibliotecas para aproveitar os novos recursos, sua chave GCM ainda funcionará. Você pode mudar para usar uma chave FCM se desejar, mas certifique-se de que está adicionando firebase ao seu projeto GCM existente ao criar o novo projeto Firebase. Isso garantirá a retrocompatibilidade com seus clientes que estão executando versões mais antigas do aplicativo que ainda usam SDKs e bibliotecas GCM.

Se você estiver criando um novo projeto FCM e não anexando ao projeto GCM existente, uma vez que você atualizar hubs de notificação com o novo segredo FCM, você perderá a capacidade de empurrar notificações para suas instalações atuais do aplicativo, uma vez que a nova chave FCM não tem link para o gcm antigo Projeto.

**Q:** Por que estou recebendo esse e-mail sobre os antigos pontos finais da GCM sendo usados? O que eu tenho que fazer?

**A:** Nada. Estamos migrando para os novos pontos finais e estaremos concluídos em breve, então nenhuma mudança é necessária. Nada está quebrado, nosso ponto final perdido simplesmente causou mensagens de aviso do Google.

**Q:** Como posso fazer a transição para os novos SDKs e bibliotecas FCM sem quebrar os usuários existentes?

A: Upgrade a qualquer momento. O Google ainda não anunciou qualquer depreciação dos SDKs e bibliotecas GCM existentes. Para garantir que você não quebre as notificações push para seus usuários existentes, certifique-se de que, ao criar o novo projeto Firebase, você esteja se associando ao projeto GCM existente. Isso garantirá que novos segredos do Firebase funcionem para os usuários que executam as versões mais antigas do seu aplicativo com SDKs e bibliotecas GCM, bem como novos usuários do seu aplicativo com SDKs fcm e bibliotecas.

**Q:** Quando posso usar novos recursos de FCM e esquemas para minhas notificações?

**A:** Assim que publicarmos uma atualização para nossa API e SDKs, fique ligado – esperamos ter algo para você nos próximos meses.
