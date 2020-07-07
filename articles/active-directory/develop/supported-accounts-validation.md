---
title: Diferenças de validação por tipos de conta com suporte – plataforma de identidade da Microsoft | Azure
description: Saiba mais sobre as diferenças de validação de várias propriedades para diferentes tipos de conta com suporte ao registrar seu aplicativo com a plataforma de identidade da Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80128871"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Diferenças de validação por tipos de conta com suporte (signInAudience)

Ao registrar um aplicativo com a plataforma de identidade da Microsoft para desenvolvedores, você será solicitado a selecionar a quais tipos de conta o aplicativo dá suporte. No objeto de aplicativo e no manifesto, essa propriedade é `signInAudience` .

As opções incluem o seguinte:

- *AzureADMyOrg*: somente as contas no diretório organizacional em que o aplicativo está registrado (locatário único)
- *AzureADMultipleOrgs*: contas em qualquer diretório organizacional (multilocatário)
- *AzureADandPersonalMicrosoftAccount*: contas em qualquer diretório organizacional (multilocatário) e contas pessoais da Microsoft (por exemplo, Skype, Xbox e Outlook.com)

Para aplicativos registrados, você pode encontrar o valor dos tipos de conta com suporte na seção de **autenticação** de um aplicativo. Você também pode encontrá-lo na `signInAudience` propriedade no **manifesto**.

O valor selecionado para essa propriedade tem implicações em outras propriedades de objeto de aplicativo. Como resultado, se você alterar essa propriedade, talvez seja necessário alterar as outras propriedades primeiro.

Consulte a tabela a seguir para obter as diferenças de validação de várias propriedades para diferentes tipos de conta com suporte.

| Propriedade | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` e `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| URI da ID do aplicativo ( `identifierURIs` )  | Deve ser exclusivo no locatário <br><br> Há suporte para esquemas urn:// <br><br> Não há suporte para curingas <br><br> Há suporte para cadeias de caracteres de consulta e fragmentos <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite * no número de identifierURIs  | Ser globalmente exclusivo <br><br> Há suporte para esquemas urn:// <br><br> Não há suporte para curingas <br><br> Há suporte para cadeias de caracteres de consulta e fragmentos <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite * no número de identifierURIs | Ser globalmente exclusivo <br><br> Não há suporte para esquemas urn:// <br><br> Não há suporte para curingas, fragmentos e cadeias de consulta <br><br> Comprimento máximo de 120 caracteres <br><br> Máximo de 50 identifierURIs |
| Certificados ( `keyCredentials` ) | Chave de assinatura simétrica | Chave de assinatura simétrica | Criptografia e chave de assinatura assimétrica | 
| Segredos do cliente ( `passwordCredentials` ) | Sem limite * | Sem limite * | Se liveSDK estiver habilitado: máximo de 2 segredos de cliente | 
| Redirecionar URIs ( `replyURLs` ) | Confira [restrições de redirecionamento de URI/URL de resposta e limitações](reply-url.md) para obter mais informações. | | | 
| Permissões de API ( `requiredResourceAccess` ) | Sem limite * | Sem limite * | Máximo de 30 permissões por recurso permitido (por exemplo, Microsoft Graph) | 
| Escopos definidos por esta API ( `oauth2Permissions` ) | Comprimento máximo do nome de escopo de 120 caracteres <br><br> Sem limite * no número de escopos definidos | Comprimento máximo do nome de escopo de 120 caracteres <br><br> Sem limite * no número de escopos definidos |  Comprimento máximo do nome de escopo de 40 caracteres <br><br> Máximo de 100 escopos definidos | 
| Aplicativos cliente autorizados ( `preautorizedApplications` ) | Sem limite * | Sem limite * | Máximo total de 500 <br><br> Máximo de 100 aplicativos cliente definidos <br><br> Máximo de 30 escopos definidos por cliente | 
| appRoles | Com suporte <br> Sem limite * | Com suporte <br> Sem limite * | Sem suporte | 
| URL de logoff | http://localhosté permitido <br><br> Comprimento máximo de 255 caracteres | http://localhosté permitido <br><br> Comprimento máximo de 255 caracteres | <br><br> https://localhosté permitido, http://localhost falha para MSA <br><br> Comprimento máximo de 255 caracteres <br><br> O esquema HTTP não é permitido <br><br> Não há suporte para curingas | 

* Há um limite global de cerca de 1000 itens em todas as propriedades de coleção no objeto de aplicativo

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [registro do aplicativo](app-objects-and-service-principals.md)
- Saiba mais sobre o [manifesto do aplicativo](reference-app-manifest.md).
