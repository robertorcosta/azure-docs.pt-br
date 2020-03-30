---
title: Como o Azure AD usa o protocolo SAML | Microsoft Docs
description: Este artigo fornece uma visão geral dos perfis SAML de Logon Único e Logout Único no Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dc7771f29fb5d00aedfe5162a98f5f0c14544a7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161164"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Como o Azure AD usa o protocolo SAML

O Azure AD (Azure Active Directory) usa o protocolo SAML 2.0 para permitir que os aplicativos forneçam uma experiência de logon único para os usuários. O perfis SAML do [Logon Único](single-sign-on-saml-protocol.md) e do [Logout Único](single-sign-out-saml-protocol.md) do Azure AD explicam como as declarações SAML, protocolos e vinculações são usados no serviço do provedor de identidade.

O protocolo SAML requer que o provedor de identidade (Azure AD) e o provedor de serviço (o aplicativo) troquem informações sobre si mesmos.

Quando um aplicativo é registrado no Azure AD, o desenvolvedor do aplicativo registra informações relacionadas à federação no Azure AD. Essa informações incluem o **URI de Redirecionamento** e o **URI de Metadados** do aplicativo.

O Azure AD usa o **URI de Metadados** do serviço de nuvem para recuperar a chave de assinatura e o URI de logout. O cliente pode abrir o aplicativo em **Azure AD -> Registro de Aplicativo** e, em seguida, em **Configurações -> Propriedades**, pode atualizar a URL de logoff. Assim, o Azure AD pode enviar a resposta para a URL correta. 

O Azure Active Directory expõe pontos de extremidade de logon único e logout único comuns e específicos de locatário (independente do locatário). Essas URLs representam os locais endereçáveis (não são apenas identificadores) para que você possa ir ao ponto de extremidade ler os metadados.

* O ponto de extremidade específico do locatário está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. O espaço reservado * \<de>TenantDomainName* representa um nome de domínio registrado ou Guia TenantID de um inquilino Azure AD. Por exemplo, os metadados de federação do locatário contoso.com estão em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto de extremidade independente de locatário está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. No endereço desse ponto de extremidade, **comum** é exibido, em vez de um nome de domínio do locatário ou ID.

Para obter informações sobre os documentos de metadados da federação que o Azure AD publica, consulte [Metadados da Federação](../azuread-dev/azure-ad-federation-metadata.md).
