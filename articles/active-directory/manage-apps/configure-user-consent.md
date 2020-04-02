---
title: Configure como os usuários finais concordam com aplicativos usando o Azure AD
description: Saiba como gerenciar como e quando os usuários podem consentir com aplicativos que terão acesso aos dados da sua organização.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519618"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configure como os usuários finais concordam com aplicativos

Os aplicativos podem se integrar à plataforma Microsoft Identity para permitir que os usuários entrem usando sua conta de trabalho ou escola no Azure Active Directory (Azure AD) e acessar os dados da sua organização para oferecer experiências ricas orientadas a dados. Diferentes permissões permitem ao aplicativo diferentes níveis de acesso aos dados de seus usuários e de sua organização.

Por padrão, os usuários podem consentir com aplicativos que acessam os dados da sua organização, embora apenas para algumas permissões. Por exemplo, por padrão, um usuário pode consentir em permitir que um aplicativo acesse sua própria caixa de correio ou as conversas das Equipes para uma equipe que o usuário possui, mas não pode consentir em permitir que um aplicativo não compareça ao acesso para ler e escrever em todos os sites do SharePoint em sua organização. Embora permitir que os usuários consentirem por si mesmos permite que os usuários adquiram facilmente aplicativos úteis que se integram com o Microsoft 365, Azure e outros serviços, ele pode representar um risco se não for usado e monitorado cuidadosamente.

A Microsoft recomenda desativar futuras operações de consentimento do usuário para ajudar a reduzir sua área de superfície e mitigar esse risco. Se o consentimento do usuário for desativado, as concessões de consentimento anteriorainda serão honradas, mas todas as futuras operações de consentimento devem ser realizadas por um administrador. O consentimento do admin em todo o inquilino pode ser solicitado pelos usuários através de um fluxo de trabalho de [solicitação de consentimento](configure-admin-consent-workflow.md) integrado ou através de seus próprios processos de suporte. Consulte [Cinco passos para proteger sua infra-estrutura de identidade](../../security/fundamentals/steps-secure-identity.md) para obter mais detalhes.

## <a name="configure-user-consent-to-applications"></a>Configure o consentimento do usuário para aplicativos
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Desativar ou ativar o consentimento do usuário a partir do portal Azure

Você pode usar o portal Azure para desativar ou habilitar a capacidade dos usuários de consentir em aplicativos que acessam os dados da sua organização:

1. Faça login no [portal Azure](https://portal.azure.com) como [administrador global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
2. Selecione **O Diretório Ativo do Azure,** depois os aplicativos **Corporativos**e **as configurações do usuário**.
3. Habilitar ou desativar o consentimento do usuário com o controle rotulado **Os usuários podem consentir com aplicativos que acessam os dados da empresa em seu nome**.
4. (Opcional) Configure o fluxo de trabalho de [solicitação de consentimento](configure-admin-consent-workflow.md) do administrador para garantir que os usuários que não têm permissão para consentir com um aplicativo possam solicitar aprovação.

> [!TIP]
> Para permitir que os usuários solicitem a revisão de um aplicativo ao administrador ao qual o usuário não pode consentir (por exemplo, porque o consentimento do usuário foi desativado, ou porque o aplicativo está solicitando permissões que o usuário não está autorizado a conceder), considere [configurar o fluxo de trabalho de consentimento](configure-admin-consent-workflow.md)do administrador .

### <a name="disable-or-enable-user-consent-using-powershell"></a>Desativar ou ativar o consentimento do usuário usando o PowerShell

Você pode usar o módulo Azure AD PowerShell v1[(MSOnline),](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)para ativar ou desativar a capacidade dos usuários de consentir em aplicativos que acessam os dados da sua organização.

1. Faça login na sua organização executando este cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Verifique se o consentimento do usuário está ativado executando este cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Habilite ou desative o consentimento do usuário. Por exemplo, para desativar o consentimento do usuário, execute este cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configure o consentimento do proprietário do grupo para aplicativos que acessam dados de grupo

> [!IMPORTANT]
> As informações a seguir são para um recurso que permitirá aos proprietários de grupos conceder acesso aos dados de seus grupos. Quando esse recurso for liberado, ele será ativado por padrão. Embora este recurso ainda não tenha sido lançado amplamente, você pode usar essas instruções para desativar o recurso antes de sua liberação.

Os proprietários de grupos podem autorizar aplicativos (por exemplo, aplicativos publicados por fornecedores de terceiros) para acessar os dados de sua organização associados a um grupo. Por exemplo, um proprietário de equipe (que é o proprietário do Grupo Office 365 para a equipe) pode permitir que um aplicativo leia todas as mensagens de Equipes na equipe ou liste o perfil básico dos membros de um grupo.

> [!NOTE]
> Independente dessa configuração, um proprietário de grupo é sempre autorizado a adicionar outros usuários ou aplicativos diretamente como proprietários de grupo.

### <a name="configure-group-owner-consent-using-powershell"></a>Configure o consentimento do proprietário do grupo usando o PowerShell

Você pode usar o módulo Azure AD PowerShell Preview[(AzureADPreview),](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para ativar ou desativar a capacidade dos proprietários de grupos de consentir em aplicativos que acessam os dados da sua organização para os grupos que possuem.

1. Certifique-se de que você está usando o módulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (esta etapa é importante se você tiver instalado o módulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) e o módulo [AzureADPreview).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Conecte-se ao Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Recupere o valor atual para as configurações do diretório *Configurações da diretiva de consentimento* em seu inquilino. Isso requer verificar se as configurações do diretório para este recurso foram criadas e, se não, usando os valores do modelo de configurações de diretório correspondentes.

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

4. Entenda os valores de configuração. Existem dois valores de configuração que definem quais usuários seriam capazes de permitir que um aplicativo acesse os dados de seu grupo:

    | Configuração       | Type         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _HabilitarOConsentimento específico do Grupo_   | Boolean |  Sinalizar indicando se os proprietários de grupos podem conceder permissões específicas do grupo. |
    | _constrangimentoMembroEspecíficosdoMembroMembrosdoGroupId_ | Guid | Se _EnableGroupSpecificConsent_ estiver definido como "True" e esse valor definido como ID de objeto de um grupo, os membros do grupo identificado serão autorizados a conceder permissões específicas do grupo aos grupos que possuem. |

5. Atualizar os valores das configurações para a configuração desejada:

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

6. Salvar configurações.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Configure o consentimento de intensificação baseado em risco

O consentimento de intensificação baseado em riscos ajuda a reduzir a exposição do usuário a aplicativos maliciosos que fazem [solicitações de consentimento ilícito.](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Se a Microsoft detectar uma solicitação de consentimento de usuário final arriscada, a solicitação exigirá um "step-up" para o consentimento do admin. Esse recurso é ativado por padrão, mas só resultará em uma mudança de comportamento quando o consentimento do usuário final estiver ativado.

Quando uma solicitação de consentimento de risco for detectada, o prompt de consentimento exibirá uma mensagem indicando que a aprovação do admin é necessária. Se o fluxo de trabalho da [solicitação de consentimento do admin](configure-admin-consent-workflow.md) estiver habilitado, o usuário poderá enviar a solicitação a um admin para posterior revisão diretamente do prompt de consentimento. Se não estiver habilitada, a seguinte mensagem será exibida:

* **AADSTS90094:** &lt;clientAppDisplayName&gt; precisa de permissão para acessar recursos em sua organização que apenas um admin pode conceder. Peça ao administrador para conceder permissão para este aplicativo antes de usá-lo.

Neste caso, um evento de auditoria também será registrado com uma categoria de "ApplicationManagement", tipo de atividade de "Consentimento para aplicação" e Razão de Status de "Aplicativo de risco detectado".

> [!IMPORTANT]
> Os admins devem [avaliar todas as solicitações de consentimento](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) cuidadosamente antes de aprovar, especialmente quando a Microsoft detectou risco.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Desativar ou reativar o consentimento de intensificação baseado em risco usando o PowerShell

Você pode usar o módulo Azure AD PowerShell Preview[(AzureADPreview),](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para desativar o passo-up para o consentimento administrativo necessário nos casos em que a Microsoft detecta risco ou para reativá-lo se ele estiver desativado anteriormente.

Isso pode ser feito usando as mesmas etapas mostradas acima para [configurar o consentimento do proprietário do grupo usando o PowerShell,](#configure-group-owner-consent-using-powershell)mas substituindo um valor de configurações diferente. Há três diferenças nos passos: 

1. Entenda os valores de configuração para o consentimento de intensificação baseado em risco:

    | Configuração       | Type         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _BlockuserconsentForRiskyApps_   | Boolean |  Sinalizador indicando se o consentimento do usuário será bloqueado quando uma solicitação arriscada for detectada. |

2. Substitua o seguinte valor na etapa 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. Substitua um dos seguintes passos 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Próximas etapas

[Configure o fluxo de trabalho de consentimento de administrador](configure-admin-consent-workflow.md)

[Saiba como gerenciar o consentimento para aplicativos e avaliar solicitações de consentimento](manage-consent-requests.md)

[Conceder consentimento de admin em todo o inquilino para um aplicativo](grant-admin-consent.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
