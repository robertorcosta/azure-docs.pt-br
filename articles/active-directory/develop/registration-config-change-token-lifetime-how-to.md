---
title: Alterar padrões de tempo de vida do token para aplicativos personalizados do Azure AD
description: Como atualizar as políticas de tempo de vida do token para o aplicativo que você está desenvolvendo no Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 8de6a7aafdd402e4ee75862e69ac60af3af0e041
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114924"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os padrões de tempo de vida do token para um aplicativo personalizado

Este artigo mostra como usar o PowerShell do Azure AD para definir uma política de tempo de vida do token. O Azure AD Premium permite que desenvolvedores de aplicativos e administradores de locatários configurem o tempo de vida de tokens emitidos para clientes não confidenciais. As políticas de tempo de vida do token são definidas com base em todos os locatários ou nos recursos que estão sendo acessados.

1. Para definir uma política de tempo de vida do token, você precisa baixar o [Módulo PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).
1. Execute o comando **Connect-AzureAD -Confirm**.

    Veja um exemplo de política que define o token de atualização de fator único de idade máxima. Crie a política: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Próximas etapas

* Consulte [Tempos de vida de token configuráveis no Azure AD](./active-directory-configurable-token-lifetimes.md) para saber como configurar tempos de vida de token emitidos pelo Azure AD, incluindo como definir tempos de vida de token para todos os aplicativos na organização, para um aplicativo de vários locatários ou para uma entidade de serviço específica na organização. 
* [Referência de token do Azure AD](./id-tokens.md)