---
title: Habilitar a autenticação multifator por usuário-Azure Active Directory
description: Saiba como habilitar a autenticação multifator do Azure por usuário alterando o estado do usuário
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309775"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Habilitar a autenticação multifator do Azure por usuário para proteger eventos de entrada

Há duas maneiras de proteger eventos de entrada do usuário exigindo a autenticação multifator no Azure AD. A primeira opção e preferencial é configurar uma política de acesso condicional que exija a autenticação multifator em determinadas condições. A segunda opção é habilitar cada usuário para a autenticação multifator do Azure. Quando os usuários são habilitados individualmente, eles executam a autenticação multifator cada vez que entram (com algumas exceções, como quando eles entram em endereços IP confiáveis ou quando o recurso de _dispositivos lembrados_ está ativado).

> [!NOTE]
> Habilitar a autenticação multifator do Azure usando políticas de acesso condicional é a abordagem recomendada. Não é mais recomendável alterar os Estados do usuário, a menos que suas licenças não incluam o acesso condicional, pois eles exigem que os usuários executem a MFA sempre que entrarem.
>
> Para começar a usar o acesso condicional, consulte [tutorial: proteger eventos de entrada do usuário com a autenticação multifator do Azure](tutorial-enable-azure-mfa.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Estados do usuário da autenticação multifator do Azure

As contas de usuário na Autenticação Multifator do Azure apresentam os três estados distintos a seguir:

> [!IMPORTANT]
> Habilitar a autenticação multifator do Azure por meio de uma política de acesso condicional não altera o estado do usuário. Não se assuste se os usuários aparecerem desabilitados. O acesso condicional não altera o estado.
>
> **Você não deve habilitar ou impor usuários se estiver usando políticas de acesso condicional.**

| Status | Descrição | Aplicativos que não usam navegador afetados | Aplicativos que usam o navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desabilitado | O estado padrão para um novo usuário não registrado na autenticação multifator do Azure. | Não | Não | Não |
| habilitado | O usuário foi inscrito na autenticação multifator do Azure, mas não foi registrado. Eles receberão uma solicitação para se registrarem na próxima vez que entrarem. | Não.  Eles continuarão a trabalhar até o processo ser concluído. | Sim. Depois que a sessão expirar, o registro da autenticação multifator do Azure será necessário.| Sim. Depois que o token de acesso expira, o registro da autenticação multifator do Azure é necessário. |
| Imposto | O usuário foi registrado e concluiu o processo de registro para a autenticação multifator do Azure. | Sim. Os aplicativos exigem senhas de aplicativo. | Sim. A autenticação multifator do Azure é necessária no logon. | Sim. A autenticação multifator do Azure é necessária no logon. |

O estado de um usuário reflete se um administrador o registrou na autenticação multifator do Azure e se concluiu o processo de registro.

Todos os usuários começam com o status *Desabilitado*. Quando você registra os usuários na autenticação multifator do Azure, seu estado muda para *habilitado*. Quando usuários habilitados entram e concluem o processo de registro, seu estado é alterado para *Imposto*.

> [!NOTE]
> Se a MFA for reabilitada em um objeto de usuário que já tem detalhes de registro, como telefone ou email, os administradores precisarão fazer com que o usuário registre novamente o MFA por meio do portal do Azure ou do PowerShell. Se o usuário não se registrar novamente, seu estado de MFA não fará a transição de *habilitado* para *imposto* na interface do usuário de gerenciamento do MFA.

## <a name="view-the-status-for-a-user"></a>Exibir o status de um usuário

Use as etapas a seguir para acessar a página de portal do Azure onde você pode exibir e gerenciar os Estados do usuário:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador.
1. Procure e selecione *Azure Active Directory*e, em seguida, selecione **usuários** > **todos os usuários**.
1. Selecione **autenticação multifator**. Talvez seja necessário rolar para a direita para ver essa opção de menu. Selecione a captura de tela de exemplo abaixo para ver a janela de portal do Azure completa e o local do menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecionar a autenticação multifator na janela usuários no Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Uma nova página é aberta e exibe o estado do usuário, conforme mostrado no exemplo a seguir.
   ![Captura de tela que mostra informações de estado do usuário de exemplo para a autenticação multifator do Azure](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Alterar o status de um usuário

Para alterar o estado da autenticação multifator do Azure para um usuário, conclua as seguintes etapas:

1. Use as etapas anteriores para chegar à página de **usuários** da Autenticação Multifator do Azure.
1. Localize o usuário que você deseja habilitar para a autenticação multifator do Azure. Talvez seja necessário alterar a exibição na parte superior para **os usuários**.
   ![Selecione o usuário para o qual alterar o status na guia usuários](./media/howto-mfa-userstates/enable1.png)
1. Marque a caixa ao lado dos nomes dos usuários para os quais alterar o estado.
1. No lado direito, em **etapas rápidas**, escolha **habilitar** ou **desabilitar**. No exemplo a seguir, o usuário *João Silva* tem uma marca de seleção ao lado de seu nome e está sendo habilitado ![para uso: habilitar o usuário selecionado clicando em habilitar no menu etapas rápidas](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Os usuários *habilitados* são automaticamente alternados para *imposto* quando se registram para a autenticação multifator do Azure. Não altere manualmente o estado do usuário para *imposto*.

1. Confirme sua seleção na janela pop-up que é aberta.

Depois de habilitar os usuários, notifique-os por email. Informe aos usuários que um prompt é exibido para pedir que eles se registrem na próxima vez que entrarem. Além disso, se sua organização usar aplicativos sem navegador que não dão suporte à autenticação moderna, eles precisam criar senhas de aplicativo. Para obter mais informações, consulte o [Guia do usuário final da autenticação multifator do Azure](../user-help/multi-factor-authentication-end-user.md) para ajudá-los a começar.

## <a name="change-state-using-powershell"></a>Alterar o estado usando o PowerShell

Para alterar o estado do usuário usando o [PowerShell do Azure ad](/powershell/azure/overview), altere `$st.State` o parâmetro de uma conta de usuário. Há três estados possíveis para uma conta de usuário:

* *Habilitada*
* *Imposto*
* *Desabilitada*  

Não mova os usuários diretamente para o estado *Imposto*. Se você fizer isso, aplicativos não baseados em navegador deixarão de funcionar porque o usuário não passou pelo registro da autenticação multifator do Azure e obteve uma [senha de aplicativo](howto-mfa-mfasettings.md#app-passwords).

Para começar, instale o módulo *MSOnline* usando [install-Module](/powershell/module/powershellget/install-module) da seguinte maneira:

```PowerShell
Install-Module MSOnline
```

Em seguida, conecte-se usando [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

O exemplo de script do PowerShell a seguir habilita a MFA para *bsimon@contoso.com*um usuário individual chamado:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Usar o PowerShell é uma boa opção quando você precisa habilitar usuários em massa. O script a seguir executa um loop em uma lista de usuários e habilita a MFA em suas contas. Defina as contas de usuário para defini-lo na primeira `$users` linha para, da seguinte maneira:

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Para desabilitar o MFA, o exemplo a seguir obtém um usuário com [Get-MsolUser](/powershell/module/msonline/get-msoluser)e, em seguida, remove qualquer *StrongAuthenticationRequirements* definido para o usuário definido usando [set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Você também pode desabilitar diretamente o MFA para um usuário usando [set-MsolUser](/powershell/module/msonline/set-msoluser) da seguinte maneira:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter usuários de MFA por usuário para MFA baseada em acesso condicional

O PowerShell a seguir pode ajudá-lo a fazer a conversão para o acesso condicional com base na autenticação multifator do Azure.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Alteramos recentemente o comportamento e esse script do PowerShell. Anteriormente, o script economizou os métodos de MFA, desabilitou a MFA e restaurei os métodos. Isso não é mais necessário agora que o comportamento padrão para desabilitar não limpa os métodos.
>
> Se a MFA for reabilitada em um objeto de usuário que já tem detalhes de registro, como telefone ou email, os administradores precisarão fazer com que o usuário registre novamente o MFA por meio do portal do Azure ou do PowerShell. Se o usuário não se registrar novamente, seu estado de MFA não fará a transição de *habilitado* para *imposto* na interface do usuário de gerenciamento do MFA.

## <a name="next-steps"></a>Próximas etapas

Para definir as configurações de autenticação multifator do Azure, como IPs confiáveis, mensagens de voz personalizadas e alertas de fraude, consulte [definir as configurações de autenticação multifator do Azure](howto-mfa-mfasettings.md). Para gerenciar as configurações de usuário para a autenticação multifator do Azure, consulte [gerenciar configurações de usuário com a autenticação multifator do Azure](howto-mfa-userdevicesettings.md).

Para entender por que um usuário foi solicitado ou não solicitado a executar a MFA, consulte [relatórios de autenticação multifator do Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).
