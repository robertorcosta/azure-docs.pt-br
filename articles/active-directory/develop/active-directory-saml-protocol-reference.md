---
title: Como o Azure AD usa o protocolo SAML
description: Este artigo fornece uma visão geral dos perfis SAML de Logon Único e Logout Único no Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 54d8278a93bfd2d6009422a5c105be14a7bc3c80
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87274316"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Como o Azure AD usa o protocolo SAML

O Azure AD (Azure Active Directory) usa o protocolo SAML 2.0 para permitir que os aplicativos forneçam uma experiência de logon único para os usuários. O perfis SAML do [Logon Único](single-sign-on-saml-protocol.md) e do [Logout Único](single-sign-out-saml-protocol.md) do Azure AD explicam como as declarações SAML, protocolos e vinculações são usados no serviço do provedor de identidade.

O protocolo SAML requer que o provedor de identidade (Azure AD) e o provedor de serviço (o aplicativo) troquem informações sobre si mesmos.

Quando um aplicativo é registrado no Azure AD, o desenvolvedor do aplicativo registra informações relacionadas à federação no Azure AD. Essa informações incluem o **URI de Redirecionamento** e o **URI de Metadados** do aplicativo.

O Azure AD usa o **URI de Metadados** do serviço de nuvem para recuperar a chave de assinatura e o URI de logout. O cliente pode abrir o aplicativo em **Azure AD -> Registro de Aplicativo** e, em seguida, em **Configurações -> Propriedades**, pode atualizar a URL de logoff. Assim, o Azure AD pode enviar a resposta para a URL correta. 

O Azure Active Directory expõe pontos de extremidade de logon único e logout único comuns e específicos de locatário (independente do locatário). Essas URLs representam os locais endereçáveis (não são apenas identificadores) para que você possa ir ao ponto de extremidade ler os metadados.

* O ponto de extremidade específico do locatário está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. O *\<TenantDomainName>* espaço reservado representa um nome de domínio registrado ou um GUID tenantid de um locatário do Azure AD. Por exemplo, os metadados de federação do locatário contoso.com estão em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto de extremidade independente de locatário está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. No endereço desse ponto de extremidade, **comum** é exibido, em vez de um nome de domínio do locatário ou ID.

Para obter informações sobre os documentos de metadados de Federação que o Azure AD publica, consulte [metadados de Federação](../azuread-dev/azure-ad-federation-metadata.md).
