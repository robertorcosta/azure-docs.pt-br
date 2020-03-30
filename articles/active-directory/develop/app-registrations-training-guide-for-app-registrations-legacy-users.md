---
title: Novo guia de treinamento de registro de aplicativo do portal do Azure
description: Apresenta a nova experiência de registro do portal Azure
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154569"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Novo guia de treinamento de registro de aplicativo do portal do Azure

Você pode encontrar muitas melhorias na experiência de registro do [novo App](https://go.microsoft.com/fwlink/?linkid=2083908) no portal Azure. Se você está familiarizado com a experiência de registros do App (legado) no portal Azure, use este guia de treinamento para começar a usar a nova experiência.

No Azure Active Directory, a nova experiência de registro de inscrição descrita aqui está geralmente disponível (GA). No Azure Active Directory B2C (Azure AD B2C), essa experiência está em pré-visualização.

## <a name="key-changes"></a>Principais mudanças

- Os registros de aplicativos não se limitam a ser um *aplicativo web/API* ou um aplicativo *nativo.* Você pode usar o mesmo registro de aplicativo para todos esses aplicativos registrando as respectivas URIs de redirecionamento.

- A experiência herdada suportava aplicativos que se login usando apenas contas organizacionais (Azure AD). Os aplicativos foram registrados como inquilinoúnico. Os aplicativos suportavam apenas contas organizacionais do diretório em que o aplicativo estava registrado. Os aplicativos podem ser modificados para serem multi-inquilinos e suportar todas as contas organizacionais. A nova experiência permite que você registre aplicativos que podem suportar essas opções, bem como uma terceira opção: todas as contas organizacionais, bem como contas pessoais da Microsoft.

- A experiência do legado só estava disponível quando assinada no portal Azure usando uma conta organizacional. Com a nova experiência, você pode usar contas pessoais da Microsoft que não estão associadas a um diretório.

## <a name="list-of-applications"></a>Lista de aplicativos

A nova lista de aplicativos mostra aplicativos que foram registrados através da experiência de registros de aplicativos legados no portal Azure. Esses aplicativos se login usando contas AD do Azure. A nova lista de aplicativos também mostra aplicativos cadastrados no Portal de Registro de Aplicativos. Esses aplicativos se login usando o Azure AD e contas pessoais da Microsoft.

>[!NOTE]
>O Portal de Registro de Candidaturas foi preterido.

A nova lista de aplicativos não tem uma coluna **de tipo de aplicativo** porque um único registro de aplicativo pode ser de vários tipos. A lista tem duas colunas adicionais: **Criadas** e **Certificados & segredos**. **Certificados & segredos** mostram o status das credenciais que foram registradas no aplicativo. Os status incluem **Corrente,** **Expiração em breve** **e Expirado**.

## <a name="new-app-registration"></a>Registro de novo aplicativo

Na experiência legado, para registrar um aplicativo que você foi obrigado a fornecer: **Nome,** **tipo de aplicativo**e **URL/Redirecionar URI**. Os aplicativos que foram criados foram o Azure AD apenas aplicativos de inquilino único. Eles só suportavam contas organizacionais do diretório em que o aplicativo estava registrado.

Na nova experiência, você deve fornecer um **Nome** para o aplicativo e escolher os **tipos de conta suportadas.** Você pode fornecer opcionalmente um **URI redirecionado**. Se você fornecer um URI redirecionado, você precisará especificar se é web/público (móvel e desktop). Para obter mais informações, consulte [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](quickstart-register-app.md). Para Azure AD B2C, consulte [Registre um aplicativo no Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Diferenças entre o Portal de Inscrição e a página de registros de aplicativos

### <a name="the-legacy-properties-page"></a>A página Propriedades legado

A experiência do legado tinha uma página **propriedades.** **As propriedades** tinham os seguintes campos:

- **Nome**
- **ID do objeto**
- **ID de aplicação**
- **URI da ID do Aplicativo**
- **Logotipo**
- **URL da home page**
- **Logout URL**
- **TERMOS de URL de serviço**
- **URL de declaração de privacidade**
- **Tipo de aplicação**
- **Multi-inquilino**

A nova experiência não tem essa página. Aqui é onde você pode encontrar a funcionalidade equivalente:

- **Nome**, **Logotipo, URL da página inicial,** **URL termos de serviço**e URL de **declaração de privacidade** estão agora na página de **Branding** do aplicativo. **Logo**
- **O ID de ID** de ID de ID de ID de ID de Objeto e **Aplicativo (cliente)** está na **página Visão Geral.**
- A funcionalidade controlada pelo **alternador Multi-inquilino** na experiência legado foi substituída por **tipos de conta suportados** na página **Autenticação.** Para obter mais informações, consulte [Quickstart: Modifique as contas suportadas por um aplicativo](quickstart-modify-supported-accounts.md).
- **O URL de logout** está agora na página **Autenticação.**
- **O tipo de aplicativo** não é mais um campo válido. Em vez disso, redirecione os URIs, que você pode encontrar na página **Autenticação,** determine quais tipos de aplicativos são suportados.
- **O App ID URI** agora é chamado **de ID de aplicativo URI** e você pode encontrá-lo em Expor uma **API**. Na experiência do legado, esta propriedade foi registrada `https://{tenantdomain}/{appID}`automaticamente usando `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`o seguinte formato: , por exemplo, . Na nova experiência, é autogerado `api://{appID}`como , mas precisa ser explicitamente salvo. Nos inquilinos Azure AD B2C, o `https://{tenantdomain}/{appID}` formato ainda é usado.

### <a name="reply-urlsredirect-urls"></a>Responder URLs/Redirecionar URls

Na experiência do legado, um aplicativo tinha uma página **de URLs de resposta.** Na nova experiência, urls de resposta podem ser encontrados na página de **Autenticação** de um aplicativo. Eles agora são chamados de **URIs de redirecionamento.**

O formato para redirecionar URIs foi alterado. Eles são obrigados a ser associados a um tipo de aplicativo, seja web ou público. Por razões de `http://` segurança, curingas e *http://localhost*esquemas não são suportados, exceto para .

### <a name="keyscertificates--secrets"></a>Chaves/Certificados & segredos

Na experiência do legado, um aplicativo tinha página **Keys.** Na nova experiência, foi renomeada para **Certificados & segredos**.

**As chaves públicas** são agora referidas como **Certificados**. **As senhas** agora são referidas como **segredos do Cliente**.

### <a name="required-permissionsapi-permissions"></a>Permissões necessárias/permissões de API

Na experiência do legado, um aplicativo tinha uma página **de permissões necessárias.** Na nova experiência, foi renomeado para **permissões de API.**

Quando você selecionou uma API na experiência do legado, você pode escolher entre uma pequena lista de APIs da Microsoft. Você também pode procurar através de diretores de serviço no inquilino. Na nova experiência, você pode escolher entre várias guias: **APIs microsoft**, **APIs que minha organização usa**ou Minhas **APIs**. A barra de pesquisa em **APIs minha organização** usa pesquisas de guias através de diretores de serviço no inquilino.

> [!NOTE]
> Você não verá esta guia se sua aplicação não estiver associada a um inquilino. Para obter mais informações sobre como solicitar permissões, consulte [Quickstart: Configure um aplicativo cliente para acessar APIs da Web](quickstart-configure-app-access-web-apis.md).

A experiência do legado tinha um botão **de permissões Grant** na parte superior da página **de permissões solicitadas.** Na nova experiência, a página **de consentimento do Grant** tem um botão de consentimento de **admin Grant** na seção de permissões de **API** de um aplicativo. Há também algumas diferenças na forma como os botões funcionam.

Na experiência do legado, a lógica variava dependendo do usuário assinado e das permissões solicitadas. A lógica era:

- Se apenas as permissões de consentimento do usuário fossem solicitadas e o usuário assinado não fosse um admin, o usuário poderia conceder o consentimento do usuário para as permissões solicitadas.
- Se pelo menos uma permissão que requer consentimento administrativo foi solicitada e o usuário assinado não era um admin, o usuário teve um erro ao tentar conceder o consentimento.
- Se o usuário registrado era um admin, o consentimento do admin foi concedido para todas as permissões solicitadas.

Na nova experiência, apenas um admin pode conceder consentimento. Quando um admin seleciona **o consentimento do admin Grant,** o consentimento do admin é concedido a todas as permissões solicitadas.

## <a name="deleting-an-app-registration"></a>Excluindo um registro de aplicativo

Na experiência do legado, você pode excluir apenas aplicativos de inquilino único. O botão de exclusão foi desativado para aplicativos de vários inquilinos. Na nova experiência, você pode excluir aplicativos em qualquer estado, mas você deve confirmar a ação. Para obter mais informações, consulte [Quickstart: Remova um aplicativo registrado na plataforma de identidade Microsoft](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto do aplicativo

O legado e as novas experiências utilizam versões diferentes para o formato do JSON no editor do manifesto. Para obter mais informações, consulte [o manifesto do aplicativo Azure Active Directory](reference-app-manifest.md).

## <a name="new-ui"></a>Nova UI

A nova experiência adiciona controles de iu para as seguintes propriedades:

- A página **autenticação** tem`oauth2AllowImplicitFlow`fluxo de **concessão implícito** (). Ao contrário da experiência de legado, você pode habilitar **tokens de acesso** ou **tokens de ID,** ou ambos.
- A **página Expor uma API** contém **Escopos definidos por esta API** (`oauth2Permissions`) e aplicativos de **clienteautorizados** ().`preAuthorizedApplications` Para obter mais informações sobre como configurar um aplicativo para ser uma API web e expor permissões/escopos, consulte [Quickstart: Configure um aplicativo para expor APIs da Web](quickstart-configure-app-expose-web-apis.md).
- A **página Marca** contém o domínio **'Editor'.** O domínio do editor é exibido aos usuários no [prompt de consentimento do aplicativo](application-consent-experience.md). Para obter mais informações, [consulte Como: Configurar o domínio do editor de um aplicativo](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

- O formato dos segredos do cliente (senhas de aplicativo) é diferente do da experiência legada e pode quebrar a CLI.
- A alteração do valor para contas suportadas não é suportada na ui. Você precisa usar o manifesto do aplicativo, a menos que esteja alternando entre o Azure AD e o multi-inquilino.
