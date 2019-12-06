---
title: Como localizar uma API específica necessária para um aplicativo personalizado | Microsoft Docs
description: Como configurar as permissões que você precisa para acessar uma API em particular em seu aplicativo personalizado do Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c223df28f8082125d48f1e3619088de5cf2687
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844593"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Como localizar uma API específica necessária para um aplicativo personalizado

O acesso às APIs exigem configuração de escopos de acesso e funções. Se você quiser expor suas APIs web do aplicativo de recursos para aplicativos cliente, precisará configurar escopos de acesso e funções da API. Se desejar que um aplicativo cliente acesse uma API web, você precisará configurar permissões para acessar a API no registro de aplicativo.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurando um aplicativo de recurso para expor APIs Web

Quando você expõe sua API web, a API é exibida na lista **Selecionar uma API** ao adicionar permissões a um registro de aplicativo. Para adicionar escopos de acesso, siga as etapas descritas em [configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurando um aplicativo cliente para acessar APIs Web

Quando você adiciona permissões em seu registro de aplicativo, você pode **Adicionar acesso à API** para APIs web expostas. Para acessar APIs Web, siga as etapas descritas em [configurar um aplicativo cliente para acessar APIs da Web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Próximos passos

- [Noções básicas sobre o manifesto de aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
