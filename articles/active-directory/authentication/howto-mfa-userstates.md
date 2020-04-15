---
title: Habilitar autenticação multifatorial por usuário - Diretório Ativo do Azure
description: Saiba como ativar a autenticação multifatorial do Azure por usuário alterando o estado do usuário
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309775"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Habilite a autenticação multifatorial do Azure por usuário para garantir eventos de login

Existem duas maneiras de proteger eventos de login do usuário, exigindo autenticação multifatorial no Azure AD. A primeira opção, e preferida, é configurar uma política de acesso condicional que exija autenticação multifatorial sob certas condições. A segunda opção é habilitar cada usuário para autenticação multifatorial do Azure. Quando os usuários são ativados individualmente, eles executam autenticação multifatorial cada vez que fazem login (com algumas exceções, como quando fazem login em endereços IP confiáveis ou quando o recurso _de dispositivos lembrados_ é ativado).

> [!NOTE]
> Habilitar a autenticação multifatorial do Azure usando políticas de acesso condicional é a abordagem recomendada. A alteração dos estados dos usuários não é mais recomendada, a menos que suas licenças não incluam o Conditional Access, pois exige que os usuários realizem o MFA toda vez que fizerem login.
>
> Para começar a usar o Conditional Access, consulte [Tutorial: Proteja eventos de login do usuário com autenticação multifatorial do Azure](tutorial-enable-azure-mfa.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Estados de usuário de autenticação multifatorial do Azure

As contas de usuário na Autenticação Multifator do Azure apresentam os três estados distintos a seguir:

> [!IMPORTANT]
> Habilitar a autenticação multifatorial do Azure por meio de uma política de acesso condicional não altera o estado do usuário. Não se assuste se os usuários parecerem desativados. O Acesso Condicional não muda o estado.
>
> **Você não deve habilitar ou impor usuários se estiver usando políticas de acesso condicional.**

| Status | Descrição | Aplicativos que não usam navegador afetados | Aplicativos que usam o navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desabilitado | O estado padrão para um novo usuário não está inscrito na Autenticação Multifatorial do Azure. | Não | Não | Não |
| habilitado | O usuário foi inscrito no Azure Multi-Factor Authentication, mas não se registrou. Eles receberão uma solicitação para se registrarem na próxima vez que entrarem. | Não.  Eles continuarão a trabalhar até o processo ser concluído. | Sim. Após o término da sessão, é necessário o registro de Autenticação Multifatorial do Azure.| Sim. Após o término do token de acesso, é necessário o registro de Autenticação Multifatorial do Azure. |
| Imposto | O usuário foi inscrito e completou o processo de registro da Autenticação Multifatorial do Azure. | Sim. Os aplicativos exigem senhas de aplicativo. | Sim. A autenticação multifatorial do Azure é necessária no login. | Sim. A autenticação multifatorial do Azure é necessária no login. |

O estado de um usuário reflete se um administrador os inscreveu na Autenticação Multifatorial do Azure e se eles concluíram o processo de registro.

Todos os usuários começam com o status *Desabilitado*. Quando você inscreve usuários na Autenticação Multifatorial do Azure, seu estado muda para *Habilitado*. Quando usuários habilitados entram e concluem o processo de registro, seu estado é alterado para *Imposto*.

> [!NOTE]
> Se o MFA for reativado em um objeto de usuário que já tenha detalhes cadastrais, como telefone ou e-mail, os administradores precisarão que o usuário recadastre o MFA via portal Azure ou PowerShell. Se o usuário não fizer o recadastramento, seu estado MFA não faz a transição de *Habilitado* para *Aplicado* na UI de gerenciamento de MFA.

## <a name="view-the-status-for-a-user"></a>Exibir o status de um usuário

Use as seguintes etapas para acessar a página do portal Azure, onde você pode visualizar e gerenciar estados de usuário:

1. Faça login no [portal Azure](https://portal.azure.com) como administrador.
1. Procure e selecione *o Azure Active Directory*e selecione **Usuários** > **Todos os usuários**.
1. Selecione **Autenticação multifatorial**. Você pode precisar rolar para a direita para ver esta opção de menu. Selecione a captura de tela de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecione Autenticação multifatorial na janela Usuários no Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Uma nova página é aberta que exibe o estado do usuário, como mostrado no exemplo a seguir.
   ![Captura de tela que mostra informações do estado do usuário de exemplo para autenticação multifatorial do Azure](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Alterar o status de um usuário

Para alterar o estado de autenticação multifatorial do Azure para um usuário, complete as seguintes etapas:

1. Use as etapas anteriores para chegar à página de **usuários** da Autenticação Multifator do Azure.
1. Encontre o usuário que deseja habilitar para autenticação multifatorial do Azure. Você pode precisar alterar a exibição na parte superior para **os usuários**.
   ![Selecione o usuário para alterar o status na guia usuários](./media/howto-mfa-userstates/enable1.png)
1. Verifique a caixa ao lado do nome(s) dos usuários para alterar o estado.
1. No lado direito, em **passos rápidos,** escolha **Ativar** ou **Desativar**. No exemplo a seguir, o usuário *John Smith* tem uma verificação ![ao lado de seu nome e está sendo habilitado para uso: Habilitar usuário selecionado clicando em Ativar no menu passos rápidos](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Os usuários *habilitados* são automaticamente comutados para *Executados* quando se registram para autenticação multifatorial do Azure. Não altere manualmente o estado do usuário para *Enforced*.

1. Confirme sua seleção na janela pop-up que é aberta.

Depois de habilitar os usuários, notifique-os por email. Diga aos usuários que um prompt é exibido para pedir que eles registrem na próxima vez que fizerem login. Além disso, se sua organização usar aplicativos sem navegador que não dão suporte à autenticação moderna, eles precisam criar senhas de aplicativo. Para obter mais informações, consulte o guia do usuário final da [Autenticação Multifatorial do Azure para ajudá-los](../user-help/multi-factor-authentication-end-user.md) a começar.

## <a name="change-state-using-powershell"></a>Alterar estado usando powershell

Para alterar o estado do usuário usando [o Azure AD PowerShell,](/powershell/azure/overview)você altera o `$st.State` parâmetro para uma conta de usuário. Existem três estados possíveis para uma conta de usuário:

* *habilitado*
* *Imposto*
* *Desabilitado*  

Não mova os usuários diretamente para o estado *Imposto*. Se você fizer isso, aplicativos não baseados em navegador param de funcionar porque o usuário não passou pelo registro de autenticação multifatorial do Azure e obteve uma senha do [aplicativo](howto-mfa-mfasettings.md#app-passwords).

Para começar, instale o módulo *MSOnline* usando [o Módulo de Instalação](/powershell/module/powershellget/install-module) da seguinte forma:

```PowerShell
Install-Module MSOnline
```

Em seguida, conecte-se usando [Connect-MsolService:](/powershell/module/msonline/connect-msolservice)

```PowerShell
Connect-MsolService
```

O exemplo a seguir, o script PowerShell habilita o MFA para um usuário individual chamado *bsimon@contoso.com*:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Usar o PowerShell é uma boa opção quando você precisa habilitar usuários em massa. O script a seguir faz loops através de uma lista de usuários e habilita o MFA em suas contas. Defina as contas de usuário `$users` definam-na na primeira linha da seguinte forma:

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

Para desativar o MFA, o exemplo a seguir obtém um usuário com [Get-MsolUser](/powershell/module/msonline/get-msoluser)e remove quaisquer *Requisitos de Autenticação Forte* definidos para o usuário definido usando [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Você também pode desativar diretamente o MFA para um usuário usando [Set-MsolUser](/powershell/module/msonline/set-msoluser) da seguinte forma:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter usuários de MFA por usuário para MFA baseado em Acesso Condicional

O PowerShell a seguir pode ajudá-lo a fazer a conversão para autenticação multifatorial baseada no Acesso Condicional.

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
> Recentemente mudamos o comportamento e este script PowerShell. Anteriormente, o script salvava os métodos mfa, desativava o MFA e restaurava os métodos. Isso não é mais necessário agora que o comportamento padrão para desabilitação não limpa os métodos.
>
> Se o MFA for reativado em um objeto de usuário que já tenha detalhes cadastrais, como telefone ou e-mail, os administradores precisarão que o usuário recadastre o MFA via portal Azure ou PowerShell. Se o usuário não fizer o recadastramento, seu estado MFA não faz a transição de *Habilitado* para *Aplicado* na UI de gerenciamento de MFA.

## <a name="next-steps"></a>Próximas etapas

Para configurar as configurações de autenticação multifatorial do Azure, como IPs confiáveis, mensagens de voz personalizadas e alertas de fraude, consulte [Configure configurações de autenticação multifatorial do Azure](howto-mfa-mfasettings.md). Para gerenciar as configurações do usuário para autenticação multifatorial do Azure, consulte [Gerenciar as configurações do usuário com a autenticação multifatorial do Azure](howto-mfa-userdevicesettings.md).

Para entender por que um usuário foi solicitado ou não a realizar o MFA, consulte [os relatórios de autenticação multifatorial do Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).
