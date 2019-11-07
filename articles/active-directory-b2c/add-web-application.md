---
title: Adicionar um aplicativo de API Web-Azure Active Directory B2C | Microsoft Docs
description: Saiba como adicionar um aplicativo de API Web ao seu locatário Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 37cb242f667190fcd29bed1b7a82ca44ba2c94e9
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641555"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar um aplicativo de API Web ao locatário do Azure Active Directory B2C

 Registre recursos da API Web em seu locatário para que eles possam aceitar e responder a solicitações por aplicativos cliente que apresentem um token de acesso. Este artigo mostra como registrar uma API da Web no Azure Active Directory B2C (Azure AD B2C).

Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Azure AD B2C**.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
7. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Em seu aplicativo de produção, você pode definir a URL de resposta para um valor como `https://localhost:44332`. Para fins de teste, defina a URL de resposta como `https://jwt.ms`.
8. Para o **ID do aplicativo URI**, insira o identificador usado para sua API da web. O URI do identificador completo, incluindo o domínio, é gerado para você. Por exemplo: `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *webapi1*.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Em um aplicativo de produção, você pode definir o URI de redirecionamento como um ponto de extremidade `https://localhost:5000`. Durante o desenvolvimento ou teste, você pode defini-lo como `https://jwt.ms`, um aplicativo da Web da Microsoft que exibe o conteúdo decodificado de um token (o conteúdo do token nunca sai do seu navegador). Você pode adicionar e modificar URIs de redirecionamento em seus aplicativos registrados a qualquer momento.
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso no código da sua API Web.

Se você tiver um aplicativo que implementa o fluxo de concessão implícita, por exemplo, um aplicativo de página única (SPA) baseado em JavaScript, você poderá habilitar o fluxo seguindo estas etapas:

1. Em **gerenciar**, selecione **autenticação**.
1. Selecione **experimentar a nova experiência** (se mostrado).
1. Em **concessão implícita**, marque as caixas de seleção **tokens de acesso** e **tokens de ID** .
1. Selecione **Salvar**.

* * *

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, os usuários da API Web podem ter tanto acesso de leitura quanto de gravação ou somente acesso de leitura. Neste tutorial, você usa escopos para definir as permissões de leitura e gravação da API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, é necessário conceder permissões ao aplicativo para a API. Por exemplo, no [tutorial: registrar um aplicativo no Azure Active Directory B2C](tutorial-register-applications.md), um aplicativo Web chamado *webapp1* é registrado em Azure ad B2C. Você pode usar aplicativo para chamar a API da Web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Seu aplicativo é registrado para chamar a API Web protegida. Um usuário autentica-se com o Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.
