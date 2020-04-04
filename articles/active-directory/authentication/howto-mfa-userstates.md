---
title: Autenticação multifatorial por usuário - Diretório Ativo do Azure
description: Habilite o MFA alterando estados de usuário na Autenticação Multifatorial do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e955a3d10eea0a23154396a44fe6e1fc552a9ccb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653485"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Como exigir a verificação em duas etapas para um usuário

Você pode adotar uma das duas abordagens para solicitar a verificação em duas etapas, sendo que ambas exigem o uso de uma conta de administrador global. A primeira opção é habilitar cada usuário para a Autenticação Multifator do Azure (MFA). Quando os usuários são habilitados individualmente, eles executam a verificação em duas etapas em cada entrada (com algumas exceções, como ao entrarem a partir de endereços IP confiáveis, ou quando o recurso de _dispositivos lembrados_ estiver ativado). A segunda opção é configurar uma política de acesso condicional que exija verificação em duas etapas sob certas condições.

> [!TIP]
> Habilitar a autenticação multifatorial do Azure usando políticas de acesso condicional é a abordagem recomendada. A alteração dos estados dos usuários não é mais recomendada, a menos que suas licenças não incluam acesso condicional, pois exigirá que os usuários realizem o MFA toda vez que fizerem login.

## <a name="choose-how-to-enable"></a>Escolha como habilitar

**Habilitado alterando o estado do usuário** - Esse é o método tradicional para exigir a verificação em duas etapas e é abordado neste artigo. Ele funciona tanto para MFA do Azure na nuvem como Servidor de MFA do Azure. O uso desse método exige que os usuários realizem a verificação em duas **etapas cada vez** que fazem login e sobrepõe as políticas de Acesso Condicional.

**Ativado pela política de acesso condicional** - Este é o meio mais flexível para habilitar a verificação em duas etapas para seus usuários. Habilitar o uso da política de Acesso Condicional só funciona para o Azure MFA na nuvem e é um recurso premium do Azure AD. Mais informações sobre esse método podem ser encontradas em [Implantar Autenticação Multifator do Microsoft Azure baseada em nuvem](howto-mfa-getstarted.md).

**Ativado pelo Azure AD Identity Protection** - Este método usa a política de risco azure AD Identity Protection para exigir verificação em duas etapas com base apenas no risco de login para todos os aplicativos em nuvem. Este método requer o licenciamento do Azure Active Directory P2. Mais informações sobre este método podem ser encontradas no [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Mais informações sobre licenças e preços podem ser encontradas nas páginas de preço do [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) e [Autenticação Multifator do Microsoft Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="enable-azure-mfa-by-changing-user-state"></a>Habilitar o MFA do Azure alterando o estado do usuário

As contas de usuário na Autenticação Multifator do Azure apresentam os três estados distintos a seguir:

> [!IMPORTANT]
> Habilitar o Azure MFA através de uma política de Acesso Condicional não mudará o estado do usuário. Não se assuste, os usuários parecem desativados. O Acesso Condicional não muda o estado. **As organizações não devem habilitar ou impor usuários se estiverem utilizando políticas de acesso condicional.**

| Status | Descrição | Aplicativos que não usam navegador afetados | Aplicativos que usam o navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desabilitado | O estado padrão para um novo usuário não inscrito na MFA do Azure. | Não | Não | Não |
| habilitado | O usuário foi inscrito no MFA do Azure, mas não foi registrado. Eles receberão uma solicitação para se registrarem na próxima vez que entrarem. | Não.  Eles continuarão a trabalhar até o processo ser concluído. | Sim. Depois que a sessão expirar, será exigido o registro da MFA do Azure.| Sim. Depois que o token de acesso expirar, será exigido o registro da MFA do Azure. |
| Imposto | O usuário foi inscrito e concluiu o processo de registro para usar a MFA do Azure. | Sim. Os aplicativos exigem senhas de aplicativo. | Sim. A MFA do Azure é exigida no logon. | Sim. A MFA do Azure é exigida no logon. |

O estado de um usuário reflete se um administrador o registrou na MFA do Azure e se ele concluiu o processo de registro.

Todos os usuários começam com o status *Desabilitado*. Quando você registra os usuários na MFA do Azure, seu estado é alterado para *Habilitado*. Quando usuários habilitados entram e concluem o processo de registro, seu estado é alterado para *Imposto*.

> [!NOTE]
> Se o MFA for reativado em um objeto de usuário que já tenha detalhes cadastrais, como telefone ou e-mail, os administradores precisarão que o usuário recadastre o MFA via portal Azure ou PowerShell. Se o usuário não fizer o recadastramento, seu estado MFA não faz a transição de *Habilitado* para *Aplicado* na UI de gerenciamento de MFA.

### <a name="view-the-status-for-a-user"></a>Exibir o status de um usuário

Use as etapas a seguir para acessar a página em que você pode exibir e gerenciar estados de usuário:

1. Faça login no [portal Azure](https://portal.azure.com) como administrador.
2. Pesquise *Azure Active Directory* e selecione-o. Selecione **usuários Todos** > **os usuários**.
3. Selecione **Autenticação multifatorial**. Você pode precisar rolar para a direita para ver esta opção de menu. Selecione a captura de tela de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecione Autenticação multifatorial na janela Usuários no Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. Uma nova página que exibe os estados do usuário é aberta.
   ![estados do usuário da Autenticação Multifator do Azure – captura de tela](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Alterar o status de um usuário

1. Use as etapas anteriores para chegar à página de **usuários** da Autenticação Multifator do Azure.
2. Localize o usuário que deseja habilitar para a MFA do Azure. Talvez seja necessário alterar o modo de exibição na parte superior.
   ![Selecione o usuário para alterar o status na guia usuários](./media/howto-mfa-userstates/enable1.png)
3. Marque a caixa, ao lado do nome.
4. À direita, em **etapas rápidas**, escolha **Habilitar** ou **Desabilitar**.
   ![Habilite o usuário selecionado clicando em Ativar no menu passos rápidos](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Usuários *Habilitados* mudam automaticamente para *Imposto* ao se registrarem na MFA do Azure. Não altere manualmente o estado do usuário para *Imposto*.

5. Confirme sua seleção na janela pop-up que é aberta.

Depois de habilitar os usuários, notifique-os por email. Informe que eles receberão uma solicitação de registro na próxima vez que entrarem. Além disso, se sua organização usar aplicativos sem navegador que não dão suporte à autenticação moderna, eles precisam criar senhas de aplicativo. Você também pode incluir um link para o [Guia do usuário final da MFA do Azure](../user-help/multi-factor-authentication-end-user.md) para ajudá-los a começar.

### <a name="use-powershell"></a>Usar o PowerShell

Para alterar o estado do usuário usando o [PowerShell do Azure AD](/powershell/azure/overview), altere `$st.State`. Há três opções de estado possíveis:

* habilitado
* Imposto
* Desabilitado  

Não mova os usuários diretamente para o estado *Imposto*. Caso os mova, aplicativos não baseados em navegador deixarão de funcionar porque o usuário não passou pelo registro do MFA do Azure nem obteve uma [senha de aplicativo](howto-mfa-mfasettings.md#app-passwords).

Instale o módulo em primeiro lugar, usando:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Não se esqueça de se conectar pela primeira vez usando **Connect-MsolService**

   ```PowerShell
   Connect-MsolService
   ```

Este exemplo de script do PowerShell habilita a MFA para um usuário individual:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Usar o PowerShell é uma boa opção quando você precisa habilitar usuários em massa. Por exemplo, o script a seguir executa um loop por uma lista de usuários e habilita a MFA em suas contas:

   ```PowerShell
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

Para desabilitar a MFA, use este script:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

que também pode ser encurtado para:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter usuários de MFA por usuário para MFA baseado em Acesso Condicional

O PowerShell a seguir pode ajudá-lo a fazer a conversão para autenticação multifatorial baseada no Acesso Condicional.

Execute este PowerShell em uma janela ISE ou salve como um . Arquivo PS1 para executar localmente.

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
> Recentemente mudamos o comportamento e o script PowerShell acima em conformidade. Anteriormente, o script salvava os métodos mfa, desativava o MFA e restaurava os métodos. Isso não é mais necessário agora que o comportamento padrão para desabilitação não limpa os métodos.
>
> Se o MFA for reativado em um objeto de usuário que já tenha detalhes cadastrais, como telefone ou e-mail, os administradores precisarão que o usuário recadastre o MFA via portal Azure ou PowerShell. Se o usuário não fizer o recadastramento, seu estado MFA não faz a transição de *Habilitado* para *Aplicado* na UI de gerenciamento de MFA.

## <a name="next-steps"></a>Próximas etapas

* Por que foi um usuário solicitado ou não é solicitado a executar a MFA? Consulte a seção [relatório de entradas do Microsoft Azure Active Directory nos relatórios do documento de autenticação multifator do Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Para definir configurações adicionais, como IPs confiáveis, mensagens de voz personalizadas e alertas de fraudes, consulte o artigo [Definir configurações de Autenticação Multifator do Microsoft Azure](howto-mfa-mfasettings.md)
* Informações sobre como gerenciar configurações de usuário para Autenticação Multifator do Microsoft Azure podem ser encontradas no artigo [Gerenciar configurações do usuário com Autenticação Multifator do Microsoft Azure na nuvem](howto-mfa-userdevicesettings.md)
