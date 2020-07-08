---
title: Habilitar a Autenticação Multifator individual – Azure Active Directory
description: Saiba como habilitar a Autenticação Multifator individual do Azure alterando o estado do usuário
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
ms.openlocfilehash: e8ef25df8fdb11715ebba954e31a97939d6ac0e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476828"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Habilitar a Autenticação Multifator individual do Azure para proteção de acesso

Há duas maneiras de proteger acessos dos usuários através da autenticação multifator no Azure AD. A primeira, e recomendada, é configurar uma política de Acesso Condicional que exige a autenticação multifator sob determinadas condições. A segunda é habilitar cada usuário para a Autenticação Multifator do Azure. Quando os usuários são habilitados individualmente, eles executam a autenticação multifator em cada acesso (com algumas exceções, como quando eles acessam a partir de endereços IP confiáveis, ou quando o recurso de _dispositivos lembrados_ estiver ativado).

> [!NOTE]
> Habilitar a Autenticação Multifator do Azure usando políticas de Acesso Condicional é a abordagem recomendada. Não é mais recomendável alterar os estados do usuário, salvo se suas licenças não incluem o Acesso Condicional, pois elas exigem que os usuários executem a MFA em cada acesso. Para começar a usar o Acesso Condicional, veja [Tutorial: proteger acessos do usuário com a Autenticação Multifator do Azure](tutorial-enable-azure-mfa.md).
>
> Para locatários gratuitos do Azure AD sem acesso condicional, você pode [usar os padrões de segurança para proteger os usuários](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Estados de usuário da Autenticação Multifator do Azure

As contas de usuário na Autenticação Multifator do Azure apresentam os três estados distintos a seguir:

> [!IMPORTANT]
> Habilitar a Autenticação Multifator do Azure através de política de Acesso Condicional não altera o estado do usuário. Não se assuste se os usuários parecem desabilitados. O Acesso Condicional não altera o estado.
>
> **Você não deve habilitar ou impor usuários se estiver usando políticas de Acesso Condicional.**

| Status | Descrição | Aplicativos que não usam navegador afetados | Aplicativos que usam o navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desabilitado | O estado padrão para um novo usuário não inscrito na Autenticação Multifator do Azure. | Não | Não | Não |
| habilitado | O usuário foi registrado na autenticação multifator do Azure, mas não registrou os métodos de autenticação. Eles receberão uma solicitação para se registrarem na próxima vez que entrarem. | Não.  Eles continuarão a trabalhar até o processo ser concluído. | Sim. Depois que a sessão expirar, o registro da Autenticação Multifator do Azure será exigido.| Sim. Depois que o token de acesso expirar, o registro da Autenticação Multifator do Azure será exigido. |
| Imposto | O usuário foi inscrito e concluiu o processo de registro da Autenticação Multifator do Azure. | Sim. Os aplicativos exigem senhas de aplicativo. | Sim. A Autenticação Multifator do Azure é exigida para logon. | Sim. A Autenticação Multifator do Azure é exigida para logon. |

O estado de um usuário reflete se um administrador o registrou na Autenticação Multifator do Azure e se ele concluiu o processo de registro.

Todos os usuários começam com o status *Desabilitado*. Quando você registra os usuários na Autenticação Multifator do Azure, seu estado é alterado para *Habilitado*. Quando usuários habilitados entram e concluem o processo de registro, seu estado é alterado para *Imposto*.

> [!NOTE]
> Se a MFA for habilitada novamente para objeto de usuário que já tem detalhes de registro, como telefone ou email, os administradores precisarão fazer com que o usuário se registre novamente para MFA através do portal do Azure ou do PowerShell. Se o usuário não se registrar novamente, seu estado de MFA não fará a transição de *Habilitada* para *Imposta* na interface do usuário de gerenciamento da MFA.

## <a name="view-the-status-for-a-user"></a>Exibir o status de um usuário

Use as etapas a seguir para acessar a página do portal do Azure onde você pode exibir e gerenciar os estados do usuário:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
1. Pesquise e selecione *Azure Active Directory* e, em seguida, selecione **Usuários** > **Todos os usuários**.
1. Selecione **Autenticação Multifator**. Role para a direita para ver esta opção de menu. Selecione a captura de tela de exemplo abaixo para ver toda a janela do portal do Azure e a localização do menu: [![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecionar a Autenticação Multifator na janela Usuários no Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Uma nova página abre e exibe o estado do usuário, conforme mostrado no exemplo a seguir.
   ![Captura de tela que mostra informações do estado do usuário de exemplo para a Autenticação Multifator do Azure](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Alterar o status de um usuário

Para alterar o estado da Autenticação Multifator do Azure para um usuário, siga estas etapas:

1. Use as etapas anteriores para chegar à página de **usuários** da Autenticação Multifator do Azure.
1. Localize o usuário que deseja habilitar para a Autenticação Multifator do Azure. Altere o modo de exibição na parte superior para **usuários**, se necessário.
   ![Selecione o usuário desejado na guia de usuários e altere o seu estado](./media/howto-mfa-userstates/enable1.png)
1. Marque a caixa ao lado do nome de um usuário para alterar o seu estado.
1. No lado direito, em **etapas rápidas**, escolha **Habilitar** ou **Desabilitar**. No exemplo a seguir, o usuário *John Smith* tem uma marca de seleção ao lado do seu nome e está habilitado para uso: ![Habilitar o usuário selecionado clicando em Habilitar no menu etapas rápidas](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Usuários *Habilitados* mudam automaticamente para *Imposto* ao se registrarem na Autenticação Multifator do Azure. Não altere manualmente o estado do usuário para *Imposto*.

1. Confirme sua seleção na janela pop-up que é aberta.

Depois de habilitar os usuários, notifique-os por email. Informe aos usuários que um aviso é exibido para pedir que eles se registrem no próximo acesso. Além disso, se sua organização usar aplicativos sem navegador que não dão suporte à autenticação moderna, eles precisam criar senhas de aplicativo. Para obter mais informações, confira [Guia do usuário final para Autenticação Multifator do Azure](../user-help/multi-factor-authentication-end-user.md) para ajudá-los a começar a usar este recurso.

## <a name="change-state-using-powershell"></a>Alteração do estado usando o PowerShell

Para alterar o estado do usuário usando o [PowerShell do Azure AD](/powershell/azure/overview), altere o parâmetro `$st.State` para uma conta de usuário. Há três opções de estado possíveis para uma conta de usuário:

* *Enabled*
* *Imposto*
* *Desabilitado*  

Não mova os usuários diretamente para o estado *Imposto*. Caso o faça, os aplicativos que não são baseados em navegador deixarão de funcionar porque o usuário não passou pelo registro da Autenticação Multifator do Azure nem obteve uma [senha de aplicativo](howto-mfa-app-passwords.md).

Para começar, instale o módulo *MSOnline* usando [Install-Module](/powershell/module/powershellget/install-module) conforme a seguir:

```PowerShell
Install-Module MSOnline
```

Em seguida, conecte-se usando [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Este exemplo de script do PowerShell habilita a MFA para um usuário individual chamado *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Usar o PowerShell é uma boa opção quando você precisa habilitar usuários em massa. O script a seguir executa um loop em uma lista de usuários e habilita a MFA em suas contas. Defina as contas de usuário na primeira linha para `$users` da seguinte maneira:

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

Para desabilitar a MFA, o exemplo a seguir obtém um usuário com [Get-MsolUser](/powershell/module/msonline/get-msoluser) e, em seguida, remove qualquer *StrongAuthenticationRequirements* configurado para o usuário definido usando [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Você também pode desabilitar diretamente a MFA para um usuário usando [Set-MsolUser](/powershell/module/msonline/set-msoluser) da seguinte maneira:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Conversão de usuários da MFA individual para a MFA baseada em Acesso Condicional

O PowerShell a seguir ajuda a fazer a conversão para a Autenticação Multifator do Azure baseada em Acesso Condicional.

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
> Alteramos recentemente o comportamento e o script do PowerShell. Anteriormente, o script excluía os métodos de MFA, desabilitava a MFA e restaurava os métodos. Isso não é mais necessário, agora que o comportamento padrão para desabilitar não limpa os métodos.
>
> Se a MFA for habilitada novamente para objeto de usuário que já tem detalhes de registro, como telefone ou email, os administradores precisarão fazer com que o usuário se registre novamente para MFA através do portal do Azure ou do PowerShell. Se o usuário não se registrar novamente, seu estado de MFA não fará a transição de *Habilitada* para *Imposta* na interface do usuário de gerenciamento da MFA.

## <a name="next-steps"></a>Próximas etapas

Para definir configurações de Autenticação Multifator do Azure, como IPs confiáveis, mensagens de voz personalizadas e alertas de fraudes, veja o artigo [Definição de configurações de Autenticação Multifator do Azure](howto-mfa-mfasettings.md). Para gerenciar as configurações do usuário para Autenticação Multifator do Azure , veja [Gerenciamento das configurações do usuário com Autenticação Multifator do Azure](howto-mfa-userdevicesettings.md).

Para entender por que um usuário foi solicitado ou não a executar a MFA, veja [Relatórios de Autenticação Multifator do Azure](howto-mfa-reporting.md).
