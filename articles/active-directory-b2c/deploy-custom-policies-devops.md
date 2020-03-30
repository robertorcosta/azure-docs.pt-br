---
title: Implantar políticas personalizadas com pipelines Azure
titleSuffix: Azure AD B2C
description: Saiba como implantar políticas personalizadas Azure AD B2C em um pipeline de CI/CD usando pipelines Azure nos Serviços Azure DevOps.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188742"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Implantar políticas personalizadas com pipelines Azure

Usando um pipeline de integração e entrega contínua (CI/CD) que você configura no [Azure Pipelines,][devops-pipelines]você pode incluir suas políticas personalizadas Azure AD B2C em sua entrega de software e automação de controle de código. À medida que você se desloca para diferentes ambientes Azure AD B2C, por exemplo, dev, teste e produção, recomendamos que você remova processos manuais e realize testes automatizados usando o Azure Pipelines.

Existem três etapas principais necessárias para permitir que o Azure Pipelines gerencie políticas personalizadas dentro do Azure AD B2C:

1. Crie um registro de aplicativo web no seu inquilino Azure AD B2C
1. Configure um Azure Repo
1. Configure um pipeline Azure

> [!IMPORTANT]
> O gerenciamento de políticas personalizadas azure AD B2C com um Azure `/beta` Pipeline atualmente usa operações **de visualização** disponíveis no ponto final da API do Microsoft Graph. Não há suporte para o uso dessas APIs em aplicativos de produção. Para obter mais informações, consulte a referência de ponto final beta da [API do Microsoft Graph REST](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Pré-requisitos

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)e credenciais para um usuário no diretório com a função [de administrador de políticas b2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Políticas personalizadas](custom-policy-get-started.md) enviadas ao seu inquilino
* [Aplicativo de gerenciamento](microsoft-graph-get-started.md) registrado em seu inquilino com a política de permissão da API do Microsoft *Graph.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)e acesso a [um projeto de Serviços Azure DevOps][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Fluxo de concessão de credenciais do cliente

O cenário descrito aqui faz uso de chamadas de serviço para serviço entre a Azure Pipelines e o Azure AD B2C usando o fluxo de [concessão de credenciais de cliente](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)oAuth 2.0 . Esse fluxo de concessão permite que um serviço web como o Azure Pipelines (o cliente confidencial) use suas próprias credenciais em vez de se passar por um usuário para autenticar ao chamar outro serviço web (a API do Microsoft Graph, neste caso). O Azure Pipelines obtém um token não interativo e, em seguida, faz solicitações à API do Microsoft Graph.

## <a name="register-an-application-for-management-tasks"></a>Registre um aplicativo para tarefas de gerenciamento

Como mencionado nos [pré-requisitos,](#prerequisites)você precisa de um registro de aplicativo que seus scripts PowerShell — executados pelo Azure Pipelines — possam usar para acessar os recursos em seu inquilino.

Se você já tiver um registro de aplicativo que você usa para tarefas de automação, certifique-se de que foi concedida a política de > **política** > do **Microsoft****GraphPolicy.ReadWrite.TrustFramework** permissão dentro das permissões de **API** do registro do aplicativo.

Para obter instruções sobre como registrar um aplicativo de gerenciamento, consulte [Gerenciar o Azure AD B2C com o Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Configure um Azure Repo

Com um aplicativo de gerenciamento registrado, você está pronto para configurar um repositório para seus arquivos de diretiva.

1. Faça login na organização Azure DevOps Services.
1. [Crie um novo projeto][devops-create-project] ou selecione um projeto existente.
1. Em seu projeto, navegue até **Repos** e selecione a página **Arquivos.** Selecione um repositório existente ou crie um para este exercício.
1. Crie uma pasta chamada *B2CAssets*. Nomeie o arquivo de espaço reservado necessário *README.md* e **Comprometa** o arquivo. Você pode remover este arquivo mais tarde, se quiser.
1. Adicione seus arquivos de diretiva Azure AD B2C à pasta *B2CAssets.* Isso inclui o *TrustFrameworkBase.xml,* *TrustFrameWorkExtensions.xml,* *SignUpOrSignin.xml,* *ProfileEdit.xml,* *PasswordReset.xml*e quaisquer outras políticas criadas. Grave o nome de arquivo de cada arquivo de diretiva AD B2C do Azure para uso em uma etapa posterior (eles são usados como argumentos de script PowerShell).
1. Crie uma pasta chamada *Scripts* no diretório raiz do repositório, nomeie o arquivo de espaço reservado *DeployToB2c.ps1*. Não comprometa o arquivo neste momento, você vai fazê-lo em uma etapa posterior.
1. Cole o seguinte script PowerShell em *DeployToB2c.ps1*e, em seguida, **comprometa** o arquivo. O script adquire um token do Azure AD e chama a API do Microsoft Graph para carregar as políticas dentro da pasta *B2CAssets* para o seu inquilino Azure AD B2C.

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

## <a name="configure-your-azure-pipeline"></a>Configure seu pipeline Azure

Com seu repositório inicializado e preenchido com seus arquivos de diretiva personalizados, você está pronto para configurar o pipeline de liberação.

### <a name="create-pipeline"></a>Criar um pipeline

1. Faça login na organização Azure DevOps Services e navegue até o seu projeto.
1. Em seu projeto, selecione **Pipelines** > **Releases** > **New pipeline**.
1. Em **Selecionar um modelo,** **selecione Esvaziar o trabalho**.
1. Digite um **nome de palco,** por *exemplo, DeployCustomPolicies*, e feche o painel.
1. Selecione **Adicionar um artefato**e, em **tipo de origem,** selecione **O Repositório Azure**.
    1. Escolha o repositório de origem que contém a pasta *Scripts* que você preencheu com o script PowerShell.
    1. Escolha um **ramo Padrão**. Se você criou um novo repositório na seção anterior, o ramo padrão será *master*.
    1. Deixe a **configuração de versão Padrão** do *Mais Recente no ramo padrão*.
    1. Digite um **alias de Origem** para o repositório. Por exemplo, *policyRepo*. Não inclua nenhum espaço no nome do pseudônimo.
1. Selecione **Adicionar**
1. Renomeie o oleoduto para refletir sua intenção. Por exemplo, *implantar pipeline de políticas personalizadas*.
1. Selecione **Salvar** para salvar a configuração do pipeline.

### <a name="configure-pipeline-variables"></a>Configurar variáveis de pipeline

1. Selecione a guia **Variáveis.**
1. Adicione as seguintes variáveis em **variáveis pipeline** e defina seus valores conforme especificado:

    | Nome | Valor |
    | ---- | ----- |
    | `clientId` | ID de aplicação **(cliente)** do aplicativo que você registrou anteriormente. |
    | `clientSecret` | O valor do segredo do **cliente** que você criou anteriormente. <br /> Alterar o tipo de variável para **secreto** (selecione o ícone de bloqueio). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, onde *seu inquilino b2c* é o nome do seu inquilino Azure AD B2C. |

1. Selecione **Salvar** para salvar as variáveis.

### <a name="add-pipeline-tasks"></a>Adicionar tarefas de pipeline

Em seguida, adicione uma tarefa para implantar um arquivo de diretiva.

1. Selecione a guia **Tarefas.**
1. Selecione **o trabalho de Agente****+** e selecione o sinal de mais () para adicionar uma tarefa ao trabalho de Agente.
1. Procure e selecione **PowerShell**. Não selecione "Azure PowerShell", "PowerShell em máquinas de destino" ou outra entrada PowerShell.
1. Selecione a tarefa **PowerShell Script** recém-adicionada.
1. Insira os seguintes valores para a tarefa PowerShell Script:
    * **Versão da tarefa:** 2.*
    * **Nome de exibição**: O nome da política que esta tarefa deve carregar. Por exemplo, *B2C_1A_TrustFrameworkBase.*
    * **Tipo:** Caminho do arquivo
    * **Caminho de script**: Selecione a elipse ***( ...***), navegue até a pasta *Scripts* e selecione o arquivo *DeployToB2C.ps1.*
    * **Argumentos:**

        Digite os **seguintes**valores para Argumentos . Substitua-o `{alias-name}` pelo alias especificado na seção anterior.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Por exemplo, se o alias especificado for *policyRepo,* a linha de argumento deve ser:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selecione **Salvar** para salvar o trabalho do Agente.

A tarefa que você acabou de adicionar carrega *um* arquivo de diretiva para o Azure AD B2C. Antes de prosseguir, acione manualmente o trabalho **(Criar versão)** para garantir que ele seja concluído com sucesso antes de criar tarefas adicionais.

Se a tarefa for concluída com sucesso, adicione tarefas de implantação executando as etapas anteriores para cada um dos arquivos de diretiva personalizados. Modificar `-PolicyId` os `-PathToFile` valores e argumentos para cada política.

O `PolicyId` valor encontrado no início de um arquivo de diretiva XML dentro do nó TrustFrameworkPolicy. Por exemplo, `PolicyId` a seguinte política XML é *B2C_1A_TrustFrameworkBase*:

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

Ao executar os agentes e carregar os arquivos de diretiva, certifique-se de que eles são carregados nesta ordem:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *Inscreva-seOuOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

O Identity Experience Framework impõe essa ordem à medida que a estrutura do arquivo é construída em uma cadeia hierárquica.

## <a name="test-your-pipeline"></a>Testar o pipeline

Para testar seu pipeline de liberação:

1. Selecione **Pipelines** e, em seguida, **Lançamentos**.
1. Selecione o pipeline que você criou anteriormente, por *exemplo, DeployCustomPolicies*.
1. Selecione **Criar versão**e selecione **Criar** para enfileirar a versão.

Você deve ver um banner de notificação que diz que uma versão foi enfileirada. Para exibir seu status, selecione o link no banner de notificação ou selecione-o na lista na guia **Lançamentos.**

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

* [Chamadas de serviço para serviço usando credenciais do cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
