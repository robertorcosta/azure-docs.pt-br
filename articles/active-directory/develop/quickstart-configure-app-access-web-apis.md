---
title: 'Início Rápido: Acessar APIs Web para aplicativo – plataforma de identidade da Microsoft | Azure'
description: Neste início rápido, configure um aplicativo registrado na plataforma de identidade da Microsoft para incluir URIs de redirecionamento, credenciais ou permissões para acessar as APIs Web.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 210ed5b8ad53fd59a46e160fe5fc72633d115d44
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082315"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Início Rápido: Configure um aplicativo cliente para acessar APIs Web

Neste início rápido, você adicionará URIs de redirecionamento, credenciais ou permissões para acessar APIs Web para seu aplicativo. Um aplicativo cliente Web ou confidencial precisa estabelecer credenciais seguras para participar de um fluxo de concessão de autorização que requer autenticação. O método de autenticação padrão com suporte no portal do Azure é a ID do Cliente + chave secreta. O aplicativo obtém um token de acesso durante esse processo.

Antes que um cliente possa acessar uma API Web exposta por um aplicativo de recurso, como a API do Microsoft Graph, a estrutura de consentimento verifica se o cliente obtém a concessão de permissão necessária para as permissões solicitadas. Por padrão, todos os aplicativos podem solicitar permissões da API do Microsoft Graph.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md).
* Examine [Permissões e consentimento no ponto de extremidade da plataforma de identidade da Microsoft](v2-permissions-and-consent.md).
* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Entrar no portal do Azure e selecionar o aplicativo

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito. Defina sua sessão do portal para o locatário do Azure AD desejado.
1. Pesquise **Azure Active Directory** e selecione-o. Em **Gerenciar**, selecione **Registros de aplicativo**.
1. Encontre e selecione o aplicativo que você deseja configurar. Depois de selecionar o aplicativo, você verá a página **Visão Geral** ou a página de registro principal.

Use os procedimentos a seguir para configurar seu aplicativo para acessar APIs Web.

## <a name="add-redirect-uris-to-your-application"></a>Adicionar URIs de redirecionamento ao aplicativo

Você pode adicionar URIs de redirecionamento personalizados e URIs de redirecionamento sugeridos ao seu aplicativo. Para adicionar um URI de redirecionamento personalizado para Web e aplicativos cliente públicos:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Autenticação**.
1. Localize **URIs de Redirecionamento**. Talvez seja necessário selecionar **Mudar para a experiência antiga**.
1. Selecione o tipo de aplicativo que você está criando: **Web** ou **Cliente público/nativo (móvel e desktop)** .
1. Insira o URI de redirecionamento para seu aplicativo.

   * Para aplicativos Web, informe a URL base do aplicativo. Por exemplo, `http://localhost:31544` pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usarão essa URL para entrar em um aplicativo cliente Web.
   * Para aplicativos públicos, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico para o aplicativo, por exemplo: `https://MyFirstApp`.
1. Clique em **Salvar**.

Para escolher entre os URIs de redirecionamento sugeridos para clientes públicos, siga estas etapas:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Autenticação**.
1. Localize **URIs de redirecionamento sugeridos para clientes públicos (móvel, desktop)** . Talvez seja necessário selecionar **Mudar para a experiência antiga**.
1. Selecione um ou mais URIs de redirecionamento para seu aplicativo. Você também pode inserir um URI de redirecionamento personalizado. Se você não tiver certeza do que usar, confira a documentação da biblioteca.
1. Clique em **Salvar**.

Determinadas restrições se aplicam a URIs de redirecionamento. Para obter mais informações, confira [Limitações e restrições de URI de redirecionamento/URL de resposta](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

> [!NOTE]
> Experimente a nova experiência de configurações de **Autenticação**, na qual você pode definir configurações para seu aplicativo com base na plataforma ou no dispositivo que você deseja ter como destino.
>
> Para ver essa exibição, selecione **Experimentar a nova experiência** na página **Autenticação**.
>
> ![Clique em "Experimentar a nova experiência" para ver a exibição de configuração de Plataforma](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Isso levará você para a [nova página de **Configurações de plataforma**](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Definir configurações avançadas para seu aplicativo

Dependendo do aplicativo que você está registrando, há algumas configurações adicionais que talvez você precise configurar, como:

* **URL de Logoff**.
* Para aplicativos de página única, você pode habilitar a **Concessão implícita** e selecionar os tokens que você deseja que o ponto de extremidade da autorização emita.
* Para aplicativos para desktop adquirem tokens usando a Autenticação Integrada do Windows, o fluxo de código de dispositivo ou nome de usuário/senha na seção **Tipo de cliente padrão**, defina a configuração **Tratar aplicativo como cliente público** como **Sim**.
* Para aplicativos herdados que estavam usando o Live SDK para integração com o serviço de conta Microsoft, configure o **suporte ao Live SDK**. Novos aplicativos não precisam dessa configuração.
* **Tipo de cliente padrão**.
* **Tipos de conta com suporte**.

### <a name="modify-supported-account-types"></a>Modificar tipos de conta com suporte

Os **Tipos de conta com suporte** especificam quem pode usar o aplicativo ou acessar a API.

Se você tiver configurado os tipos de conta com suporte quando registrou o aplicativo, só poderá alterar essa configuração usando o editor de manifesto do aplicativo se:

* Você alterar os tipos de conta de **AzureADMyOrg** ou **AzureADMultipleOrgs** para **AzureADandPersonalMicrosoftAccount**, ou o oposto, ou
* Você alterar os tipos de conta de **AzureADMyOrg** para **AzureADMultipleOrgs** ou o oposto.

Para alterar os tipos de conta com suporte para um registro de aplicativo existente, atualize a chave `signInAudience`. Para obter mais informações, confira [Configurar o manifesto do aplicativo](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Definir configurações de plataforma para seu aplicativo

![Definir configurações para seu aplicativo com base na plataforma ou no dispositivo](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Para definir as configurações do aplicativo com base na plataforma ou no dispositivo que você está direcionando:

1. Na página **Configurações da plataforma**, selecione **Adicionar uma plataforma** e escolha entre as opções disponíveis.

   ![Mostra a página Configurar plataformas](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Insira as informações de configurações com base na plataforma selecionada.

   | Plataforma                | Definições de configuração            |
   |-------------------------|-----------------------------------|
   | **Web**              | Insira o **URI de redirecionamento** para seu aplicativo. |
   | **iOS/macOS**              | Insira a **ID do Pacote** do aplicativo, que pode ser encontrada no XCode em Info.plist ou nas Configurações de Build. Adicionar a ID do pacote cria automaticamente um URI de redirecionamento para o aplicativo. |
   | **Android**          | Forneça o **Nome do pacote** do aplicativo, que pode ser encontrado no arquivo AndroidManifest.xml.<br/>Gere e insira o **Hash de assinatura**. Adicionar o hash de assinatura cria automaticamente um URI de redirecionamento para o aplicativo.  |
   | **Aplicativos móveis e para desktop**  | Opcional. Selecione um dos **URIs de redirecionamento sugeridos** se você estiver criando aplicativos para desktop e dispositivos.<br/>Opcional. Insira um **URI de redirecionamento personalizado**, que é usado como o local para o qual o Azure AD redirecionará os usuários em resposta às solicitações de autenticação. Por exemplo, para aplicativos .NET Core em que você deseja interação, use `http://localhost`. |

   > [!NOTE]
   > No AD FS (Serviços de Federação do Active Directory) e no Azure AD B2C, você também deve especificar um número da porta.  Por exemplo: `http://localhost:1234`. 

   > [!IMPORTANT]
   > Para aplicativos móveis que não estão usando a MSAL (Biblioteca de Autenticação da Microsoft) mais recente ou não estão usando um agente, você deve configurar os URIs de redirecionamento para esses aplicativos em **Desktop + dispositivos**.

Dependendo da plataforma escolhida, poderá haver configurações adicionais a serem configuradas. Para aplicativos **Web**, você pode:

* Adicionar mais URIs de redirecionamento
* Configure a **concessão implícita** para selecionar os tokens que você deseja que sejam emitidos pelo ponto de extremidade da autorização:

  * Para aplicativos de página única, selecione **Tokens de acesso** e **Tokens de ID**
  * Para aplicativos Web, selecione **Tokens de ID**

## <a name="add-credentials-to-your-web-application"></a>Adicionar credenciais ao aplicativo Web

Para adicionar uma credencial ao seu aplicativo Web, adicione um certificado ou crie um segredo do cliente. Para adicionar um certificado:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Certificados e segredos**.
1. Selecione **Carregar certificado**.
1. Selecione o arquivo que você gostaria de carregar. Ele deve ser um dos seguintes tipos de arquivo: .cer, .pem, .crt.
1. Selecione **Adicionar**.

Para adicionar um segredo do cliente:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Certificados e segredos**.
1. Selecione **Novo segredo do cliente**.
1. Adicione uma descrição para o segredo do cliente.
1. Selecione uma duração.
1. Selecione **Adicionar**.

> [!NOTE]
> Depois que você salvar as alterações de configuração, a coluna mais à direita conterá o valor do segredo do cliente. **Copie o valor** para uso no código do aplicativo cliente, já que ele não ficará acessível depois que você sair da página.

## <a name="add-permissions-to-access-web-apis"></a>Adicionar permissões para acessar APIs Web

A [permissão Entrar e ler perfil do usuário da API do Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) está selecionada por padrão. Você pode selecionar entre [dois tipos de permissões](developer-glossary.md#permissions) para cada API Web:

* **Permissões de aplicativo**. Seu aplicativo cliente precisa acessar a API Web diretamente por si só, sem contexto de usuário. Esse tipo de permissão requer o consentimento do administrador. Essa permissão não está disponível para aplicativos cliente móveis e para desktop.
* **Permissões delegadas**. o aplicativo cliente precisa acessar a API Web como o usuário conectado, mas com acesso limitado pela permissão selecionada. Esse tipo de permissão pode ser concedido por um usuário, a menos que a permissão exija consentimento do administrador.

  > [!NOTE]
  > Adicionar uma permissão delegada a um aplicativo não dá automaticamente consentimento aos usuários no locatário. Os usuários devem consentir manualmente para as permissões delegadas adicionadas em runtime, a menos que o administrador dê consentimento em nome de todos os usuários.

Para adicionar permissões para acessar as APIs do recurso do cliente:

1. Na página **Visão Geral** do aplicativo, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Por padrão, o modo de exibição permite que você selecione das **APIs da Microsoft**. Selecione a seção de APIs desejada:

    * **APIs da Microsoft**. Permite que você selecione permissões para APIs da Microsoft, como o Microsoft Graph.
    * **APIs que a minha organização usa**. Permite que você selecione as permissões para as APIs que sua organização expõe ou para APIs com as quais sua organização está integrada.
    * **Minhas APIs**. Permite que você selecione as permissões para as APIs expostas.

1. Depois de selecionar as APIs, você verá a página **Solicitar Permissões de API**. Se a API expõe permissões delegadas e de aplicativo, selecione o tipo de permissão de que seu aplicativo precisa.
1. Quando terminar, selecione **Adicionar permissões**.

Volte para a página **Permissões de API**. As permissões foram salvas e adicionadas à tabela.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Noções básicas de permissões de API e interface do usuário de consentimento do administrador

### <a name="configured-permissions"></a>Permissões configuradas

Esta seção mostra as permissões que foram explicitamente configuradas no objeto de aplicativo. Essas permissões fazem parte da lista de acesso de recursos necessária do aplicativo. Você pode adicionar ou remover permissões desta tabela. Como administrador, você também pode conceder ou revogar o consentimento do administrador para um conjunto de permissões de uma API ou permissões individuais.

### <a name="other-permissions-granted"></a>Outras permissões concedidas

Caso seu aplicativo esteja registrado em um locatário, é possível que você veja uma seção adicional chamada **Outras permissões concedidas ao locatário**. Esta seção mostra as permissões concedidas para o locatário que não foram explicitamente configuradas no objeto de aplicativo. Essas permissões foram solicitadas e consentidas dinamicamente. Essa seção só será exibida se houver pelo menos uma permissão aplicável.

Convém adicionar um conjunto de permissões de uma API ou permissões individuais que aparecem nessa seção à seção **Permissões configuradas**. Como administrador, você também pode revogar o consentimento do administrador para APIs individuais ou permissões nesta seção.

### <a name="admin-consent-button"></a>Botão de consentimento do administrador

Se seu aplicativo estiver registrado em um locatário, você verá um botão **Dar consentimento do administrador ao locatário**. Ele estará desabilitado se você não for um administrador ou se nenhuma permissão tiver sido configurada para o aplicativo.
Esse botão permite que um administrador dê o consentimento do administrador às permissões configuradas para o aplicativo. Um clique no botão de consentimento do administrador inicializará uma nova janela com uma solicitação de consentimento mostrando todas as permissões configuradas.

> [!NOTE]
> Há um atraso entre as permissões que estão sendo configuradas para o aplicativo e sua exibição na solicitação de consentimento. Se você não vir todas as permissões configuradas na solicitação de consentimento, feche-a e inicialize-a novamente.

Se você tiver permissões que foram concedidas sem serem configuradas, o botão de consentimento do administrador solicitará que você lide com essas permissões. Você pode adicioná-las às permissões configuradas ou pode removê-las.

A solicitação de consentimento tem a opção **Aceitar** ou **Cancelar**. Selecione **Aceitar** para dar consentimento do administrador. Se você selecionar **Cancelar**, o consentimento do administrador não será concedido. Uma mensagem de erro informa que o consentimento foi recusado.

> [!NOTE]
> Há um atraso entre a solicitação de consentimento do administrador selecionando **Aceitar** na solicitação de consentimento e o status do consentimento do administrador refletido no portal.

## <a name="next-steps"></a>Próximas etapas

Passe para o próximo artigo para saber como expor APIs Web.
> [!div class="nextstepaction"]
> [Início Rápido: Configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md)

* Saiba mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, confira [Objetos de aplicativo e objetos de entidade de serviço](app-objects-and-service-principals.md).

* Para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Azure Active Directory, confira [Diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md).

* [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md)

* [Início Rápido: Modificar as contas que têm suporte de um aplicativo](quickstart-modify-supported-accounts.md)

* [Início Rápido: Remover um aplicativo registrado na plataforma de identidade da Microsoft](quickstart-remove-app.md)
