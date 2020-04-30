---
title: Mover API Web chamando APIs da Web para produção-plataforma de identidade da Microsoft | Azure
description: Saiba como mover uma API Web que chama APIs da Web para produção.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537144"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API Web que chama APIs da Web: mover para produção

Depois de adquirir um token para chamar APIs Web, você pode mover seu aplicativo para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saiba mais

Agora que você conhece as noções básicas de como chamar APIs Web de sua própria API Web, você pode estar interessado no tutorial a seguir, que descreve o código usado para criar uma API Web protegida que chama APIs da Web.

| Amostra | Plataforma | Descrição |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webAPI-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | API Web do ASP.NET Core 2,2, área de trabalho (WPF) | ASP.NET Core chamadas de API Web 2,2 Microsoft Graph, que você chama de um aplicativo WPF usando a plataforma de identidade da Microsoft (v 2.0). |
