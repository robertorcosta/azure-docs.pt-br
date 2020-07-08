---
title: Configurar como os usuários finais consentem nos aplicativos que usam o Azure AD
description: Saiba como gerenciar como e quando os usuários podem consentir nos aplicativos que terão acesso aos dados da sua organização.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763458"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurar como os usuários finais consentem nos aplicativos

Você pode integrar seus aplicativos à plataforma de identidade da Microsoft para permitir que os usuários entrem com sua conta corporativa ou de estudante e acessem os dados da sua organização a fim de fornecer experiências avançadas controladas por dados.

Antes que um aplicativo possa acessar os dados da sua organização, um usuário precisa conceder permissões ao aplicativo para isso. Permissões diferentes permitem diferentes níveis de acesso. Por padrão, todos os usuários têm permissão para consentir nos aplicativos em relação a permissões que não exijam o consentimento do administrador. Por exemplo, por padrão, um usuário pode permitir que um aplicativo acesse sua caixa de correio, mas não pode permitir que um aplicativo tenha acesso irrestrito para ler e gravar em todos os arquivos em sua organização.

Ao permitir que os usuários concedam aos aplicativos acesso aos dados, os usuários poderão facilmente adquirir aplicativos úteis e ser produtivos. No entanto, em algumas situações, essa configuração pode representar um risco se não for monitorada e controlada com cuidado.

## <a name="user-consent-settings"></a>Configurações de consentimento do usuário

Para controlar em quais casos os usuários podem consentir nos aplicativos, escolha a política de consentimento que será aplicada a todos os usuários. Aqui estão as três opções de política de consentimento:

* **Desabilitar o consentimento do usuário**: os usuários não podem conceder permissões a aplicativos. Os usuários podem continuar a entrar em aplicativos que tinham consentido anteriormente ou cujo consentimento tenha sido feito por administradores em seu nome, mas eles não terão permissão para dar consentimento a novas permissões ou a novos aplicativos por conta própria. Somente os usuários que receberam uma função de diretório que inclua a permissão para conceder consentimento poderão consentir novas permissões ou novos aplicativos.

* **Os usuários podem consentir em aplicativos de fornecedores verificados, mas somente em permissões que você selecionar (versão prévia)** : todos os usuários só podem dar consentimento a aplicativos que forem publicados por um [fornecedor verificado](../develop/publisher-verification-overview.md) e aplicativos que estejam registrados em seu locatário. Os usuários só podem consentir nas permissões que você classificou como de "Pouco impacto".

  [Classifique as permissões](#configure-permission-classifications-preview) para selecionar quais permissões os usuários têm permissão para consentir.

* **Os usuários podem consentir em todos os aplicativos**: é uma opção que permite que todos os usuários consintam qualquer permissão, que não requer consentimento de administrador, para qualquer aplicativo. 

   Para reduzir o risco de aplicativos mal-intencionados tentarem induzir os usuários a conceder acesso aos dados da sua organização, recomendamos que você permita o consentimento do usuário apenas em aplicativos que foram publicados por um [fornecedor verificado](../develop/publisher-verification-overview.md).

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Definir as configurações de consentimento do usuário no portal do Azure

Para definir as configurações de consentimento do usuário por meio do portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) como um [Administrador Global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Consentimento e permissões** > **Configurações de consentimento do usuário**.
1. Em **Consentimento do usuário para aplicativos**, selecione a configuração de consentimento que você deseja configurar para todos os usuários.
1. Selecione **salvar** para salvar suas configurações.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Configurações de consentimento do usuário":::

> [!TIP]
> Considere [habilitar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md) para permitir que os usuários solicitem o exame e a aprovação do administrador de um aplicativo no qual o usuário não tenha permissão para consentir, por exemplo, quando o consentimento do usuário foi desabilitado ou quando um aplicativo está solicitando permissões que o usuário não tem permissão para conceder.

### <a name="configure-user-consent-settings-using-powershell"></a>Definir configurações de consentimento do usuário usando o PowerShell

Você pode usar o módulo de versão prévia mais recente do PowerShell do Azure AD, [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), para escolher qual política de consentimento regerá o consentimento do usuário nos aplicativos.

* **Desabilitar consentimento do usuário**: para desabilitar o consentimento do usuário, defina as políticas de consentimento que regem o consentimento do usuário como vazio:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Permitir consentimento do usuário para permissões selecionadas em aplicativos de fornecedores verificados (versão prévia)** : para permitir o consentimento do usuário limitado somente a aplicativos de fornecedores e aplicativos verificados registrados em seu locatário, e somente para permissões que você classificar como "Pouco impacto", configure a política interna de autorização chamada `microsoft-user-default-low`:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Não se esqueça de [classificar as permissões](#configure-permission-classifications-preview) para selecionar quais permissões os usuários têm permissão para consentir.

* **Permitir consentimento do usuário em todos os aplicativos**: para permitir o consentimento do usuário em todos os aplicativos:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Essa opção permite que todos os usuários consintam qualquer permissão que não exija o consentimento do administrador, para qualquer aplicativo. Recomendamos que você permita o consentimento do usuário somente em aplicativos de fornecedores verificados.

## <a name="configure-permission-classifications-preview"></a>Configurar classificações de permissão (versão prévia)

As classificações de permissão permitem que você identifique o impacto que as diferentes permissões têm de acordo com as políticas e avaliações de risco de sua organização. Por exemplo, você pode usar classificações de permissão em políticas de consentimento para identificar o conjunto de permissões no qual os usuários têm permissão para consentir.

> [!NOTE]
> Atualmente, há suporte apenas à classificação de permissão "Pouco impacto". Somente as permissões delegadas que não exijam consentimento de administrador podem ser classificadas como de "Pouco impacto".

### <a name="classify-permissions-using-the-azure-portal"></a>Classificar permissões usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) como um [Administrador Global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Consentimento e permissões** > **Classificações de permissão**.
1. Escolha **Adicionar permissões** para classificar outra permissão como de "Pouco impacto". 
1. Selecione a API e as permissões delegadas.

Neste exemplo, classificamos o conjunto mínimo de permissões obrigatórias para o logon único:

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Classificações de permissão":::

> [!TIP]
> Para a API do Microsoft Graph, as permissões mínimas necessárias para fazer logon único básico são `openid`, `profile`, `User.Read` e `offline_access`. Com essas permissões, um aplicativo pode ler os detalhes do perfil do usuário conectado e manter esse acesso mesmo quando o usuário não está mais usando o aplicativo.

### <a name="classify-permissions-using-powershell"></a>Classificar permissões usando o PowerShell

Você pode usar o módulo de versão prévia mais recente do PowerShell do Azure AD, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), para classificar permissões. As classificações de permissão são configuradas no objeto **ServicePrincipal** da API que publica as permissões.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Para ler as classificações de permissão atuais de uma API:

1. Recupere o objeto **ServicePrincipal** para a API. Aqui, recuperamos o objeto ServicePrincipal para a API do Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Leia as classificações de permissão delegadas para a API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Para classificar uma permissão como de "Pouco impacto":

1. Recupere o objeto **ServicePrincipal** para a API. Aqui, recuperamos o objeto ServicePrincipal para a API do Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Localize a permissão delegada que você deseja classificar:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Defina a classificação de permissão usando o nome e a ID da permissão:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Para remover uma classificação de permissão delegada:

1. Recupere o objeto **ServicePrincipal** para a API. Aqui, recuperamos o objeto ServicePrincipal para a API do Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Localize a classificação de permissão delegada que você deseja remover:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Exclua a classificação de permissão:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configurar o consentimento do proprietário do grupo em aplicativos que acessam dados do grupo

Os proprietários do grupo podem autorizar que aplicativos, como aplicativos publicados por fornecedores terceirizados, acessem os dados da sua organização associados a um grupo. Por exemplo, um proprietário de equipe no Microsoft Teams pode permitir que um aplicativo leia todas as mensagens do Teams na equipe ou liste o perfil básico dos membros de um grupo.

Você pode configurar quais usuários têm permissão para consentir nos aplicativos que acessam os dados de seus grupos ou pode desabilitar esse recurso.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Configurar o consentimento do proprietário do grupo usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) como um [Administrador Global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Consentimento e permissões** > **Configurações de consentimento do usuário**.
3. Em **Consentimento do proprietário do grupo em aplicativos que acessam dados**, selecione a opção que deseja habilitar.
4. Selecione **salvar** para salvar suas configurações.

Neste exemplo, todos os proprietários de grupo têm permissão para dar consentimento aos aplicativos que acessam os dados de seus grupos:

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Configurações de consentimento do proprietário do grupo":::

### <a name="configure-group-owner-consent-using-powershell"></a>Configurar o consentimento do proprietário do grupo usando o PowerShell

Você pode usar o módulo de versão prévia do PowerShell do Azure AD, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), para habilitar ou desabilitar a capacidade dos proprietários do grupo de dar consentimento a aplicativos que acessam os dados da sua organização nos grupos que eles possuem.

1. Verifique se você está usando o módulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview). Esta etapa será importante se você tiver instalado os módulos [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) e [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Conecte-se ao Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Recupere o valor atual das configurações do diretório **Configurações de Política de Consentimento** em seu locatário. Isso exige verificar se as configurações de diretório para esse recurso foram criadas e, se não, usar os valores do modelo de configurações de diretório correspondente.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Entenda os valores de configuração. Há dois valores de configurações que definem quais usuários podem permitir que um aplicativo acesse os dados do seu grupo:

    | Configuração       | Type         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean | Sinalizador que indica se os proprietários de grupos têm permissão para conceder permissões específicas do grupo. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Se _EnableGroupSpecificConsent_ for definido como "true" e esse valor for definido como a ID de objeto de um grupo, os membros do grupo identificado terão autorização para conceder permissões específicas do grupo aos grupos que eles possuem. |

1. Atualize os valores das configurações para a configuração desejada:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Salve suas configurações.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Configurar o consentimento de step-up baseado em risco

O consentimento de step-up baseado em risco ajuda a reduzir a exposição do usuário a aplicativos mal-intencionados que fazem [solicitações de consentimento ilícitas](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Se a Microsoft detectar uma solicitação arriscada de consentimento ao usuário final, a solicitação exigirá um "step-up" para o consentimento do administrador. Esse recurso é habilitado por padrão, mas só resultará em uma alteração de comportamento quando o consentimento do usuário final estiver habilitado.

Quando uma solicitação de consentimento arriscado for detectada, a solicitação de consentimento exibirá uma mensagem indicando que a aprovação do administrador é necessária. Se o [fluxo de trabalho de solicitação de consentimento do administrador](configure-admin-consent-workflow.md) estiver habilitado, o usuário poderá enviar a solicitação a um administrador para análise diretamente do prompt de consentimento. Se não estiver habilitado, a seguinte mensagem será exibida:

* **AADSTS90094:** &lt;clientAppDisplayName&gt; precisa de permissão para acessar recursos em sua organização que apenas um administrador pode conceder. Peça a um administrador para conceder permissão ao aplicativo antes de poder usá-lo.

Nesse caso, um evento de auditoria também será registrado com a categoria "ApplicationManagement", o tipo de atividade "Consentimento para aplicativo" e o motivo do status "Aplicativo arriscado detectado".

> [!IMPORTANT]
> Os administradores devem [avaliar todas as solicitações de consentimento](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) cuidadosamente antes de aprová-las, especialmente quando a Microsoft tiver detectado risco.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Desabilitar ou reabilitar o consentimento de step-up baseado em risco usando o PowerShell

Você pode usar o módulo de versão prévia do PowerShell do Azure AD, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), para desabilitar o step-up até o consentimento do administrador necessário quando a Microsoft detecta um risco ou reabilitá-lo se tiver sido desabilitado anteriormente.

Você pode fazer isso usando as mesmas etapas mostradas acima para [configurar o consentimento do proprietário do grupo usando o PowerShell](#configure-group-owner-consent-using-powershell), mas substituindo por um valor de configurações diferente. Há três diferenças nas etapas: 

1. Entenda os valores de configuração do consentimento de step-up baseado em risco:

    | Configuração       | Type         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Sinalizador que indica se o consentimento do usuário será bloqueado quando uma solicitação arriscada for detectada. |

1. Substitua o seguinte valor na etapa 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Substitua um dos seguintes aspectos na etapa 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais:

* [Configurar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)
* [Saiba como gerenciar o consentimento em aplicativos e avaliar solicitações de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador em todo o locatário para um aplicativo](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
