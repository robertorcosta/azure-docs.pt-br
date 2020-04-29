---
title: API Web protegida-visão geral
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida (visão geral).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537195"
---
# <a name="scenario-protected-web-api"></a>Cenário: API Web protegida

Nesse cenário, você aprende a expor uma API da Web. Você também aprende a proteger a API Web para que somente usuários autenticados possam acessá-la.

Para usar sua API Web, você precisa habilitar usuários autenticados com contas corporativas e de estudante ou habilitar contas pessoais da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Especificações

Aqui estão informações específicas que você precisa saber para proteger as APIs da Web:

- O registro do aplicativo deve expor pelo menos um escopo. A versão do token aceita pela sua API da Web depende do público-alvo de entrada.
- A configuração de código para a API Web deve validar o token usado quando a API Web é chamada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do aplicativo](scenario-protected-web-api-app-registration.md)
