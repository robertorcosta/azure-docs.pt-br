---
title: Adicionar um aplicativo web API - Azure Active Directory B2C | Microsoft Docs
description: Aprenda a adicionar um aplicativo de API web ao seu inquilino B2C do Active Directory.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e6dbf3d6fd5a43ab2d075c193c5bc589dc3566a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190170"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar um aplicativo de API Web ao locatário do Azure Active Directory B2C

 Registre os recursos da API web em seu inquilino para que eles possam aceitar e responder às solicitações de aplicativos clientes que apresentam um token de acesso. Este artigo mostra como registrar uma API web no Azure Active Directory B2C (Azure AD B2C).

Para registrar um aplicativo no locatário do Azure Active Directory B2C, você pode usar a experiência **Aplicativos** atual ou a nossa nova experiência **Registros de Aplicativo (versão prévia)** unificada. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Faça login no [portal Azure](https://portal.azure.com).
2. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos**e selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
7. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Em seu aplicativo de produção, você pode definir `https://localhost:44332`a URL de resposta a um valor como . Para fins de teste, defina a URL de resposta para `https://jwt.ms`.
8. Para o **ID do aplicativo URI**, insira o identificador usado para sua API da web. O URI do identificador completo, incluindo o domínio, é gerado para você. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *webapi1*.
1. Em **URI de Redirecionamento**, selecione **Web** e depois insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Em um aplicativo de produção, você pode definir `https://localhost:5000`o URI redirecionar um ponto final como . Durante o desenvolvimento ou teste, `https://jwt.ms`você pode configurá-lo para , um aplicativo web de propriedade da Microsoft que exibe o conteúdo decodificado de um token (o conteúdo do token nunca sair do seu navegador). Você pode adicionar e modificar URIs de redirecionamento em seus aplicativos registrados a qualquer momento.
1. Selecione **Registrar**.
1. Registre o **ID de aplicativo (cliente)** para uso no código da Sua API web.

Se você tiver um aplicativo que implemente o fluxo de concessão implícito, por exemplo, um APLICATIVO de página única baseado em JavaScript, você poderá habilitar o fluxo seguindo estas etapas:

1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. Em **Concessão implícita**, selecione ambas as caixas de seleção **Tokens de acesso** e **Tokens de ID**.
1. Selecione **Salvar**.

* * *

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, os usuários da API Web podem ter tanto acesso de leitura quanto de gravação ou somente acesso de leitura. Neste tutorial, você usa escopos para definir as permissões de leitura e gravação da API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, é necessário conceder permissões de aplicativo à API. Por exemplo, no [Tutorial: Registre um aplicativo no Azure Active Directory B2C](tutorial-register-applications.md), um aplicativo web chamado *webapp1* está registrado no Azure AD B2C. Você pode usar aplicativo para chamar a API da Web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Seu aplicativo é registrado para chamar a API Web protegida. Um usuário autentica-se com o Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.
