---
title: Opções avançadas de assinatura de certificados de token SAML para aplicativos AD do Azure
description: Saiba como usar as opções avançadas de assinatura de certificado no token SAML para aplicativos pré-integrados no Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159192"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>As opções avançadas de assinatura de certificado no token SAML para aplicativos da galeria no Azure Active Directory

Atualmente, o Azure Active Directory (AD do Azure) oferece suporte a milhares de aplicativos pré-integrados na Galeria de aplicativo do Azure Active Directory. Mais de 500 dos aplicativos suportam o login único usando o protocolo SAML [(Security Assertion Markup Language)](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) 2.0 , como o aplicativo [NetSuite.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Quando um cliente autentica em um aplicativo através do Azure AD usando o SAML, o Azure AD envia um token para o aplicativo (através de um POST HTTP). Em seguida, o aplicativo valida e usa o token para fazer login no cliente em vez de solicitar um nome de usuário e senha. Esses tokens SAML são assinados com o certificado exclusivo que é gerado no Azure AD e por algorítimos padrão específicos.

O Azure AD usa algumas das configurações padrão para os aplicativos de galeria. Os valores padrão são configurados com base nos requisitos do aplicativo.

No Azure AD, você pode configurar opções de assinatura de certificados e o algoritmo de assinatura de certificados.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

O Azure AD dá suporte a três opções de assinatura de certificado:

* **Assinar declaração SAML**. A opção padrão é definida para a maioria dos aplicativos de galeria. Se você selecionar essa opção, o Azure AD como provedor de identidade (IdP) assinará a afirmação e o certificado SAML com o certificado [X.509](https://wikipedia.org/wiki/X.509) do aplicativo.

* **Assinar resposta SAML**. Se você selecionar essa opção, o Azure AD como um IdP assinará a resposta SAML com o certificado X.509 do aplicativo.

* **Assinar resposta SAML e declaração**. Se você selecionar essa opção, o Azure AD como um IdP assinará todo o token SAML com o certificado X.509 do aplicativo.

## <a name="certificate-signing-algorithms"></a>Algoritmo de assinatura de certificado

O Azure AD suporta dois algoritmos de assinatura, ou algoritmos hash seguros (SHAs), para assinar a resposta SAML:

* **SHA-256**. O Azure AD dá suporte a dois algoritmos de assinatura para assinar resposta SAML. É o mais novo algoritmo e é mais seguro que o SHA-1. A maioria dos aplicativos dá suporte ao algoritmo SHA-256. Se um aplicativo suporta somente SHA-1 como o algoritmo de assinatura, você pode alterá-lo. Caso contrário, é recomendável usar o algoritmo SHA256 para assinar a resposta SAML.

* **SHA-1**. Este algoritmo é mais antigo, e é tratado como menos seguro que sha-256. Se o aplicativo der suporte somente a esse algoritmo de assinatura, você poderá selecionar essa opção na lista suspensa de **Algorítimo de Assinatura**. O Azure AD assina a resposta SAML com o algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Alterar opções de assinatura de certificados e algoritmo de assinatura

Para alterar as opções de assinatura de certificado SAML de um aplicativo e o algoritmo de assinatura de certificados, selecione o aplicativo em questão:

1. No portal Diretório Ativo do [Azure,](https://aad.portal.azure.com/)faça login na sua conta. A página **central do diretório ativo do Azure** é exibida.
1. No painel esquerdo, selecione **Aplicativos Empresariais**. Uma lista dos aplicativos corporativos em sua conta é exibida.
1. Selecione um aplicativo. Uma página de visão geral do aplicativo é exibida.

   ![Exemplo: Página de visão geral do aplicativo](./media/certificate-signing-options/application-overview-page.png)

Em seguida, altere as opções de assinatura de certificado no token SAML para esse aplicativo:

1. No painel esquerdo da página de visão geral do aplicativo, selecione **'Apostação única ''''''''**
1. Se a **página Configurar o único sinal de inscrição com SAML - Preview** for exibida, vá para a etapa 5.
1. Se a página Selecionar uma única página **do método de login** não aparecer, selecione Alterar **modos de login único** para exibir essa página.
1. Na página Selecionar uma única página **do método de login,** selecione **SAML** se disponível. (Se **o SAML** não estiver disponível, o aplicativo não suporta o SAML e você pode ignorar o resto deste procedimento e artigo.)
1. Na **página Configurar único de login com SAML - Preview,** encontre o título **do Certificado de Assinatura SAML** e selecione o ícone **Editar** (um lápis). A página **certificado de assinatura SAML** é exibida.

   ![Exemplo: Página de certificado de assinatura SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Na lista suspensa **Opção de Assinatura**, escolha **Assinar resposta SAML**, **Assinar declaração SAML** ou **Assinar resposta e declaração SAML**. As descrições dessas opções aparecem anteriormente neste artigo nas [opções de assinatura](#certificate-signing-options)de certificados .
1. Na lista suspensa **Algoritmo de Assinatura**, escolha **SHA-1** ou **SHA-256**. As descrições dessas opções aparecem anteriormente neste artigo na seção [Algoritmos de assinatura de Certificados.](#certificate-signing-algorithms)
1. Se você estiver satisfeito com suas escolhas, **selecione Salvar** para aplicar as novas configurações de certificado de assinatura SAML. Caso contrário, selecione o **X** para descartar as alterações.

## <a name="next-steps"></a>Próximas etapas

* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](configure-federated-single-sign-on-non-gallery-applications.md)
* [Solução de problemas de logon único baseado em SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
