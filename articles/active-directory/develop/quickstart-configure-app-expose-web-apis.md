---
title: 'Início Rápido: Configurar um aplicativo para expor uma API Web | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você aprenderá a configurar um aplicativo para expor uma nova permissão ou escopo e função a fim de disponibilizar o aplicativo para aplicativos cliente.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830284"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Início Rápido: Configurar um aplicativo para expor uma API Web

Você pode desenvolver uma API Web e disponibilizá-la aos aplicativos cliente expondo [permissões/escopos](developer-glossary.md#scopes) e [funções](developer-glossary.md#roles) de acesso. Uma API Web corretamente configurada é disponibilizada assim como outras APIs Web da Microsoft, incluindo a API do Graph e as APIs do Office 365.

Neste início rápido, você aprende a configurar um aplicativo para expor um novo escopo e disponibilizá-lo para aplicativos cliente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Conclusão do [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Entrar no portal do Azure e selecionar o aplicativo

Antes de configurar o aplicativo, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, **Registros de aplicativo**.
1. Encontre e selecione o aplicativo que você deseja configurar. Depois de selecionar o aplicativo, você verá a página **Visão Geral** ou a página de registro principal.
1. Escolha qual método você deseja usar, a interface do usuário ou o aplicativo de manifesto, para expor um novo escopo:
    * [Expor um novo escopo pela interface do usuário](#expose-a-new-scope-through-the-ui)
    * [Expor um novo escopo ou função pelo manifesto do aplicativo](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Expor um novo escopo pela interface do usuário

[![Mostra como expor uma API usando a interface do usuário](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Para expor um novo escopo pela interface do usuário:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Expor uma API**.

1. Selecione **Adicionar um escopo**.

1. Se você não tiver definido um **URI da ID do aplicativo**, verá um prompt para inserir um. Insira o URI da ID do aplicativo ou use o fornecido e selecione **Salvar e continuar**.

1. Quando a página **Adicionar um escopo** for exibida, insira as informações do seu escopo:

    | Campo | Descrição |
    |-------|-------------|
    | **Nome do Escopo** | Insira um nome relevante para seu escopo.<br><br>Por exemplo, `Employees.Read.All`. |
    | **Quem pode consentir** | Selecione se esse escopo pode ser consentido por usuários, ou se é necessário ter o consentimento do administrador. Selecione **Somente administradores** para permissões com privilégios mais elevados. |
    | **Nome de exibição de consentimento do administrador** | Insira uma descrição significativa para o escopo, que os administradores verão.<br><br>Por exemplo, `Read-only access to Employee records` |
    | **Descrição do consentimento do administrador** | Insira uma descrição significativa para o escopo, que os administradores verão.<br><br>Por exemplo, `Allow the application to have read-only access to all Employee data.` |

    Se os usuários podem consentir com seu escopo, adicione também valores para os seguintes campos:

    | Campo | Descrição |
    |-------|-------------|
    | **Nome para exibição do consentimento do usuário** | Insira um nome significativo para seu escopo, que os usuários verão.<br><br>Por exemplo, `Read-only access to your Employee records` |
    | **Descrição de consentimento do usuário** | Insira uma descrição significativa para o escopo, que os usuários verão.<br><br>Por exemplo, `Allow the application to have read-only access to your Employee data.` |

1. Defina o **Estado** e selecione **Adicionar escopo** quando você terminar.

1. (Opcional) Para suprimir a solicitação de consentimento dos usuários do seu aplicativo para os escopos que você definiu, você pode "pré-autorizar" o aplicativo cliente a acessar sua API Web. Você *só* deve pré-autorizar os aplicativos cliente confiáveis, pois os usuários não terão a oportunidade de recusar o consentimento.
    1. Em **Aplicativos cliente autorizados**, selecione **Adicionar um aplicativo cliente**
    1. Insira a **ID do Aplicativo (cliente)** do aplicativo cliente que deseja pré-autorizar. Por exemplo, aquela de um aplicativo Web que você já registrou.
    1. Em **Escopos autorizados**, selecione os escopos para os quais deseja suprimir a solicitação de consentimento e escolha **Adicionar aplicativo**.

    Agora, o aplicativo cliente é um PCA (aplicativo cliente pré-autorizado), e os usuários não precisarão fornecer consentimento ao entrar nele.

1. Siga as etapas para [Verificar se que a API Web está exposta a outros aplicativos](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Expor um novo escopo ou função pelo manifesto do aplicativo

O manifesto do aplicativo serve como um mecanismo para atualizar a entidade do aplicativo que define os atributos de um registro de aplicativo do Azure AD.

[![Expor um novo escopo usando a coleção oauth2Permissions no manifesto](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Para expor um novo escopo editando o manifesto do aplicativo:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Manifesto**. Abre um editor de manifesto baseado na Web, permitindo a você **Editar** o manifesto dentro do portal. Opcionalmente, você pode selecionar **Baixar** e editar o manifesto localmente e, em seguida, usar **Carregar** para reaplicá-lo ao seu aplicativo.

    O exemplo a seguir mostra como expor um novo escopo chamado `Employees.Read.All` no recurso ou na API adicionando o seguinte elemento JSON à coleção `oauth2Permissions`.

    Gere o valor `id` programaticamente ou usando uma ferramenta de geração de GUID como [guidgen](https://www.microsoft.com/download/details.aspx?id=55984).

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

1. Ao terminar, clique em **Salvar**. Agora sua API Web está configurada para ser usada por outros aplicativos do seu diretório.
1. Siga as etapas para [Verificar se que a API Web está exposta a outros aplicativos](#verify-the-web-api-is-exposed-to-other-applications).

Para obter mais informações sobre a entidade de aplicativo e seu esquema, confira a documentação de referência do tipo de recurso [Aplicativo][ms-graph-application] do Microsoft Graph.

Para obter mais informações sobre o manifesto do aplicativo, incluindo sua referência de esquema, confira [Noções básicas sobre o manifesto de aplicativo do Azure AD](reference-app-manifest.md).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Verificar se a API Web está exposta a outros aplicativos

1. Volte ao locatário do Azure AD, selecione **Registros de aplicativo** e localize e escolha o aplicativo cliente que deseja configurar.
1. Repita as etapas definidas em [Configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md).
1. Quando chegar à etapa que [seleciona uma API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis), escolha o recurso (o registro do aplicativo de API Web).
    * Se você criou o registro do aplicativo de API Web usando o portal do Azure, ele é listado na guia **Minhas APIs**.
    * Se você permitiu que o Visual Studio criasse o registro do aplicativo de API Web durante a criação do projeto, o recurso de API está listado na guia **APIs que a minha organização usa**.

Depois de selecionar o recurso de API Web, você verá o novo escopo disponível para as solicitações de permissão do cliente.

## <a name="using-the-exposed-scopes"></a>Como usar os escopos expostos

Depois que um cliente está configurado adequadamente com permissões para acessar a sua API Web, o Azure AD pode emitir um token de acesso OAuth 2.0. Quando o cliente chama a API Web, ele apresenta o token de acesso que tem a declaração de escopo (`scp`) definida para as permissões solicitadas no seu registro de aplicativo.

É possível expor escopos adicionais posteriormente conforme a necessidade. Lembre-se de que a API Web pode expor vários escopos associados a uma variedade de funções diferentes. O recurso pode controlar o acesso à API Web em runtime, avaliando declarações de escopo (`scp`) no token de acesso OAuth 2.0 recebido.

Em seus aplicativos, o valor de escopo completo é uma concatenação do **URI da ID de aplicativo** da API Web (o recurso) e o **nome do escopo**.

Por exemplo, se o URI da ID do aplicativo da sua API Web for `https://contoso.com/api` e o nome do escopo for `Employees.Read.All`, o escopo completo será:

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Próximas etapas

Agora que você expôs sua API Web Configurando seus escopos, configure o registro do aplicativo cliente com permissão para acessar esses escopos.

> [!div class="nextstepaction"]
> [Configurar um registro de aplicativo para acesso à API Web](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
