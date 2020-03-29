---
title: API web protegida - visão geral
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web protegida (visão geral).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773348"
---
# <a name="scenario-protected-web-api"></a>Cenário: API web protegida

Neste cenário, você aprende a expor uma API web. Você também aprende como proteger a API da Web para que apenas usuários autenticados possam acessá-la.

Para usar sua API web, você precisa habilitar usuários autenticados com contas de trabalho e escola ou ativar contas pessoais da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Especificidades

Aqui estão informações específicas que você precisa saber para proteger as APIs da Web:

- Seu registro de aplicativo deve expor pelo menos um escopo. A versão de token aceita pela Sua API da Web depende da audiência de login.
- A configuração de código para a API web deve validar o token usado quando a API da Web é chamada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do aplicativo](scenario-protected-web-api-app-registration.md)
