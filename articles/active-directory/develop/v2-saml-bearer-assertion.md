---
title: Plataforma de identidade da Microsoft e fluxo de declaração de portador SAML | Azure
description: Saiba como efetuar fetch de dados do Microsoft Graph sem solicitar credenciais ao usuário usando o fluxo de declaração de portador SAML.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 46f3ef775f3b17e0ebc93fc4145a5b8037b901e5
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949347"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Plataforma de identidade da Microsoft e fluxo de declaração de portador SAML do OAuth 2.0
O fluxo de declaração do portador SAML do OAuth 2.0 permite solicitar um token de acesso OAuth usando uma declaração SAML quando um cliente precisa usar uma relação de confiança existente. A assinatura aplicada à declaração SAML fornece autenticação do aplicativo autorizado. Uma declaração SAML é um token de segurança XML emitido por um provedor de identidade e consumido por um provedor de serviços. O provedor de serviços depende do conteúdo para identificar a entidade da declaração para fins relacionados à segurança.

A declaração SAML é postada no ponto de extremidade do token OAuth.  O ponto de extremidade processa a declaração e emite um token de acesso com base na aprovação anterior do aplicativo. O cliente não precisa ter nem armazenar um token de atualização, tampouco o segredo do cliente deve ser passado para o ponto de extremidade do token.

O fluxo de declaração de portador SAML é útil ao efetuar fetch de dados das APIs do Microsoft Graph (que só dão suporte a permissões delegadas) sem solicitar credenciais ao usuário. Nesse cenário, a concessão de credenciais de cliente, que é preferencial para processos em segundo plano, não funciona.

Para aplicativos que fazem logon interativo baseado em navegador para obter uma declaração SAML e que desejam adicionar o acesso a uma API protegida por OAuth (como o Microsoft Graph), faça uma solicitação OAuth para obter um token de acesso para a API. Quando o navegador é redirecionado para o Azure AD a fim de autenticar o usuário, o navegador detectará a sessão com base na entrada SAML e o usuário não precisará inserir as credenciais.

Também há suporte ao fluxo de declaração de portador SAML do OAuth para os usuários que se autenticam com provedores de identidade, como os ADFS (Serviços de Federação do Active Directory) federados para o Azure Active Directory.  A declaração SAML obtida do ADFS pode ser usada em um fluxo OAuth para autenticar o usuário.

![Fluxo do OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Grafo de chamadas usando a declaração de portador SAML
Agora vamos entender como podemos realmente efetuar fetch da declaração SAML de maneira programática. Essa abordagem é testada com o ADFS. No entanto, isso funciona com qualquer provedor de identidade que dê suporte ao retorno da declaração SAML de maneira programática. O processo básico é: obter uma declaração SAML, obter um token de acesso e acessar o Microsoft Graph.

### <a name="prerequisites"></a>Pré-requisitos

Estabelecer uma relação de confiança entre o servidor/o ambiente de autorização (Microsoft 365) e o provedor de identidade ou o emissor da declaração de portador SAML 2.0 (ADFS). Para configurar o ADFS para logon único e como um provedor de identidade, veja [este artigo](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365).

Registrar o aplicativo no [portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Entre na [folha de registro de aplicativo do portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (observe que estamos usando os pontos de extremidade v2.0 da API do Graph e, portanto, precisamos registrar o aplicativo nesse portal. Caso contrário, poderíamos ter usado os registros no Azure Active Directory). 
1. Selecione **Novo registro**.
1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo: 
    1. **Nome**: insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo.
    1. **Tipos de conta com suporte**: selecione as contas às quais você gostaria que seu aplicativo desse suporte.
    1. **URI de redirecionamento (opcional)** : selecione o tipo de aplicativo que você está compilando, Web ou Cliente público (dispositivos móvel e desktop) e insira o URI de redirecionamento (ou a URL de resposta) do aplicativo.
    1. Ao terminar, selecione **Registrar**.
1. Anote a ID do aplicativo (cliente).
1. No painel esquerdo, selecione **Certificados e segredos**. Clique em **Novo segredo do cliente** na seção **Segredos do cliente**. Copie o novo segredo do cliente, pois você não poderá recuperá-lo quando sair da folha.
1. No painel esquerdo, selecione **Permissões de API** e **Adicionar uma permissão**. Selecione **Microsoft Graph**, **Permissões delegadas** e **Tasks.read**, pois pretendemos usar a API do Graph do Outlook. 

Instale o [Postman](https://www.getpostman.com/), uma ferramenta necessária para testar as solicitações de exemplo.  Posteriormente, você poderá converter as solicitações em código.

### <a name="get-the-saml-assertion-from-adfs"></a>Obter a declaração SAML do ADFS
Crie uma solicitação POST ao ponto de extremidade do ADFS usando o envelope SOAP para efetuar fetch da declaração SAML:

![Obter a declaração SAML](./media/v2-saml-bearer-assertion/2.png)

Valores do cabeçalho:

![Valores do cabeçalho](./media/v2-saml-bearer-assertion/3.png)

Corpo da solicitação do ADFS:

![Corpo da solicitação do ADFS](./media/v2-saml-bearer-assertion/4.png)

Depois que essa solicitação for postada com êxito, você deverá receber uma declaração SAML do ADFS. Somente os dados da marca **SAML:Assertion** são necessários; converta-os na codificação Base64 para usá-los em solicitações posteriores.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Obter o token OAuth2 usando a declaração SAML 
Nesta etapa, efetue fetch de um token OAuth2 usando a resposta de declaração do ADFS.

1. Crie uma solicitação POST, conforme mostrado abaixo, com os valores de cabeçalho:

    ![Solicitação POST](./media/v2-saml-bearer-assertion/5.png)
1. No corpo da solicitação, substitua **id_do_cliente**, **segredo_do_cliente** e **declaração** (a declaração SAML codificada em Base64 obtida na etapa anterior):

    ![Corpo da solicitação](./media/v2-saml-bearer-assertion/6.png)
1. Após a solicitação bem-sucedida, você receberá um token de acesso do Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Obter os dados com o token OAuth

Depois de receber o token de acesso, chame as APIs do Graph (tarefas do Outlook, neste exemplo). 

1. Crie uma solicitação GET com o token de acesso buscado na etapa anterior:

    ![Solicitação GET](./media/v2-saml-bearer-assertion/7.png)

1. Após a solicitação bem-sucedida, você receberá uma resposta JSON.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os diferentes [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md).
