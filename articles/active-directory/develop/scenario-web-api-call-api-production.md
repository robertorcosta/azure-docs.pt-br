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
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518191"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API Web que chama APIs da Web: mover para produção

Depois de adquirir um token para chamar APIs Web, você pode mover seu aplicativo para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saiba mais

Agora que você conhece as noções básicas de como chamar APIs Web de sua própria API Web, você pode estar interessado no tutorial a seguir, que descreve o código usado para criar uma API Web protegida que chama APIs da Web.

| Amostra | Plataforma | Descrição |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webAPI-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) capítulo 1 | API Web do ASP.NET Core, área de trabalho (WPF) | ASP.NET Core chamadas à API Web Microsoft Graph, que você chama de um aplicativo WPF usando a plataforma de identidade da Microsoft (v 2.0). |
