---
title: Autenticação baseada em tokens (HTTP/2) para APNS em Hubs de Notificação do Azure | Microsoft Docs
description: Saiba como usar a nova autenticação de token para APNS.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263805"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticação baseada em tokens (HTTP/2) para APNS

## <a name="overview"></a>Visão geral

Este artigo explica como usar o novo protocolo APNS HTTP/2 com autenticação baseada em tokens.

Os principais benefícios de usar o novo protocolo incluem:

* A geração de tokens é relativamente simples (em comparação com certificados)
* Não há mais dadas de expiração – você está no controle dos seus tokens de autenticação e sua revogação
* O conteúdo agora pode ser de até 4 KB
* Comentários síncronos
* Você está no protocolo mais recente da Apple – os certificados ainda usam o protocolo binário, que está marcado para depreciação

O uso deste novo mecanismo pode ser realizado em duas etapas:

* Obtenha as informações necessárias no portal da conta do Desenvolvedor Apple.
* Configure seu hub de notificação com as novas informações.

O Notification Hubs agora está configurado para usar o novo sistema de autenticação com APNS.

Observe que se você migrou do uso de credenciais de certificado para APNS, as propriedades do token sobreescrevem seu certificado em nosso sistema, mas seu aplicativo continua recebendo notificações sem problemas.

## <a name="obtaining-authentication-information-from-apple"></a>Obtendo informações de autenticação da Apple

Para habilitar a autenticação baseada em tokens, você precisa das seguintes propriedades da sua conta do Apple Developer:

### <a name="key-identifier"></a>Identificador de chave

O identificador de chave pode ser obtido na página **Chaves** em **Certificados, Identificadores & Perfis,** em sua conta do Apple Developer:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identificador de aplicativo e nome do aplicativo

O nome e o identificador do aplicativo também estão disponíveis na página **Certificados, Identificadores & Perfis** na conta do desenvolvedor:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Configure-se através do .NET SDK ou do portal Azure

Você pode configurar seu hub para usar autenticação baseada em tokenusando nosso [SDK de cliente mais recente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)ou no portal Azure. Para habilitar a autenticação baseada em tokens no portal, entre no portal do Azure e acesse o painel **Configurações > Apple (APNS)** do seu hub de notificação. Selecione **Token** na propriedade **Modo de autenticação** para atualizar seu hub com todas as propriedades de token relevantes.

![Configurar token](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Digite as propriedades recuperadas de sua conta do Apple Developer.
* Escolha o modo de aplicação **(Produção** ou **Caixa de Areia).**
* Clique no botão **Salvar** para atualizar suas credenciais APNS.

As credenciais baseadas em tokens são compostas pelos seguintes campos:

* **ID de chave**: Identificador da chave privada gerada no portal apple developer; por exemplo, `2USFGKSKLT`.
* **ID da equipe**: Também chamado de "Prefixo" ou "Prefixo de Aplicativo". Este é o identificador para a organização no portal Apple Developer; por exemplo, `S4V3D7CHJR`.
* **ID do pacote**: Também chamado de "ID de aplicativo". Este é o identificador de pacote para o aplicativo; por exemplo, `com.microsoft.nhubsample2019`. Observe que você pode usar uma chave para muitos aplicativos. Esse valor mapeia o `apns-topic` cabeçalho HTTP ao enviar uma notificação e é usado para direcionar o aplicativo específico.
* **Token**: Também chamado de "Chave" ou "Chave Privada". Isso é obtido a partir do arquivo .p8 gerado no portal apple developer. A chave deve ter APNS ativado (que é selecionado no portal apple developer ao gerar a chave). O valor deve ter o cabeçalho/rodapé PEM retirado dele quando você fornecê-lo ao Portal/API do NH.
* **Ponto final**: Este é um alternador na lâmina portal Notification Hubs e um campo de strings na API. Os valores válidos são `https://api.push.apple.com` ou `https://api.sandbox.push.apple.com`. O Notification Hubs usa esse valor para o ambiente de produção ou sandbox, para envio de notificações. Isso deve `aps-environment` corresponder ao direito no aplicativo, caso contrário, os tokens de dispositivo APNS gerados não correspondem ao ambiente e as notificações não são enviadas.

Aqui está uma amostra de código ilustrando o uso correto:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Próximas etapas

* [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md)
* [Configure um hub de notificação no portal Azure](create-notification-hub-portal.md)
