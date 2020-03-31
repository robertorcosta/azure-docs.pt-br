---
title: Diferenças de validação por tipos de conta suportados - plataforma de identidade Microsoft | Azure
description: Conheça as diferenças de validação de várias propriedades para diferentes tipos de conta suportadas ao registrar seu aplicativo na plataforma de identidade microsoft.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128871"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Diferenças de validação por tipos de conta suportados (signInAudience)

Ao registrar um aplicativo com a plataforma de identidade Microsoft para desenvolvedores, é solicitado que você selecione quais tipos de conta seus suportes de aplicativos. No objeto de aplicação e `signInAudience`manifesto, esta propriedade é .

As opções incluem o seguinte:

- *AzureADMyOrg*: Somente contas no diretório organizacional onde o aplicativo está registrado (inquilino único)
- *AzureADMultipleOrgs*: Contas em qualquer diretório organizacional (multi-inquilino)
- *AzureADandPersonalMicrosoftAccount*: Contas em qualquer diretório organizacional (multi-inquilino) e contas pessoais da Microsoft (por exemplo, Skype, Xbox e Outlook.com)

Para aplicativos registrados, você pode encontrar o valor para tipos de conta suportados na seção **Autenticação** de um aplicativo. Você também pode encontrá-lo a `signInAudience` propriedade no **Manifesto**.

O valor selecionado para esta propriedade tem implicações em outras propriedades de objetos de aplicativo. Como resultado, se você alterar esta propriedade, você pode precisar mudar outras propriedades primeiro.

Veja a tabela a seguir para as diferenças de validação de várias propriedades para diferentes tipos de conta suportadas.

| Propriedade | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` e `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| ID de`identifierURIs`aplicação URI ( )  | Deve ser único no inquilino <br><br> urn:// esquemas são suportados <br><br> Curingas não são suportados <br><br> Cordas e fragmentos de consulta são suportados <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite* no número de URIs identificadores  | Ser globalmente exclusivo <br><br> urn:// esquemas são suportados <br><br> Curingas não são suportados <br><br> Cordas e fragmentos de consulta são suportados <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite* no número de URIs identificadores | Ser globalmente exclusivo <br><br> urn:// esquemas não são suportados <br><br> Curingas, fragmentos e strings de consulta não são suportados <br><br> Comprimento máximo de 120 caracteres <br><br> Máximo de 50 uris identificadores |
| Certificados`keyCredentials`( ) | Chave de assinatura simétrica | Chave de assinatura simétrica | Criptografia e chave de assinatura assimétrica | 
| Segredos do`passwordCredentials`cliente ( ) | Sem limite* | Sem limite* | Se o liveSDK estiver habilitado: Máximo de 2 segredos de cliente | 
| Redirecionar URIs`replyURLs`( ) | Consulte [redirecionar restrições e limitações de URL uri/resposta](reply-url.md) para obter mais informações. | | | 
| Permissões de`requiredResourceAccess`API ( ) | Sem limite* | Sem limite* | Máximo de 30 permissões por recurso permitido (por exemplo, Microsoft Graph) | 
| Escopos definidos por`oauth2Permissions`esta API ( ) | Comprimento máximo do nome do escopo de 120 caracteres <br><br> Sem limite* no número de escopos definidos | Comprimento máximo do nome do escopo de 120 caracteres <br><br> Sem limite* no número de escopos definidos |  Comprimento máximo do nome do escopo de 40 caracteres <br><br> Máximo de 100 escopos definidos | 
| Aplicações autorizadas para`preautorizedApplications`clientes ( ) | Sem limite* | Sem limite* | Máximo total de 500 <br><br> Máximo de 100 aplicativos clientedefinidos <br><br> Máximo de 30 escopos definidos por cliente | 
| appRoles | Com suporte <br> Sem limite* | Com suporte <br> Sem limite* | Sem suporte | 
| URL de logoff | http://localhosté permitido <br><br> Comprimento máximo de 255 caracteres | http://localhosté permitido <br><br> Comprimento máximo de 255 caracteres | <br><br> https://localhosté permitido, http://localhost falha para MSA <br><br> Comprimento máximo de 255 caracteres <br><br> Esquema HTTP não é permitido <br><br> Curingas não são suportados | 

*Há um limite global de cerca de 1000 itens em todas as propriedades de coleta no objeto do aplicativo

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o registro da inscrição](app-objects-and-service-principals.md)
- Conheça o [manifesto do Aplicativo](reference-app-manifest.md)
