---
title: Disponibilidade e limitações do Microsoft Authenticator para Android na China | Microsoft Docs
description: Saiba mais sobre como configurar e usar o aplicativo Microsoft Authenticator e qual a disponibilidade dele na China
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 12/08/2020
ms.author: curtand
ms.openlocfilehash: 7b3af7c5bd55e748b1d1f4cf8f192797187da30a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96901789"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator para Android na nuvem pública na China

O aplicativo Microsoft Authenticator para Android está disponível para download na China. A Google Play Store não está disponível na China, portanto, o aplicativo deve ser baixado de outros marketplaces de aplicativo chineses. O aplicativo Microsoft Authenticator para Android está disponível atualmente nas seguintes lojas na China:

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

O build mais atual do aplicativo está na Google Play Store, mas estamos atualizando o aplicativo em todas as outras lojas de aplicativos o mais rapidamente possível. Já que não há nenhum APK (pacote de aplicativo Android) personalizado implantado em nenhuma loja de aplicativos, o aplicativo pode ser atualizado diretamente de um dos seguintes locais:

- A loja da qual foi baixado
- A Google Play Store, se o usuário cruzar regiões

## <a name="limitations"></a>Limitações

O aplicativo Microsoft Authenticator para Android usa o sistema Firebase Cloud Messaging da Google e o Google Play Services para receber notificações por push. Já que nenhum desses serviços está disponível na China, há algumas limitações nas funcionalidades do aplicativo:

- O registro do aplicativo Authenticator como um método de MFA (autenticação multifator) usando notificações por push não funciona.

- A [Entrada pelo telefone](../authentication/howto-authentication-sms-signin.md) não pode ser configurada. Ela exige que o usuário configure o aplicativo como um método de MFA usando notificações por push, que atualmente não funcionam.

Se um usuário tiver conseguido configurar anteriormente a entrada pelo telefone ou a autenticação multifator usando o aplicativo, ele poderá executar uma verificação manual em busca de solicitações de notificações no aplicativo e usá-la para a verificação de identidade.

## <a name="multi-factor-authentication-workaround"></a>Solução alternativa para a autenticação multifator

Em vez de usar notificações por push para a autenticação multifator, os usuários podem [configurar o aplicativo Authenticator para receber códigos de verificação](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes) nos dispositivos deles, que eles podem usar para MFA para verificar as respectivas identidades. Esses códigos de verificação são válidos por 30 segundos e, para usá-los, os administradores devem habilitar o locatário deles para realizar a verificação usando códigos de verificação TOTP (Senhas Avulsas por Tempo Limitado).

## <a name="availability"></a>Disponibilidade

Recurso do Microsoft Authenticator | Disponibilidade na China
------------------------------- | ---------------------
Registro de MFA usando notificações por push | Não
Conta do MFA preexistente verificando a identidade usando notificações por push | Não
Conta do MFA já existente executando verificação manual para notificações | Sim
Registro/autenticação MFA usando somente códigos de verificação/TOTP | Sim
Registro de entrada pelo telefone | Não
Entrada pelo telefone existente usando notificações por push | Não
Verificação de entrada pelo telefone existente executando verificação manual de solicitações de autenticação | Sim
Suporte para o aplicativo autenticador para contas pessoais da Microsoft | Não

## <a name="next-steps"></a>Próximas etapas

- [Baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md)
