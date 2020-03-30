---
title: Registros de auditoria de acesso e revisão
titleSuffix: Azure AD B2C
description: Como acessar os registros de auditoria do Azure AD B2C de forma programática e no portal Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264213"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Acessando os logs de auditoria do Microsoft Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) emite registros de auditoria contendo informações de atividade sobre recursos B2C, tokens emitidos e acesso ao administrador. Este artigo fornece uma breve visão geral das informações disponíveis nos registros de auditoria e instruções sobre como acessar esses dados para o seu inquilino Azure AD B2C.

Os eventos de registro de auditoria são retidos apenas por **sete dias**. Planeje realizar o download e armazenar seus logs usando um dos métodos abaixo se precisar de um período de retenção mais longo.

> [!NOTE]
> Você não pode ver logins de usuário para aplicativos Azure AD B2C individuais a seção **Usuários** do Diretório Ativo do **Azure** ou páginas **Azure AD B2C** no portal Azure. Os eventos de login lá mostram a atividade do usuário, mas não podem ser correlacionados de volta ao aplicativo B2C ao que o usuário fez login. Você deve usar os registros de auditoria para isso, como explicado mais adiante neste artigo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Visão geral das atividades disponíveis na categoria B2C dos logs de auditoria

A categoria **B2C** nos logs de auditoria contém os seguintes tipos de atividades:

|Tipo de atividade |Descrição  |
|---------|---------|
|Autorização |Atividades relativas à autorização de um usuário para acessar recursos B2C (por exemplo, um administrador acessando uma lista de políticas B2C).         |
|Diretório |Atividades relacionadas a atributos de diretório recuperados quando um administrador faz sinal usando o portal Azure. |
|Aplicativo | Crie, leia, atualize e exclua (CRUD) operações em aplicativos B2C. |
|Chave |Operações CRUD em chaves armazenadas em um recipiente de chaves B2C. |
|Recurso |Operações CRUD em recursos B2C. Por exemplo, políticas e provedores de identidade.
|Autenticação |Validação de credenciais de usuário e emissão de tokens.|

Para atividades CRUD do objeto de usuário, consulte a categoria **Diretório Principal**.

## <a name="example-activity"></a>Atividade de exemplo

Esta imagem de exemplo do portal Azure mostra os dados capturados quando um usuário faz um sinal com um provedor de identidade externo, neste caso, o Facebook:

![Exemplo da página detalhes da atividade do registro de auditoria no portal Azure](./media/view-audit-logs/audit-logs-example.png)

O painel de detalhes da atividade contém as seguintes informações relevantes:

|Seção|Campo|Descrição|
|-------|-----|-----------|
| Atividade | Nome | Que atividade ocorreu. Por exemplo, *emita um id_token para o aplicativo,* que conclui o login real do usuário. |
| Iniciado por (ator) | ObjectId | O **ID** do objeto do aplicativo B2C no que o usuário está fazendo login. Este identificador não é visível no portal Azure, mas está acessível através da API do Microsoft Graph. |
| Iniciado por (ator) | Spn | O **ID** de aplicativo do aplicativo B2C no que o usuário está fazendo login. |
| Destino(s) | ObjectId | O **ID do objeto** do usuário que está fazendo login. |
| Detalhes adicionais | TenantId | O **ID** do inquilino Azure AD B2C. |
| Detalhes adicionais | PolicyId | O **ID** de diretiva do fluxo de usuário (política) que está sendo usado para fazer login do usuário. |
| Detalhes adicionais | ApplicationId | O **ID** de aplicativo do aplicativo B2C no que o usuário está fazendo login. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Veja registros de auditoria no portal Azure

O portal Azure fornece acesso aos eventos de registro de auditoria em seu inquilino Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com)
1. Mude para o diretório que contém o inquilino Azure AD B2C e, em seguida, navegue até **a Azure AD B2C**.
1. Em **Atividades** no menu esquerdo, selecione **Registros de auditoria**.

Uma lista de eventos de atividades registradas nos últimos sete dias é exibida.

![Filtro de exemplo com dois eventos de atividade no portal Azure](./media/view-audit-logs/audit-logs-example-filter.png)

Várias opções de filtragem estão disponíveis, incluindo:

* **Tipo de recurso de atividade** - Filtrar pelos tipos de atividade mostrados na tabela na visão geral das [atividades disponíveis.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Data** - Filtrar o intervalo de datas das atividades mostradas.

Se você selecionar uma linha na lista, os detalhes da atividade para o evento serão exibidos.

Para baixar a lista de eventos de atividade em um arquivo CSV (CSV) de valores separados por comuma, selecione **Download**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Obtenha registros de auditoria com a API de relatórios de relatórios do Azure

Os logs de auditoria são publicados para o mesmo pipeline como outras atividades do Azure Active Directory, para que possam ser acessados por meio da [API de relatórios do Microsoft Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Para obter mais informações, [consulte Inicie a API de relatórios do Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Habilitar o acesso à API de relatórios

Para permitir o acesso baseado em script ou aplicativo à API de emissão de relatórios Azure AD, você precisa de um aplicativo registrado no seu inquilino Azure AD B2C com as seguintes permissões de API. Você pode habilitar essas permissões em um registro de aplicativo existente dentro do seu inquilino B2C ou criar uma nova especificamente para uso com automação de log de auditoria.

* O Microsoft Graph > permissões de aplicativos > AuditLog > AuditLog.Read.All

Siga as etapas do artigo a seguir para registrar um aplicativo com as permissões necessárias:

[Gerenciar a azure AD B2C com gráfico microsoft](microsoft-graph-get-started.md)

Depois de registrar um aplicativo com as permissões apropriadas, consulte a seção de script powerShell mais tarde neste artigo para obter um exemplo de como você pode obter eventos de atividade com um script.

### <a name="access-the-api"></a>Acesse a API

Para baixar os eventos de log de auditoria Azure AD B2C através da API, filtre os logs na `B2C` categoria. Para filtrar por `filter` categoria, use o parâmetro de seqüência de consulta saquear o ponto final do relatório ADI do Azure.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Script do PowerShell

O script powershell a seguir mostra um exemplo de como consultar a API de relatórios azure AD. Depois de consultar a API, ele imprime os eventos registrados para a saída padrão e, em seguida, grava a saída JSON em um arquivo.

Você pode experimentar este script no [Azure Cloud Shell](overview.md). Certifique-se de atualizá-lo com seu ID de aplicativo, segredo do cliente e o nome do seu inquilino Azure AD B2C.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Aqui está a representação JSON do evento de atividade de exemplo mostrado anteriormente no artigo:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

Você pode automatizar outras tarefas de administração, por exemplo, [gerenciar contas de usuário Azure AD B2C com o Microsoft Graph](manage-user-accounts-graph-api.md).
