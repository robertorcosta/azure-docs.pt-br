---
title: Publicar aplicativos cliente nativos - Azure AD | Microsoft Docs
description: Aborda como habilitar aplicativos clientes nativos para se comunicar com o Conector de Proxy do aplicativo Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais.
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
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159277"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Como permitir que aplicativos clientes nativos interajam com aplicativos proxy

Você pode usar o Proxy do Aplicativo Azure Active Directory (Azure AD) para publicar aplicativos da Web, mas também pode ser usado para publicar aplicativos clientes nativos que estão configurados com a ADAL (AD Authentication Library, biblioteca de autenticação azure ad). Os aplicativos cliente nativos diferem dos aplicativos Web porque eles são instalados em um dispositivo, enquanto os aplicativos Web são acessados por meio de um navegador.

Para suportar aplicativos de cliente nativo, o Proxy do aplicativo aceita tokens emitidos pelo Azure AD que são enviados no cabeçalho. O serviço Proxy do aplicativo faz a autenticação para os usuários. Esta solução não usa tokens de aplicativos para autenticação.

![Relacionamento entre usuários finais, Azure AD e aplicativos publicados](./media/application-proxy-configure-native-client-application/richclientflow.png)

Para publicar aplicativos nativos, use a Biblioteca de Autenticação Ad do Azure, que cuida da autenticação e suporta muitos ambientes de clientes. O Proxy de Aplicativo se encaixa no [cenário de Aplicativo Nativo para API Web](../azuread-dev/native-app.md).

Este artigo explica as quatro etapas para publicar um aplicativo nativo com o Proxy de Aplicativo e a Biblioteca de Autenticação do Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Passo 1: Publique seu aplicativo proxy

Publique seu aplicativo de proxy como faria com qualquer outro aplicativo e atribua aos usuários acesso a ele. Para obter mais informações, consulte [Publicar aplicativos com proxy de aplicativo](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Passo 2: Registre seu aplicativo nativo

Agora você precisa registrar seu aplicativo no Azure AD, da seguinte forma:

1. Faça login no portal do Diretório Ativo do [Azure](https://aad.portal.azure.com/). O **painel de controle** do centro de administração do Diretório Ativo do **Azure** é exibido.
1. Na barra lateral, selecione **Azure Active Directory**. A página de visão geral **do Diretório Ativo do Azure** é exibida.
1. Na barra lateral de visão geral do Azure AD, selecione **registros do Aplicativo**. A lista de todos os registros do aplicativo aparece.
1. Selecione **Novo registro**. A **página Registrar uma** página de aplicativo é exibida.

   ![Crie um novo registro de aplicativo no portal Azure](./media/application-proxy-configure-native-client-application/create.png)

1. No **título Nome,** especifique um nome de exibição voltado para o usuário para o seu aplicativo.
1. No **título 'Tipos de conta suportados',** selecione um nível de acesso usando essas diretrizes:

   - Para direcionar apenas contas internas à sua organização, selecione **Apenas Contas neste diretório organizacional**.
   - Para atingir apenas clientes de negócios ou educacionais, selecione **Contas em qualquer diretório organizacional**.
   - Para direcionar o conjunto mais amplo de identidades da Microsoft, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.

1. Na posição **Uri redirecionamento,** selecione **Cliente público (celular & desktop)** e digite o URI de redirecionamento para sua aplicação.
1. Selecione e leia as Políticas de **Plataforma da Microsoft**e selecione **Registrar**. Uma página de visão geral do novo registro do aplicativo é criada e exibida.

Para obter informações mais detalhadas sobre a criação de um novo registro de aplicativos, consulte [Integrando aplicativos com o Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passo 3: Conceda acesso ao seu aplicativo proxy

Agora que você registrou seu aplicativo nativo, você pode dar-lhe acesso a outros aplicativos em seu diretório, neste caso para acessar o aplicativo proxy. Para permitir que o aplicativo nativo seja exposto ao aplicativo proxy:

1. Na barra lateral da nova página de registro de aplicativos, selecione **permissões de API**. A **página de permissões da API** para o novo registro do aplicativo é exibida.
1. Selecione **Adicionar uma permissão**. A **página Depermissões aPI** de solicitação é exibida.
1. Na **configuração Selecionar uma API,** selecione **APIs que minha organização usa**. Uma lista é exibida, contendo os aplicativos em seu diretório que expõem APIs.
1. Digite a caixa de pesquisa ou role para encontrar o aplicativo proxy que você publicou na [Etapa 1: Publique seu aplicativo proxy](#step-1-publish-your-proxy-application)e, em seguida, selecione o aplicativo proxy.
1. No **Tipo de permissões que seu aplicativo exige?** Se o aplicativo nativo precisar acessar a API do aplicativo proxy como usuário de entrada, escolha **permissões delegadas**.
1. No **título Seleto de permissões,** selecione a permissão desejada e selecione **Adicionar permissões**. A página **de permissões de API** para seu aplicativo nativo agora mostra o aplicativo proxy e a API de permissão que você adicionou.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Etapa 4: Edite a Biblioteca de Autenticação do Active Directory

Edite o código de aplicativo nativo no contexto de autenticação da ADAL (Biblioteca de Autenticação do Active Directory) para incluir o seguinte texto:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

As informações necessárias no código de amostra podem ser encontradas no portal Azure AD, da seguinte forma:

| Informações necessárias | Como encontrá-lo no portal Azure AD |
| --- | --- |
| \<ID do inquilino> | **ID do diretório de** > **propriedades** > do diretório ativo do**Azure** |
| \<Url externo do proxy App> | **Aplicativos corporativos** > *seu proxy proxy* > **proxy Url** > **externo** |
| \<ID do aplicativo nativo> | **Aplicativos corporativos** > seu**ID de aplicativo** de**propriedades** > *de aplicativos* > nativos |
| \<Redirecionar o URI do aplicativo nativo> | **O Azure Active Directory** > **App cadastra** > *seu aplicativo* > nativo**Redirecionar URIs** |
| \<> de url da API do aplicativo proxy | **O Azure Active Directory** > **App cadastra** > *seu aplicativo* > nativo**Permissões** > **de API / NOME PERMISSÕES** |

Depois de editar o ADAL com esses parâmetros, seus usuários podem autenticar aplicativos clientes nativos mesmo quando eles estão fora da rede corporativa.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o fluxo de aplicativos nativo, consulte [aplicativos nativos no Azure Active Directory](../azuread-dev/native-app.md).

Saiba como configurar [o login único em aplicativos no Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
