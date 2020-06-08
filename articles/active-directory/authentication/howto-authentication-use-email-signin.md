---
title: Entrar com o email como uma ID de logon alternativa para o Azure Active Directory
description: Saiba como configurar e habilitar os usuários para entrarem no Azure Active Directory usando o endereço de email como uma ID de logon alternativa (versão preliminar)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837305"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>Entrar no Azure usando o email como uma ID de logon alternativa (versão preliminar)

Muitas organizações querem permitir que os usuários entrem no Azure usando as mesmas credenciais de seu ambiente de diretório local. Com essa abordagem, conhecida como autenticação híbrida, os usuários só precisam se lembrar de um conjunto de credenciais.

Algumas organizações não migraram para autenticação híbrida pelos seguintes motivos:

* Por padrão, o UPN (nome UPN) do Azure Active Directory (AAD) é definido com o mesmo UPN do diretório local.
* Alterar o UPN do Azure Active Directory cria uma correspondência incorreta entre ambientes locais e do Azure que podem causar problemas com determinados aplicativos e serviços.
* Devido a questões de conformidade ou de negócios, a organização não quer usar o UPN local para entrar no Azure.

Para ajudar com a mudança para a autenticação híbrida, agora é possível configurar o Azure Active Directory para permitir que os usuários entrem no Azure com um email como uma ID de logon alternativa em seu domínio verificado. Por exemplo, se *Contoso* for rebatizado como *Fabrikam*, em vez de continuar a entrar com o UPN herdado `balas@contoso.com`, será possível usar o email como uma ID de logon alternativa. Para acessar um aplicativo ou serviços, os usuários entrarão no Azure usando os emails atribuídos a eles, como `balas@fabrikam.com`.

|     |
| --- |
| A entrada no Azure Active Directory com o email como uma ID de logon alternativa é uma versão prévia do recurso do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Visão geral das abordagens de entrada no Azure Active Directory

Os UPNs (nomes UPN) são identificadores exclusivos de uma conta de usuário no diretório local e no Azure Active Directory. Cada conta de usuário em um diretório é representada por um UPN, como `balas@contoso.com`. Por padrão, quando você sincroniza um ambiente de Active Directory Domain Services (AD DS) local com o Azure Active Directory, o UPN do Azure Active Directory é definido para corresponder ao UPN local.

Em muitas organizações, é bem possível definir o UPN local e o UPN do Azure Active Directory para corresponderem. Quando os usuários entram em aplicativos e serviços do Azure, eles usam o UPN do Azure Active Directory. No entanto, algumas organizações não podem usar UPNs correspondentes para entrar devido a problemas com políticas de negócios ou com a experiência do usuário.

As organizações que não podem usar UPNs correspondentes no Azure Active Directory têm algumas opções:

* Uma abordagem é definir o UPN do Azure Active Directory para algo diferente com base nas necessidades de negócios, como `balas@fabrikam.com`.
    * No entanto, nem todos os aplicativos e serviços são compatíveis com o uso de um valor diferente para o UPN local e o UPN do Azure Active Directory.
* Uma abordagem melhor é garantir que os UPNs locais e do Azure Active Directory estejam definidos com o mesmo valor e configurar o Azure Active Directory para permitir que os usuários entrem no Azure com seu email como uma ID de logon alternativa.

Com o email como uma ID de logon alternativa, os usuários ainda podem entrar no Azure inserindo o UPN, mas também podem entrar usando o email. Para permitir isso, defina um endereço de email no atributo *ProxyAddresses* do usuário no diretório local. Este atributo *ProxyAddress* permite um ou mais endereços de email.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Sincronizar endereços de email para entrada no Azure Active Directory

A autenticação tradicional do Active Directory Domain Services (AD DS) ou dos Serviços de Federação do Active Directory (AD FS) ocorre diretamente em sua rede e é tratada pela sua infraestrutura de AD DS. Com a autenticação híbrida, os usuários podem entrar diretamente no Azure Active Directory.

Para permitir essa abordagem de autenticação híbrida, sincronize seu ambiente de AD DS local para o Azure Active Directory usando o [Azure AD Connect][azure-ad-connect] e configure-o para usar a PHS (sincronização de hash de senha) ou a PTA (autenticação de passagem).

Nas duas opções de configuração, o usuário envia seu nome de usuário e senha para o Azure Active Directory, que valida as credenciais e emite um tíquete. Quando os usuários entram no Azure Active Directory, ele acaba com a necessidade de sua organização hospedar e gerenciar uma infraestrutura de AD FS.

![Diagrama da Identidade híbrida do Azure Active Directory com sincronização de hash de senha](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagrama da Identidade híbrida do Azure Active Directory com autenticação de passagem](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Um dos atributos de usuário que é sincronizado automaticamente pelo Azure Active Directory Connect é *ProxyAddresses*. Se os usuários tiverem um endereço de email definido no ambiente de AD DS local como parte do atributo *ProxyAddresses*, ele será sincronizado automaticamente com o Azure Active Directory. Esse endereço de email pode ser usado diretamente no processo de entrada com credenciais do Azure Active Directory como uma ID de logon alternativa.

> [!IMPORTANT]
> Somente os emails nos domínios verificados para o locatário são sincronizados com o Azure Active Directory. Cada locatário do Azure Active Directory tem um ou mais domínios verificados, dos quais você tem propriedade comprovada e estão associados exclusivamente a seu locatário.
>
> Para saber mais, veja [Adicionar e verificar um nome de domínio personalizado no Azure Active Directory][verify-domain].

Para saber mais, veja [Escolher o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory | Microsoft Docs][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Habilitar a entrada do usuário com um endereço de email

Depois que os usuários com o atributo *ProxyAddresses* aplicado forem sincronizados com o Azure Active Directory usando Azure Active Directory Connect, será preciso habilitar o recurso para que os usuários entrem com o email como uma ID de logon alternativa para seu locatário. Esse recurso informa os servidores de logon do Azure Active Directory para não apenas verificar o nome de entrada em relação aos valores UPN, mas também em relação aos valores de *ProxyAddresses* para o endereço de email.

Na versão preliminar, só é possível habilitar a entrada com o email como um recurso de ID de logon alternativo usando o PowerShell. Você precisa de permissões de *administrador de locatários* para concluir as seguintes etapas:

1. Abra uma sessão do PowerShell como administrador e instale o módulo *AzureADPreview* usando o cmdlet [Install-Module][Install-Module]:

    ```powershell
    Install-Module AzureADPreview
    ```

    Se solicitado, escolha **Y** para instalar o NuGet ou instalar a partir de um repositório não confiável.

1. Entre no seu locatário do Azure Active Directory como um *administrador de locatários* usando o cmdlet do [Connect-AzureAD][Connect-AzureAD]:

    ```powershell
    Connect-AzureAD
    ```

    O comando retorna informações sobre sua conta, seu ambiente e sua ID de locatário.

1. Verifique se a política *HomeRealmDiscoveryPolicy* já existe em seu locatário usando o cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] da seguinte maneira:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Se não houver políticas configuradas no momento, o comando não retornará nada. Se uma política for retornada, ignore essa etapa e vá para a próxima etapa para atualizar uma política atual.

    Para adicionar a política *HomeRealmDiscoveryPolicy* ao locatário, use o cmdlet [New-AzureADPolicy][New-AzureADPolicy] e defina o atributo *AlternateIdLogin* como *"Enabled": true*, conforme mostrado no exemplo a seguir:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Quando a política tiver sido criada com êxito, o comando retornará a ID da política, conforme mostrado na seguinte saída de exemplo:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Se já houver uma política configurada, verifique se o atributo  *AlternateIdLogin*  está habilitado, conforme mostrado no seguinte exemplo de saída de política:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Se a política existir, mas o atributo *AlternateIdLogin* não estiver presente ou habilitado, ou se outros atributos existirem na política que você quer preservar, atualize a política existente usando o cmdlet [Set-AzureADPolicy][Set-AzureADPolicy].

    > [!IMPORTANT]
    > Ao atualizar a política, inclua as configurações antigas e o novo atributo  *AlternateIdLogin*.

    O exemplo a seguir adiciona o atributo  *AlternateIdLogin* e preserva o atributo  *AllowCloudPasswordValidation*, que pode já ter sido definido:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Confirme se a política atualizada mostra as alterações e se o atributo *AlternateIdLogin* agora está habilitado:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>Testar entrada do usuário com email

Para testar se os usuários conseguem entrar com o email, navegue até [https://myprofile.microsoft.com][my-profile] e entre com uma conta de usuário a partir do endereço de email, como `balas@fabrikam.com`, não a partir do UPN, como `balas@contoso.com`. A experiência de entrada deve ser parecida com a de um evento de entrada baseado em UPN.

## <a name="troubleshoot"></a>Solucionar problemas

Se os usuários tiverem problemas com eventos de entrada usando o endereço de email, examine as seguintes etapas de solução de problemas:

1. Verifique se a conta de usuário tem seu endereço de email definido para o atributo *ProxyAddresses* no ambiente do AD DS local.
1. Verifique se o Azure Active Directory Connect está configurado e sincroniza com êxito as contas de usuário do ambiente de AD DS local no Azure Active Directory.
1. Confirme se a política *HomeRealmDiscoveryPolicy* do Azure Active Directory tem o atributo *AlternateIdLogin* definido como *"Enabled": true*:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a identidade híbrida, como o Proxy do Aplicativo Azure AD ou o Azure Active Directory Domain Services, veja [Identidade híbrida do Azure Active Directory para acesso e gerenciamento de cargas de trabalho locais][hybrid-overview].

Para obter mais informações sobre operações de identidade híbrida, veja [Como a sincronização de hash de senha][phs-overview] ou a [autenticação de passagem][pta-overview] funcionam.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
