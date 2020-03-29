---
title: API do Gráfico microsoft para proteção de identidade do diretório ativo do Azure
description: Saiba como consultar detecções de risco do Microsoft Graph e informações associadas do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671614"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph

O Microsoft Graph é o ponto de extremidade de API unificado da Microsoft e a página inicial das APIs do [Azure Active Directory Identity Protection](../active-directory-identityprotection.md). Existem quatro APIs que expõem informações sobre usuários de risco e logins. A primeira API, **riskDetection,** permite consultar o Microsoft Graph para obter uma lista de detecções de risco vinculadas ao usuário e login e informações associadas sobre a detecção. A segunda API, **riskyUsers**, permite que você consulte o Microsoft Graph para obter informações sobre a proteção do Identity Protection detectada como risco. A terceira API, **signIn**, permite que você consulte o Microsoft Graph para obter informações sobre entradas do Azure AD com propriedades específicas relacionadas ao estado, detalhe e nível de risco. A quarta API, **identityRiskEvents,** permite consultar o Microsoft Graph para obter uma lista de detecções de risco e informações [associadas.](../reports-monitoring/concept-risk-events.md) A API identityRiskEvents será preterida em 10 de janeiro de 2020; sugerimos que você use a API **RiskDetections** em vez disso. Este artigo é uma introdução para conectar o Microsoft Graph e consultar essas APIs. Para obter uma introdução detalhada, documentação completa e acesso ao Graph Explorer, consulte o [site do Microsoft Graph](https://graph.microsoft.io/) ou a documentação de referência específica para essas APIs:

* [API de detecção de risco](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [API signIn](/graph/api/resources/signin?view=graph-rest-beta)
* [imbecisA API de Eventos de](/graph/api/resources/identityriskevent?view=graph-rest-beta) *Risco será preterida em 10 de janeiro de 2020*

## <a name="connect-to-microsoft-graph"></a>Conectar o Microsoft Graph

Há quatro etapas para acessar dados de Proteção de Identidade por meio do Microsoft Graph:

1. Recupere seu nome de domínio.
2. Crie um novo registro de aplicativo. 
3. Use esse segredo e algumas outras informações para se autenticar no Microsoft Graph, onde você recebe um token de autenticação. 
4. Use esse token para fazer solicitações para o ponto de extremidade de API e para obter dados do Identity Protection.

Antes de começar, será necessário:

* Privilégios de administrador para criar o aplicativo no Azure AD
* O nome do domínio do seu locatário (por exemplo, contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Recuperar seu nome de domínio 

1. [Entre](https://portal.azure.com) no Portal do Azure como administrador. 
1. No painel de navegação à esquerda, clique em **Active Directory**. 

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/41.png)

1. Na seção **Gerenciar**, clique em **Propriedades**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/42.png)

1. Copie seu nome de domínio.

## <a name="create-a-new-app-registration"></a>Criar um novo registro de aplicativo

1. Na página **Active Directory**, na seção **Gerenciar**, clique em **Registros de aplicativo**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/42.png)

1. No menu na parte superior, clique em **Novo registro do aplicativo**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/43.png)

1. Na página **Criar**, realize as seguintes etapas:

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/44.png)

   1. Na caixa de texto **Nome,** digite um nome para o seu aplicativo (por exemplo: Azure AD Risk Detection API Application).

   1. Como **Tipo**, selecione **Aplicativo Web E/Ou API Web**.

   1. Na caixa de texto url `http://localhost`de **login,** digite .

   1. Clique em **Criar**.
1. Para abrir a página **Configurações**, na lista de aplicativos, clique no registro de aplicativo recém-criado. 
1. Copie o **ID do aplicativo**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Conceda permissão ao aplicativo para usar a API

1. Na página **Configurações**, clique em **Permissões necessárias**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/15.png)

1. Na página **Permissões necessárias**, na barra de ferramentas na parte superior, clique em **Adicionar**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/16.png)

1. Na página **Adicionar acesso à API**, clique em **Selecionar uma API**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/17.png)

1. Na página **Selecionar uma API**, selecione **Microsoft Graph** e clique em **Selecionar**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/18.png)

1. Na página **Adicionar acesso à API**, clique em **Selecionar permissões**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/19.png)

1. Na página **Habilitar acesso**, clique em **Ler todas as informações de risco de identidade** e clique em **Selecionar**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/20.png)

1. Na página **Adicionar acesso à API**, clique em **Concluído**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/21.png)

1. Na página **Permissões necessárias**, clique em **Conceder Permissões** e em **Sim**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Obter uma chave de acesso

1. Na página **Configurações**, clique em **Chaves**.

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/23.png)

1. Na página **Chaves**, execute as seguintes etapas:

   ![Criação de um aplicativo](./media/howto-identity-protection-graph-api/24.png)

   1. Na caixa de texto **de descrição da chave,** digite uma descrição (por exemplo, *Detecção de Risco AD do Azure).*
   1. Como **Duração**, selecione **Em um ano**.
   1. Clique em **Salvar**.
   1. Copie o valor da chave e cole em um local seguro.   
   
   > [!NOTE]
   > Se você perder esta chave, precisará retornar a esta seção e criar uma nova chave. Mantenha essa chave em segredo: qualquer pessoa que a tenha poderá acessar seus dados.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autenticar o Microsoft Graph e consultar a API de Detecções de Risco de Identidade

Neste ponto, você deve ter:

- O nome do domínio do locatário
- A ID do cliente 
- A chave 

Para autenticar, envie uma solicitação post para `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: "**client_credentials**"
- resource: `https://graph.microsoft.com`
- client_id: \<a ID do cliente\>
- client_secret: \<a chave\>

Se for bem-sucedido, será retornado um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

```
`Authorization`="<token_type> <access_token>"
```

Durante a autenticação, você poderá encontrar o tipo de token e o token de acesso no token retornado.

Envie este cabeçalho como uma solicitação para a seguinte URL de API: `https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se bem sucedida, é uma coleção de detecções de risco de identidade e dados associados no formato OData JSON, que podem ser analisados e tratados como você achar melhor.

Veja um código de exemplo para autenticação e chamada da API usando o PowerShell.  
Basta adicionar sua ID do cliente, a chave secreta e o domínio do locatário.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Consultar as APIs

Essas três APIs fornecem uma infinidade de oportunidades para recuperar informações sobre entradas e usuários arriscados na organização. A seguir, estão alguns casos de uso comuns para essas APIs e as solicitações de exemplos associados. É possível executar essas consultas, usando o código de exemplo acima ou usando o [Explorador do Graph](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Obtenha todas as detecções de risco offline (RiskDetection API)

Com as políticas de risco de login de proteção de identidade, você pode aplicar condições quando o risco é detectado em tempo real. Mas e as detecções que são descobertas offline? Para entender quais detecções ocorreram offline e, portanto, não teria acionado a política de risco de login, você pode consultar a API de detecção de risco.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Obter todos os usuários que concluíram com êxito um desafio MFA disparado por uma política de entradas arriscadas (API riskyUsers)

Para entender o impacto que as políticas baseadas em risco de Proteção de Identidade têm na sua organização, você pode consultar todos os usuários que passaram com sucesso em um desafio de MFA desencadeado por uma política de logins de risco. Essas informações podem ajudá-lo a reconhecer quais usuários o Identity Protection pode ter falsamente detectado como arriscados e quais usuários legítimos podem estar realizando ações que a IA considera arriscadas.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Obter todas as entradas arriscadas para um usuário específico (API signIn)

Quando você acredita que um usuário pode ter sido comprometido, é possível reconhecer melhor o estado do risco recuperando todas as entradas arriscadas. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Próximas etapas

Parabéns, você acabou de criar sua primeira chamada para o Microsoft Graph!  
Agora você pode consultar detecções de risco de identidade e usar os dados como achar melhor.

Para saber mais sobre o Microsoft Graph e como criar aplicativos usando a API do Graph, confira a [documentação](/graph/overview) e muito mais no [site Microsoft Graph](https://developer.microsoft.com/graph). 

Para obter informações relacionadas. consulte:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Tipos de detecções de risco detectadas pelo Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Visão geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Raiz do Serviço Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
