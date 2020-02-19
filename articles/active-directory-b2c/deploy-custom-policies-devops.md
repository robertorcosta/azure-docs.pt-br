---
title: Implantar políticas personalizadas com o Azure Pipelines
titleSuffix: Azure AD B2C
description: Saiba como implantar Azure AD B2C políticas personalizadas em um pipeline de CI/CD usando Azure Pipelines no Azure DevOps Services.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 21fde69f404ee535bfe0019a91843297b1752a92
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463135"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Implantar políticas personalizadas com o Azure Pipelines

Usando um pipeline de CI/CD (integração e entrega contínuas) que você configura no [Azure pipelines][devops-pipelines], você pode incluir suas Azure ad B2C políticas personalizadas em sua distribuição de software e automação de controle de código. Conforme você implanta em diferentes ambientes de Azure AD B2C, por exemplo, desenvolvimento, teste e produção, recomendamos que você remova processos manuais e execute testes automatizados usando Azure Pipelines.

Há três etapas principais necessárias para habilitar Azure Pipelines gerenciar políticas personalizadas no Azure AD B2C:

1. Criar um registro de aplicativo Web em seu locatário Azure AD B2C
1. Configurar um repositório do Azure
1. Configurar um pipeline do Azure

> [!IMPORTANT]
> O gerenciamento de Azure AD B2C políticas personalizadas com um pipeline do Azure atualmente usa operações de **Visualização** disponíveis no ponto de extremidade `/beta` API de Microsoft Graph. Não há suporte para o uso dessas APIs em aplicativos de produção. Para obter mais informações, consulte a [referência de ponto de extremidade beta da API REST do Microsoft Graph](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Prerequisites

* [Azure ad B2C locatário](tutorial-create-tenant.md)e credenciais para um usuário no diretório com a função de [administrador da política IEF B2C](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Políticas personalizadas](custom-policy-get-started.md) carregadas para seu locatário
* [Pipeline do Azure](https://azure.microsoft.com/services/devops/pipelines/)e acesso a um [projeto Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Fluxo de concessão de credenciais de cliente

O cenário descrito aqui faz uso de chamadas de serviço a serviço entre Azure Pipelines e Azure AD B2C usando o fluxo de concessão de [credenciais de cliente](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)do OAuth 2,0. Esse fluxo de concessão permite que um serviço Web como Azure Pipelines (o cliente confidencial) Use suas próprias credenciais em vez de representar um usuário para autenticar ao chamar outro serviço Web (a API de Microsoft Graph, nesse caso). Azure Pipelines Obtém um token de forma não interativa e, em seguida, faz solicitações para a API de Microsoft Graph.

## <a name="register-an-application-for-management-tasks"></a>Registrar um aplicativo para tarefas de gerenciamento

Comece criando um registro de aplicativo que os scripts do PowerShell executados pelo Azure Pipelines usarão para se comunicar com Azure AD B2C. Se você já tiver um registro de aplicativo que você usa para tarefas de automação, poderá pular para a seção [conceder permissões](#grant-permissions) .

### <a name="register-application"></a>Registrar aplicativo

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-permissions"></a>Conceder permissões

Em seguida, conceda ao aplicativo a permissão para usar a API Microsoft Graph para ler e gravar políticas personalizadas em seu locatário Azure AD B2C.

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Na página Visão geral do **aplicativo registrado** , selecione **configurações**.
1. Em **acesso à API**, selecione **permissões necessárias**.
1. Selecione **Adicionar**e, em seguida, **Selecione uma API**.
1. Selecione **Microsoft Graph**e, em seguida, **selecione**.
1. Em **permissões do aplicativo**, selecione **ler e gravar as políticas de estrutura de confiança da sua organização**.
1. Selecione **selecionar**e, em seguida, **concluído**.
1. Selecione **Conceder permissões** e, em seguida, selecione **Sim**. Pode levar alguns minutos para que as permissões se propaguem totalmente.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione o aplicativo Web que deve ter acesso à API do Microsoft Graph. Por exemplo, *managementapp1*.
1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **APIs da Microsoft** e, em seguida, selecione **Microsoft Graph**.
1. Selecione **Permissões de aplicativo**.
1. Expanda **política** e selecione **Policy. ReadWrite. TrustFramework**.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)** .
1. Selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Aceitar**.
1. Selecione **Atualizar**e, em seguida, verifique se "concedido para..." aparece em **status**. Pode levar alguns minutos para que as permissões sejam propagadas.

* * *

### <a name="create-client-secret"></a>Criar segredo do cliente

Para autenticar com Azure AD B2C, o script do PowerShell precisa especificar um segredo do cliente que você cria para o aplicativo.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-an-azure-repo"></a>Configurar um repositório do Azure

Com um aplicativo de gerenciamento registrado, você está pronto para configurar um repositório para seus arquivos de política.

1. Entre em sua organização do Azure DevOps Services.
1. [Crie um novo projeto][devops-create-project] ou selecione um projeto existente.
1. Em seu projeto, navegue até **repositórios** e selecione a página **arquivos** . Selecione um repositório existente ou crie um para este exercício.
1. Crie uma pasta chamada *B2CAssets*. Nomeie o arquivo de espaço reservado necessário *README.MD* e **confirme** o arquivo. Você pode remover esse arquivo mais tarde, se desejar.
1. Adicione seus arquivos de política de Azure AD B2C à pasta *B2CAssets* Isso inclui o *TrustFrameworkBase. xml*, o *TrustFrameWorkExtensions. xml*, o *SignUpOrSignin. xml*, o *ProfileEdit. xml*, o *PasswordReset. xml*e todas as outras políticas que você criou. Registre o nome de arquivo de cada Azure AD B2C de política para uso em uma etapa posterior (eles são usados como argumentos de script do PowerShell).
1. Crie uma pasta chamada *scripts* no diretório raiz do repositório, nomeie o arquivo de espaço reservado *DeployToB2c. ps1*. Não confirme o arquivo neste ponto, você fará isso em uma etapa posterior.
1. Cole o script do PowerShell a seguir em *DeployToB2c. ps1*e, em seguida, **confirme** o arquivo. O script adquire um token do Azure AD e chama a API de Microsoft Graph para carregar as políticas na pasta *B2CAssets* para seu locatário Azure ad B2C.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Configurar seu pipeline do Azure

Com seu repositório inicializado e populado com seus arquivos de política personalizada, você está pronto para configurar o pipeline de liberação.

### <a name="create-pipeline"></a>Criar um pipeline

1. Entre em sua organização do Azure DevOps Services e navegue até o seu projeto.
1. Em seu projeto, selecione **pipelines** > **versões** > **novo pipeline**.
1. Em **selecionar um modelo**, selecione **trabalho vazio**.
1. Insira um **nome de estágio**, por exemplo *DeployCustomPolicies*, e feche o painel.
1. Selecione **Adicionar um artefato**e, em **tipo de origem**, selecione **repositório do Azure**.
    1. Escolha o repositório de origem que contém a pasta *scripts* que você preencheu com o script do PowerShell.
    1. Escolha uma **ramificação padrão**. Se você criou um novo repositório na seção anterior, o Branch padrão é *Master*.
    1. Deixe a configuração de **versão padrão** do *mais recente do Branch padrão*.
    1. Insira um **alias de origem** para o repositório. Por exemplo, *policyRepo*. Não inclua nenhum espaço no nome do alias.
1. Selecione **Adicionar**
1. Renomeie o pipeline para refletir sua intenção. Por exemplo, *implante pipeline de política personalizada*.
1. Selecione **salvar** para salvar a configuração de pipeline.

### <a name="configure-pipeline-variables"></a>Configurar variáveis de pipeline

1. Selecione a guia **variáveis** .
1. Adicione as seguintes variáveis em **variáveis de pipeline** e defina seus valores conforme especificado:

    | Nome | Valor |
    | ---- | ----- |
    | `clientId` | **ID do aplicativo (cliente)** do aplicativo que você registrou anteriormente. |
    | `clientSecret` | O valor do **segredo do cliente** que você criou anteriormente. <br /> Altere o tipo de variável para **segredo** (selecione o ícone de cadeado). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, em que *seu locatário-B2C* é o nome do seu locatário Azure ad B2C. |

1. Selecione **salvar** para salvar as variáveis.

### <a name="add-pipeline-tasks"></a>Adicionar tarefas de pipeline

Em seguida, adicione uma tarefa para implantar um arquivo de política.

1. Selecione a guia **tarefas** .
1. Selecione **trabalho do agente**e, em seguida, selecione o sinal de adição ( **+** ) para adicionar uma tarefa ao trabalho do agente.
1. Pesquise e selecione **PowerShell**. Não selecione "Azure PowerShell", "PowerShell em computadores de destino" ou outra entrada do PowerShell.
1. Selecione tarefa de **script do PowerShell** recém-adicionada.
1. Insira os seguintes valores para a tarefa Script do PowerShell:
    * **Versão da tarefa**: 2. *
    * **Nome de exibição**: o nome da política que esta tarefa deve carregar. Por exemplo, *B2C_1A_TrustFrameworkBase*.
    * **Tipo**: caminho do arquivo
    * **Caminho do script**: selecione as reticências (***...***), navegue até a pasta *scripts* e, em seguida, selecione o arquivo *DeployToB2C. ps1* .
    * **Argumentos**

        Insira os valores a seguir para **argumentos**. Substitua `{alias-name}` pelo alias especificado na seção anterior.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Por exemplo, se o alias especificado for *policyRepo*, a linha de argumento deverá ser:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/contosob2cpolicies/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selecione **salvar** para salvar o trabalho do agente.

A tarefa que você acabou de adicionar carrega *um* arquivo de política para Azure ad B2C. Antes de continuar, acione manualmente o trabalho (**criar versão**) para garantir que ele seja concluído com êxito antes de criar tarefas adicionais.

Se a tarefa for concluída com êxito, adicione tarefas de implantação executando as etapas anteriores para cada um dos arquivos de política personalizada. Modifique os valores de argumento `-PolicyId` e `-PathToFile` para cada política.

O `PolicyId` é um valor encontrado no início de um arquivo de política XML dentro do nó TrustFrameworkPolicy. Por exemplo, o `PolicyId` no seguinte XML de política é *B2C_1A_TrustFrameworkBase*:

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Ao executar os agentes e carregar os arquivos de política, verifique se eles foram carregados nesta ordem:

1. *TrustFrameworkBase. xml*
1. *TrustFrameworkExtensions. xml*
1. *SignUpOrSignin. xml*
1. *ProfileEdit. xml*
1. *PasswordReset. xml*

A estrutura de experiência de identidade impõe essa ordem, pois a estrutura do arquivo é criada em uma cadeia hierárquica.

## <a name="test-your-pipeline"></a>Testar o pipeline

Para testar o pipeline de lançamento:

1. Selecione **pipelines** e, em seguida, **versões**.
1. Selecione o pipeline que você criou anteriormente, por exemplo, *DeployCustomPolicies*.
1. Selecione **criar versão**e, em seguida, selecione **criar** para enfileirar a versão.

Você deverá ver uma faixa de notificação informando que uma liberação foi enfileirada. Para exibir seu status, selecione o link na faixa de notificação ou selecione-o na lista na guia **versões** .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

* [Chamadas de serviço a serviço usando credenciais do cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines