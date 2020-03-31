---
title: Plataforma de identidade da Microsoft & fluxo de afirmação do portador saml | Azure
description: Aprenda a obter dados do Microsoft Graph sem solicitar ao usuário credenciais usando o fluxo de afirmação do portador do SAML.
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 16f30473ded5f1de5dc94c1cff9da96165b1a01c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700202"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Plataforma de identidade Microsoft e fluxo de afirmação do portador OAuth 2.0 SAML
O fluxo de afirmação do portador DoI Auth 2.0 Permite que você solicite um token de acesso OAuth usando uma afirmação SAML quando um cliente precisa usar uma relação de confiança existente. A assinatura aplicada à afirmação SAML fornece autenticação do aplicativo autorizado. Uma afirmação SAML é um token de segurança XML emitido por um provedor de identidade e consumido por um provedor de serviços. O provedor de serviços conta com seu conteúdo para identificar o assunto da afirmação para fins relacionados à segurança.

A afirmação SAML é postada no ponto final do token OAuth.  O ponto final processa a afirmação e emite um token de acesso com base na aprovação prévia do aplicativo. O cliente não é obrigado a ter ou armazenar um token de atualização, nem o segredo do cliente é necessário para ser passado para o ponto final do token.

O fluxo de afirmação do portador do SAML é útil ao buscar dados das APIs do Microsoft Graph (que só suportam permissões delegadas) sem solicitar credenciais ao usuário. Nesse cenário, a concessão de credenciais do cliente, que é preferida para processos de fundo, não funciona.

Para aplicativos que fazem login interativo baseado em navegador para obter uma afirmação SAML e, em seguida, querem adicionar acesso a uma API protegida oAuth (como o Microsoft Graph), você pode fazer uma solicitação OAuth para obter um token de acesso para a API. Quando o navegador é redirecionado para o Azure AD para autenticar o usuário, o navegador pegará a sessão a partir do login Do SAML e o usuário não precisa inserir suas credenciais.

O fluxo oAuth SAML Bearer Assertion também é suportado para usuários autenticando com provedores de identidade, como Active Directory Federation Services (ADFS) federados para o Azure Active Directory.  A afirmação SAML obtida do ADFS pode ser usada em um fluxo OAuth para autenticar o usuário.

![Fluxo OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Gráfico de chamada usando a afirmação do portador saml
Agora vamos entender como podemos realmente buscar a afirmação SAML programaticamente. Esta abordagem é testada com ADFS. No entanto, isso funciona com qualquer provedor de identidade que suporte o retorno da afirmação SAML de forma programática. O processo básico é: obter uma afirmação SAML, obter um token de acesso e acessar o Microsoft Graph.

### <a name="prerequisites"></a>Pré-requisitos

Estabeleça uma relação de confiança entre o servidor/ambiente de autorização (Microsoft 365) e o provedor de identidade, ou emissor da afirmação do portador SAML 2.0 (ADFS). Para configurar o ADFS para o único login e como provedor de identidade, você pode consultar [este artigo](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Registre a inscrição no [portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Faça login na [lâmina de registro do aplicativo do portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Por favor, note que estamos usando os pontos finais v2.0 para API gráfico e, portanto, precisamos registrar o aplicativo neste portal. Caso contrário, poderíamos ter usado os registros no diretório ativo do Azure). 
1. Selecione **Novo registro**.
1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo: 
    1. **Nome**: insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo.
    1. **Tipos de conta com suporte**: selecione as contas às quais você gostaria que seu aplicativo desse suporte.
    1. **Redirecione uri (opcional)** - Selecione o tipo de aplicativo que você está construindo, Web ou cliente público (celular & desktop) e, em seguida, digite o URI (ou URL de resposta) redirecionamento para o seu aplicativo.
    1. Ao terminar, selecione **Registrar**.
1. Anote o ID do aplicativo (cliente).
1. No painel esquerdo, selecione **Certificados & segredos**. Clique em **Novo cliente secreto** na seção segredos do **cliente.** Copie o novo cliente secreto, você não será capaz de recuperar quando deixar a lâmina.
1. No painel esquerdo, selecione **permissões de API** e **adicione uma permissão**. Selecione **o Microsoft Graph,** depois **delege as permissões**e selecione **Tasks.read,** pois pretendemos usar a API do Outlook Graph. 

Instale [o Carteiro](https://www.getpostman.com/), uma ferramenta necessária para testar as solicitações de amostra.  Mais tarde, você pode converter as solicitações em código.

### <a name="get-the-saml-assertion-from-adfs"></a>Obtenha a afirmação SAML da ADFS
Crie uma solicitação POST no ponto final do ADFS usando o envelope SOAP para buscar a afirmação do SAML:

![Obter afirmação SAML](./media/v2-saml-bearer-assertion/2.png)

Valores de cabeçalho:

![Valores de cabeçalho](./media/v2-saml-bearer-assertion/3.png)

Órgão de solicitação do ADFS:

![Órgão de solicitação do ADFS](./media/v2-saml-bearer-assertion/4.png)

Uma vez que essa solicitação seja postada com sucesso, você deve receber uma afirmação SAML da ADFS. Apenas os dados da tag **SAML:Assertion** são necessários, converta-os em codificação base64 para uso em outras solicitações.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Obtenha o token OAuth2 usando a afirmação SAML 
Nesta etapa, busque um token OAuth2 usando a resposta de afirmação do ADFS.

1. Crie uma solicitação POST conforme mostrado abaixo com os valores de cabeçalho:

    ![Solicitação do POST](./media/v2-saml-bearer-assertion/5.png)
1. No corpo do pedido, substitua **client_id,** **client_secret**e **afirmação** (a afirmação saml codificada base64 obteve a etapa anterior):

    ![Corpo da solicitação](./media/v2-saml-bearer-assertion/6.png)
1. Após solicitação bem-sucedida, você receberá um token de acesso do diretório ativo do Azure.

### <a name="get-the-data-with-the-oauth-token"></a>Obtenha os dados com o token Oauth

Depois de receber o token de acesso, chame as APIs do Gráfico (tarefas do Outlook neste exemplo). 

1. Crie uma solicitação GET com o token de acesso solicitado na etapa anterior:

    ![Solicitação GET](./media/v2-saml-bearer-assertion/7.png)

1. Após solicitação bem-sucedida, você receberá uma resposta JSON.

## <a name="next-steps"></a>Próximas etapas

Conheça os [diferentes fluxos de autenticação e cenários de aplicativos.](authentication-flows-app-scenarios.md)