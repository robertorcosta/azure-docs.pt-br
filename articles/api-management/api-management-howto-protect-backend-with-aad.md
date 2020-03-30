---
title: Proteja uma API usando o OAuth 2.0 com Gerenciamento de AAD e API
titleSuffix: Azure API Management
description: Saiba como proteger um back-end da API da Web com o Azure Active Directory e Gerenciamento de API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 8b396b782c1254b3229aeeb8e51b61cc744d6318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190375"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Proteger uma API usando OAuth 2.0 com o Azure Active Directory e o Gerenciamento de API

Este guia mostra como configurar sua instância do Gerenciamento de API do Azure para proteger uma API usando o protocolo OAuth 2.0 com o Microsoft Azure Active Directory (Azure AD). 

> [!NOTE]
> Este recurso está disponível nos níveis **Desenvolvedor,** **Padrão** e **Premium** de Gerenciamento de API.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir as etapas deste artigo, você precisa ter:
* Uma instância de Gerenciamento de API
* Uma API sendo publicada que use a instância de Gerenciamento de API
* Um locatário do Azure AD

## <a name="overview"></a>Visão geral

A seguir é apresentada uma visão geral das etapas:

1. Registrar um aplicativo (aplicativo de back-end) no Azure AD para representar a API.
2. Registrar outro aplicativo (aplicativo cliente) no Azure AD para representar um aplicativo cliente que precisa chamar a API.
3. No Azure AD, conceder permissões para permitir que o aplicativo cliente chame o aplicativo de back-end.
4. Configure o Console do Desenvolvedor para chamar a API usando a autorização do usuário OAuth 2.0.
5. Adicionar política **validate-jwt** para validar o token OAuth para cada solicitação de entrada.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrar um aplicativo no Azure AD para representar a API

Para proteger uma API com o Azure AD, a primeira etapa é registrar no Azure AD um aplicativo que represente a API. 

1. Acesse o [portal azure](https://portal.azure.com) para registrar sua inscrição. Pesquise e selecione **inscrições de APP**.

1. Selecione **Novo registro**. 

1. Quando a **página registrar uma página de aplicativo** for exibida, digite as informações de registro do seu aplicativo: 
    - Na seção **Nome,** digite um nome de aplicativo significativo que será exibido aos usuários do aplicativo, como *o aplicativo backend*. 
    - Na seção **Tipos de conta suportados,** selecione uma opção que se adapte ao seu cenário. 

1. Deixe a seção **Redirecionar URI** vazia.

1. Selecione **Registrar** para criar o aplicativo. 

1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior.

1. Selecione **Expor uma API** e defina o **URI de ID do aplicativo** com o valor padrão. Registre esse valor para depois.

1. Selecione **o botão Adicionar um escopo** para exibir a página Adicionar um **escopo.** Em seguida, crie um novo escopo que seja suportado pela API (por exemplo, `Files.Read`). Por fim, selecione o botão **Adicionar escopo** para criar o escopo. Repita esta etapa para adicionar todos os escopos suportados por sua API.

1. Quando os escopos forem criados, anote-os para uso em uma etapa subseqüente. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrar outro aplicativo no Azure AD para representar um aplicativo cliente

Todos os aplicativos cliente que chamam a API também precisam ser registrados como aplicativos no Microsoft Azure Active Directory. Neste exemplo, o aplicativo cliente é o Console desenvolvedor no portal de desenvolvedores de gerenciamento de API. É assim que se registra outro aplicativo no Microsoft Azure Active Directory para representar o Console do Desenvolvedor.

1. Acesse o [portal azure](https://portal.azure.com) para registrar sua inscrição. Pesquise e selecione **inscrições de APP**.

1. Selecione **Novo registro**.

1. Quando a **página registrar uma página de aplicativo** for exibida, digite as informações de registro do seu aplicativo: 
    - Na seção **Nome,** digite um nome de aplicativo significativo que será exibido para os usuários do aplicativo, como *client-app*. 
    - Na seção **Tipos de conta suportados,** selecione **Contas em qualquer diretório organizacional (Qualquer diretório Azure AD - Multitenant)**. 

1. Na seção **Redirecionar URI,** selecione `Web` e insira a URL `https://contoso5.portal.azure-api.net/signin`.

1. Selecione **Registrar** para criar o aplicativo. 

1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior.

Agora, crie um segredo de cliente para que este aplicativo seja usado em uma etapa subsequente.

1. Na lista de páginas para o aplicativo do cliente, selecione **Certificados & segredos**e selecione **Novo segredo do cliente**.

1. Em **Adicionar um segredo de cliente,** forneça uma **descrição**. Escolha quando a chave deve expirar e selecione **Adicionar**.

Quando o segredo for criado, observe o valor-chave para uso em uma etapa subseqüente. 

## <a name="grant-permissions-in-azure-ad"></a>Conceder permissões no Azure AD

Agora que você já registrou um aplicativo para representar a API e outro para representar o Console do Desenvolvedor, é preciso conceder permissões para permitir que o aplicativo de cliente chame o aplicativo de back-end.  

1. Vá ao [portal Azure](https://portal.azure.com) para conceder permissões ao seu aplicativo cliente. Pesquise e selecione **inscrições de APP**.

1. Escolha seu aplicativo cliente. Em seguida, na lista de páginas para o aplicativo, selecione **permissões de API**.

1. Selecione **Adicionar uma Permissão**.

1. Em **Selecionar uma API,** selecione **Minhas APIs**e, em seguida, encontre e selecione seu aplicativo backend.

1. Em **Permissões Delegadas,** selecione as permissões apropriadas para o aplicativo backend e selecione **Adicionar permissões**.

1. Opcionalmente, na página de permissões da **API,** **selecione o consentimento de admin grant \<para o seu nome de inquilino>** conceder consentimento em nome de todos os usuários neste diretório. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Habilitar a autorização de usuário OAuth 2.0 no Console do Desenvolvedor

Você já criou seus aplicativos no Azure AD e concedeu as permissões adequadas para permitir que o aplicativo cliente chame o aplicativo back-end. 

Neste exemplo, o Console do Desenvolvedor é o aplicativo cliente. As etapas a seguir descrevem como habilitar a autorização de usuário OAuth 2.0 no Console do Desenvolvedor. 

1. No portal Azure, navegue até a instância de gerenciamento de API.

1. Selecione **OAuth 2.0** > **Adicionar**.

1. Forneça um **Nome de exibição** e uma **Descrição**.

1. Para o URL da página de registro do `http://localhost` **cliente,** digite um valor de espaço reservado, como . O **URL da página de registro do Cliente** aponta para uma página que os usuários podem usar para criar e configurar suas próprias contas para provedores OAuth 2.0 que suportam isso. Neste exemplo, como os usuários não criam nem configuram contas próprias, é usado um espaço reservado.

1. Para **Tipos de concessão de autorização**, selecione **Código de autorização**.

1. Especifique a **URL de ponto de extremidade de autorização** e a **URL de ponto de extremidade de token**. Recupere esses valores da página **Pontos de extremidade** no locatário do Azure AD. Volte à página **Registros de aplicativo** e clique em **Pontos de extremidade**.


1. Copie o **ponto de extremidade da autorização OAuth 2.0** e cole-o na caixa de texto **URL do Ponto de Extremidade da Autorização**. Selecione **POST** no método de solicitação de autorização.

1. Copie o **ponto de extremidade do token OAuth 2.0** e cole-o na caixa de texto **URL do Ponto de Extremidade do Token**. 

    >[!IMPORTANT]
    > Você pode usar pontos finais **v1** ou **v2.** No entanto, dependendo da versão escolhida, a etapa abaixo será diferente. Recomendamos o uso de pontos finais v2. 

1. Se você usar pontos finais **v1,** adicione um parâmetro corporal chamado **recurso**. Para o valor deste parâmetro, use **o ID** do aplicativo back-end. 

1. Se você usar pontos finais **v2,** use o escopo criado para o aplicativo backend no campo **de escopo Padrão.**

1. Em seguida, especifique as credenciais do cliente. Essas são as credenciais para o aplicativo cliente.

1. Para **iD do cliente,** use o **ID** de aplicativo do aplicativo cliente-.

1. Para **Segredo do cliente**, use a chave que você criou para o aplicativo cliente anteriormente. 

1. Logo após o segredo do cliente, está a **URL de redirecionamento** para o tipo de concessão de código de autorização. Anote essa URL.

1. Selecione **Criar**.

1. Volte para o seu aplicativo de cliente e selecione **Autenticação**.

1. Em **UrIs redirecionados,** selecione o tipo como **Web,** cole o **redirect_url** em **Redirecionar URI**e, em seguida, salve.

Agora que você já configurou um servidor de autorização OAuth 2.0, o Console do Desenvolvedor pode obter tokens de acesso do Azure AD. 

A próxima etapa é habilitar a autorização de usuário do OAuth 2.0 para a sua API. Isso permite que o Console do Desenvolvedor saiba o que é necessário para obter um token de acesso em nome do usuário antes de fazer chamadas à API.

1. Navegue até instância de Gerenciamento de API e vá para **APIs**.

2. Selecione a API que você deseja proteger. Por exemplo, você `Echo API`pode usar o .

3. Vá para **Configurações**.

4. Em **Segurança**, escolha **OAuth 2.0** e selecione o servidor OAuth 2.0 configurado anteriormente. 

5. Selecione **Salvar**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Chamar com êxito a API do portal do desenvolvedor

> [!NOTE]
> Esta seção não se aplica para a camada **consumo**, que não oferece suporte para o portal do desenvolvedor.

Agora que a autorização do usuário OAuth 2.0 está ativada em sua API, o Console do Desenvolvedor obterá um token de acesso em nome do usuário, antes de chamar a API.

1. Navegue para qualquer operação a API no portal do desenvolvedor e **selecione Experimentá-lo**. Isso o levará ao Console do Desenvolvedor.

2. Observe um novo item na seção **Autorização,** correspondente ao servidor de autorização que você acabou de adicionar.

3. Selecione **Código de autorização** na lista suspensa de autorização. Quando você fizer isso, será solicitado o logon no locatário do Azure AD.  O logon pode não ser solicitado caso você já esteja conectado com a conta.

4. Após o logon com êxito, será adicionado um cabeçalho de `Authorization` à solicitação com um token de acesso do Azure AD. Veja abaixo um exemplo de token (com a codificação Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Clique em **Enviar**. Pronto! Agora você já pode chamar a API com êxito.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma política de validação de JWT para pré-autorizar solicitações

Neste ponto, quando um usuário tenta fazer uma chamada a partir do Console do Desenvolvedor, é solicitado o logon. O Console do Desenvolvedor obtém um token de acesso em nome do usuário, e inclui o token na solicitação feita à API.

No entanto, e se alguém ligar para sua API sem um token ou com um token inválido? Por exemplo, tente chamar a `Authorization` API sem o cabeçalho, a chamada ainda será chamada. Isso porque o Gerenciamento de API não valida o token de acesso nesse momento. Ele simplesmente passa o cabeçalho de `Authorization` para a API de back-end.

Você pode usar a política [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar solicitações no Gerenciamento de API, validando os tokens de acesso de cada solicitação de entrada. Se uma solicitação não tiver um token válido, o Gerenciamento de API a bloqueará. Por exemplo, adicione a `<inbound>` seguinte política `Echo API`à seção de política do . Ela verifica a declaração de audiência em um token de acesso e retorna uma mensagem de erro se o token não é válido. Para obter informações sobre como configurar as políticas, confira [Definir ou editar políticas](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```
> [!NOTE]
> Esta `openid-config` URL corresponde ao ponto final v1. Para o `openid-config`ponto final `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`v2, use .

## <a name="build-an-application-to-call-the-api"></a>Criar um aplicativo para chamar a API

Neste guia, você usou o Console do Desenvolvedor no Gerenciamento de API como o aplicativo cliente de exemplo para chamar a `Echo API` protegida pelo OAuth 2.0. Para saber mais sobre como criar um aplicativo e implementar o OAuth 2.0, confira [Exemplos de código do Microsoft Azure Active Directory](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Azure Active Directory e o OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).

* [Crie uma instância de serviço de gerenciamento de API](get-started-create-service-instance.md).

* [Gerencie sua primeira API](import-and-publish.md).
