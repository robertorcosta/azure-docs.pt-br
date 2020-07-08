---
title: Introdução da área de trabalho do Windows do Azure AD v2-configuração
description: Como um aplicativo .NET da Área de Trabalho do Windows (XAML) pode obter um token de acesso e chamar uma API protegida pelo ponto de extremidade do Azure Active Directory v2.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885762"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar as informações de registro do aplicativo ao aplicativo
Nesta etapa, você precisa adicionar a ID do Aplicativo ao projeto.

1.  Abra `App.xaml.cs` e substitua a linha que contém a `ClientId` por:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Próximas etapas

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
