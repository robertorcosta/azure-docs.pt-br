---
title: 'Azure AD Connect: Logon Único Contínuo | Microsoft Docs'
description: Este tópico descreve o Logon Único Contínuo do Azure AD (Azure Active Directory) e como ele permite que você forneça o verdadeiro logon único para usuários de área de trabalho corporativos dentro da sua rede corporativa.
services: active-directory
keywords: o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7791e7b50a963d2f92a2cbc460e36f9e83bb1b52
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025705"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Logon Único Contínuo do Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é o Logon Único Contínuo do Azure Active Directory?

O SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory) conecta usuários automaticamente quando estiverem nos respectivos dispositivos corporativos conectados à sua rede corporativa. Quando habilitado, os usuários não precisam digitar as senhas para entrar no Azure AD e, geralmente, nem precisam digitar os nomes de usuário. Esse recurso fornece aos usuários acesso fácil a seus aplicativos baseados em nuvem sem a necessidade de nenhum componente local adicional.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

O SSO Contínuo pode ser combinado com o método de entrada de [Sincronização de Hash de Senha](how-to-connect-password-hash-synchronization.md) ou de [Autenticação de Passagem](how-to-connect-pta.md). O SSO contínuo _não_ é aplicável aos Serviços de Federação do Active Directory (AD FS).

![Logon Único Contínuo](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>O SSO contínuo precisa no dispositivo do usuário para ser **ingressado no domínio**, mas não necessário para o dispositivo esteja [ingressado no Azure AD](../active-directory-azureadjoin-overview.md).

## <a name="key-benefits"></a>Principais benefícios

- *Ótima experiência do usuário*
  - Os usuários são conectados automaticamente aos aplicativos baseados em nuvem e locais.
  - Os usuários não precisam digitar suas senhas repetidamente.
- *Fácil de implantar e administrar*
  - Não há necessidade de nenhum componente adicional local para fazer com que ele funcione.
  - Funciona com qualquer método de autenticação de nuvem – [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) ou [Autenticação de passagem](how-to-connect-pta.md).
  - Pode ser distribuído a alguns ou todos os seus usuários usando a Política de Grupo.
  - Registre dispositivos não Windows 10 no Azure AD sem a necessidade de nenhuma infraestrutura do AD FS. Esse recurso deve usar a versão 2.1 ou posterior do [cliente workplace-join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Destaques do recurso

- O nome de usuário de conexão pode ser o nome de usuário local padrão (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (`Alternate ID`). Ambos casos de uso funcionam porque o SSO Contínuo usa a declaração `securityIdentifier` no tíquete do Kerberos para pesquisar o objeto de usuário correspondente no Azure AD.
- O SSO Contínuo é um recurso oportunista. Se ele falhar por qualquer motivo, a experiência de entrada do usuário retornará ao comportamento normal, ou seja, o usuário precisará digitar sua senha na página de entrada.
- Se um aplicativo (por exemplo, `https://myapps.microsoft.com/contoso.com`) encaminha um parâmetro `domain_hint` (OpenID Connect) ou `whr` (SAML)-identificando seu locatário ou @no__t parâmetro-3-identificando o usuário, em sua solicitação de entrada do Azure AD, os usuários são conectados automaticamente sem eles inserindo nomes de acessadores ou senhas.
- Os usuários também terão uma experiência de logon silenciosa se um aplicativo (por exemplo, `https://contoso.sharepoint.com`) enviar solicitações de entrada para pontos de extremidade do Azure AD configurados como locatários, ou seja, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>`-em vez do ponto de extremidade comum do Azure AD – ou seja, `https://login.microsoftonline.com/common/<...>`.
- Há suporte para saída. Isso permite que os usuários escolham outra conta do Azure AD para conectar, em vez de conectar automaticamente usando o SSO contínuo automaticamente.
- Clientes do Office 365 Win32 (Outlook, Word, Excel e outros) com as versões 16.0.8730.xxxx e superiores têm suporte com o uso de um fluxo não interativo. Para o OneDrive, você precisará ativar o [recurso de Configuração silenciosa do OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de logon silenciosa.
- Isso pode ser habilitado por meio do Azure AD Connect.
- Essa é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.
- Há suporte para ele em clientes baseados em navegador da Web e clientes do Office que dão suporte à [autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) em plataformas e navegadores que sejam compatíveis com a autenticação Kerberos:

| Sistema operacional\Navegador |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sim\*|Sim|Sim|Sim\*\*\*|N/D
|Windows 8.1|Sim\*|N/D|Sim|Sim\*\*\*|N/D
|Windows 8|Sim\*|N/D|Sim|Sim\*\*\*|N/D
|Windows 7|Sim\*|N/D|Sim|Sim\*\*\*|N/D
|Windows Server 2012 R2 ou acima|Sim\*\*|N/D|Sim|Sim\*\*\*|N/D
|Mac OS X|N/D|N/D|Sim\*\*\*|Sim\*\*\*|Sim\*\*\*


\*Exige o Internet Explorer versões 10 ou superior

\*\*Exige o Internet Explorer versões 10 ou superior. Desabilitar Modo Protegido Avançado

\*\*\*Exige [configuração adicional](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Para o Windows 10, a recomendação é usar o [Ingresso do Azure AD](../active-directory-azureadjoin-overview.md) para obter a experiência ideal de logon único com o Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido** ](how-to-connect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Plano de Implantação**](https://aka.ms/deploymentplans/sso) - Plano de implantação passo a passo.
- [**Aprofundamento técnico**](how-to-connect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](how-to-connect-sso-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](tshoot-connect-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.

