---
title: API de Microsoft Graph para Azure Active Directory Identity Protection
description: Saiba como consultar Microsoft Graph as detecções de risco e as informações associadas de Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81c3715995d0d53cd72acf7277f46a279fa4cfb2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019651"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph

O Microsoft Graph é o ponto de extremidade de API unificado da Microsoft e a página inicial das APIs do [Azure Active Directory Identity Protection](../active-directory-identityprotection.md). Há três APIs que expõem informações sobre usuários arriscados e entradas. A primeira API, **riskDetection**, permite que você consulte Microsoft Graph para obter uma lista de detecções de risco vinculadas de usuário e de entrada e informações associadas sobre a detecção. A segunda API, **riskyUsers**, permite que você consulte o Microsoft Graph para obter informações sobre a proteção do Identity Protection detectada como risco. A terceira API, **signIn**, permite que você consulte o Microsoft Graph para obter informações sobre entradas do Azure AD com propriedades específicas relacionadas ao estado, detalhe e nível de risco. 

Este artigo é uma introdução para conectar o Microsoft Graph e consultar essas APIs. Para obter uma introdução detalhada, documentação completa e acesso ao Graph Explorer, consulte o [site do Microsoft Graph](https://graph.microsoft.io/) ou a documentação de referência específica para essas APIs:

* [API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [API signIn](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Conectar o Microsoft Graph

Há quatro etapas para acessar dados de Proteção de Identidade por meio do Microsoft Graph:

- [Recuperar seu nome de domínio](#retrieve-your-domain-name)
- [Criar um novo registro de aplicativo](#create-a-new-app-registration)
- [Configurar permissões de API](#configure-api-permissions)
- [Configurar uma credencial válida](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Recuperar seu nome de domínio 

1. Entre no [portal do Azure](https://portal.azure.com).  
1. Navegue até **Azure Active Directory**  >  **nomes de domínio personalizados**. 
1. Anote o `.onmicrosoft.com` domínio, você precisará dessas informações em uma etapa posterior.

### <a name="create-a-new-app-registration"></a>Criar um novo registro de aplicativo

1. Na portal do Azure, navegue até **Azure Active Directory**  >  **registros de aplicativo**.
1. Selecione **Novo registro**.
1. Na página **criar** , execute as seguintes etapas:
   1. Na caixa de texto **nome** , digite um nome para seu aplicativo (por exemplo: API de detecção de riscos do Azure AD).
   1. Em **tipos de conta com suporte**, selecione o tipo de contas que usarão as APIs.
   1. Selecione **Registrar**.
1. Copie a **ID do aplicativo**.

### <a name="configure-api-permissions"></a>Configurar permissões de API

1. No **aplicativo** que você criou, selecione **permissões de API**.
1. Na página **permissões configuradas** , na barra de ferramentas na parte superior, clique em **Adicionar uma permissão**.
1. Na página **Adicionar acesso à API**, clique em **Selecionar uma API**.
1. Na página **Selecionar uma API**, selecione **Microsoft Graph** e clique em **Selecionar**.
1. Na página **solicitar permissões de API** : 
   1. Selecione **Permissões de aplicativo**.
   1. Marque as caixas de seleção ao lado de `IdentityRiskEvent.Read.All` e `IdentityRiskyUser.Read.All` .
   1. Escolha **Adicionar permissões**.
1. Selecione **conceder consentimento de administrador para o domínio** 

### <a name="configure-a-valid-credential"></a>Configurar uma credencial válida

1. No **aplicativo** que você criou, selecione **certificados & segredos**.
1. Em **segredos do cliente**, selecione **novo segredo do cliente**.
   1. Dê uma **Descrição** ao segredo do cliente e defina o período de tempo de expiração de acordo com as políticas organizacionais.
   1. Selecione **Adicionar**.

   > [!NOTE]
   > Se você perder esta chave, precisará retornar a esta seção e criar uma nova chave. Mantenha essa chave em segredo: qualquer pessoa que a tenha poderá acessar seus dados.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autenticar para Microsoft Graph e consultar a API de detecções de risco de identidade

Neste ponto, você deve ter:

- O nome do domínio do locatário
- A ID do aplicativo (cliente) 
- O segredo ou certificado do cliente 

Para autenticar, envie uma solicitação post para `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: "**client_credentials**"
- resource: `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Se for bem-sucedida, essa solicitação retornará um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

```
`Authorization`="<token_type> <access_token>"
```

Durante a autenticação, você poderá encontrar o tipo de token e o token de acesso no token retornado.

Envie este cabeçalho como uma solicitação para a seguinte URL de API: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

A resposta, se for bem-sucedida, é uma coleção de detecções de risco de identidade e dados associados no formato OData JSON, que pode ser analisado e manipulado da maneira que você vê adequado.

### <a name="sample"></a>Amostra

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

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
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

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Obter todas as detecções de riscos offline (API riskDetection)

Com as políticas de risco de entrada da proteção de identidade, você pode aplicar condições quando o risco for detectado em tempo real. Mas e quanto às detecções descobertas offline? Para entender quais detecções ocorreram offline e, portanto, não dispararia a política de risco de entrada, você pode consultar a API riskDetection.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Obter todos os usuários que concluíram com êxito um desafio MFA disparado por uma política de entradas arriscadas (API riskyUsers)

Para entender o impacto sobre as políticas baseadas em risco da proteção de identidade em sua organização, você pode consultar todos os usuários que passaram com êxito um desafio MFA disparado por uma política de entradas arriscadas. Essas informações podem ajudá-lo a reconhecer quais usuários o Identity Protection pode ter falsamente detectado como arriscados e quais usuários legítimos podem estar realizando ações que a IA considera arriscadas.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Próximas etapas

Parabéns, você acabou de criar sua primeira chamada para o Microsoft Graph!  
Agora você pode consultar as detecções de risco de identidade e usar os dados, no entanto, se desejar.

Para saber mais sobre o Microsoft Graph e como criar aplicativos usando a API do Graph, confira a [documentação](/graph/overview) e muito mais no [site Microsoft Graph](https://developer.microsoft.com/graph). 

Para obter informações relacionadas. consulte:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Tipos de detecções de risco detectadas pelo Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Visão geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Raiz do Serviço Azure AD Identity Protection](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)
