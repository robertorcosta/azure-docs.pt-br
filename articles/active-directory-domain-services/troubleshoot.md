---
title: Solução de problemas do Azure Active Directory Domain Services | Microsoft Docs
description: Saiba como solucionar erros comuns ao criar ou gerenciar serviços de domínio do Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e17112cbe2a494a585cd5a09c36cfe449d3d433c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365808"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Etapas comuns de solução de problemas para serviços de domínio do diretório ativo do Azure

Como parte central da identidade e autenticação para aplicativos, o Azure Active Directory Domain Services (Azure AD DS) às vezes tem problemas. Se você encontrar problemas, existem algumas mensagens de erro comuns e etapas associadas de solução de problemas para ajudá-lo a fazer as coisas funcionarem novamente. A qualquer momento, você também pode [abrir uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

Este artigo fornece etapas de solução de problemas para problemas comuns no Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível habilitar o Azure AD Domain Services para seu diretório do Azure AD

Se você tiver problemas para habilitar o Azure AD DS, revise os seguintes erros e etapas comuns para resolvê-los:

| **Mensagem de erro de amostra** | **Resolução** |
| --- |:--- |
| *O nome aaddscontoso.com já está em uso nesta rede. Especifique um nome que não esteja em uso.* |[Conflito de nome de domínio na rede virtual](troubleshoot.md#domain-name-conflict) |
| *Os Serviços de Domínio não puderam ser habilitados neste inquilino Azure AD. O serviço não possui permissões adequadas para o aplicativo chamado 'Azure AD Domain Services Sync'. Exclua o aplicativo chamado 'Azure AD Domain Services Sync' e tente ativar os Serviços de Domínio para o seu inquilino Azure AD.* |[O Domain Services não tem permissões adequadas para o aplicativo Azure AD Domain Services Sync](troubleshoot.md#inadequate-permissions) |
| *Os Serviços de Domínio não puderam ser habilitados neste inquilino Azure AD. O aplicativo Serviços de Domínio em seu inquilino Azure AD não tem as permissões necessárias para habilitar serviços de domínio. Exclua o aplicativo com o identificador de aplicativo d87dcbc6-a371-462e-88e3-28ad15ec4e64 e, em seguida, tente ativar serviços de domínio para o seu inquilino Azure AD.* |[O aplicativo Serviços de Domínio não está configurado corretamente no seu inquilino Azure AD](troubleshoot.md#invalid-configuration) |
| *Os Serviços de Domínio não puderam ser habilitados neste inquilino Azure AD. O aplicativo Microsoft Azure AD está desativado no seu inquilino Azure AD. Habilite o aplicativo com o identificador de aplicativo 0000002-0000-0000-0000000000000eee, em seguida, tente habilitar os Serviços de Domínio para o seu inquilino Azure AD.* |[O aplicativo Microsoft Graph está desabilitado no seu locatário do Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflito de Nome de Domínio

**Mensagem de erro**

*O nome aaddscontoso.com já está em uso nesta rede. Especifique um nome que não esteja em uso.*

**Resolução**

Verifique se você não tem um ambiente AD DS existente com o mesmo nome de domínio na mesma ou uma rede virtual peered. Por exemplo, você pode ter um domínio AD DS chamado *aaddscontoso.com* que é executado em VMs do Azure. Quando você tenta ativar um domínio gerenciado pelo Azure AD DS com o mesmo nome de domínio de *aaddscontoso.com* na rede virtual, a operação solicitada falha.

Essa falha deve-se a conflitos de nome para o nome de domínio na rede virtual. Uma pesquisa de DNS verifica se um ambiente AD DS existente responde sobre o nome de domínio solicitado. Para resolver essa falha, use um nome diferente para configurar o domínio gerenciado do Azure AD DS ou desprovisionar o domínio AD DS existente e, em seguida, tentar novamente habilitar o Azure AD DS.

### <a name="inadequate-permissions"></a>Permissões inadequadas

**Mensagem de erro**

*Os Serviços de Domínio não puderam ser habilitados neste inquilino Azure AD. O serviço não possui permissões adequadas para o aplicativo chamado 'Azure AD Domain Services Sync'. Exclua o aplicativo chamado 'Azure AD Domain Services Sync' e tente ativar os Serviços de Domínio para o seu inquilino Azure AD.*

**Resolução**

Verifique se há um aplicativo chamado *Azure AD Domain Services Sync* em seu diretório Azure AD. Se esse aplicativo existir, exclua-o e tente novamente ativar o Azure AD DS. Para verificar se há um aplicativo existente e excluí-lo se necessário, complete as seguintes etapas:

1. No portal Azure, selecione **O Diretório Ativo do Azure** no menu de navegação à esquerda.
1. Selecione **Aplicativos empresariais**. Escolha *Todos os aplicativos* do menu suspenso tipo de **aplicativo** e, em seguida, **selecione Aplicar**.
1. Na caixa de pesquisa, *insira a sincronização de serviços de domínio Ad do Azure.* Se o aplicativo existir, selecione-o e escolha **Excluir**.
1. Depois de excluir o aplicativo, tente ativar o Azure AD DS novamente.

### <a name="invalid-configuration"></a>Configuração inválida

**Mensagem de erro**

*Os Serviços de Domínio não puderam ser habilitados neste inquilino Azure AD. O aplicativo Serviços de Domínio em seu inquilino Azure AD não tem as permissões necessárias para habilitar serviços de domínio. Exclua o aplicativo com o identificador de aplicativo d87dcbc6-a371-462e-88e3-28ad15ec4e64 e, em seguida, tente ativar serviços de domínio para o seu inquilino Azure AD.*

**Resolução**

Verifique se você tem um aplicativo existente chamado *AzureActiveDirectoryDomainControllerServices* com um identificador de aplicativo *d87dcbc6-a371-462e-88e3-28ad15ec4e64* em seu diretório Azure AD. Se esse aplicativo existir, exclua-o e tente novamente ativar o Azure AD DS.

Use o seguinte script PowerShell para procurar uma instância de aplicativo existente e excluí-la se necessário:

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desabilitado

**Mensagem de erro**

*Os Serviços de Domínio não puderam ser habilitados neste inquilino Azure AD. O aplicativo Microsoft Azure AD está desativado no seu inquilino Azure AD. Habilite o aplicativo com o identificador de aplicativo 0000002-0000-0000-0000000000000eee, em seguida, tente habilitar os Serviços de Domínio para o seu inquilino Azure AD.*

**Resolução**

Verifique se você desabilitou um aplicativo com o identificador *00000002-0000-0000-c000-0000000000000000000*. Esse aplicativo é o aplicativo Microsoft Azure AD e fornece acesso à API do Graph para seu locatário do Azure AD. Para sincronizar o inquilino azure AD, este aplicativo deve ser habilitado.

Para verificar o status deste aplicativo e ahabilitá-lo, se necessário, complete as seguintes etapas:

1. No portal Azure, selecione **O Diretório Ativo do Azure** no menu de navegação à esquerda.
1. Selecione **Aplicativos empresariais**. Escolha *Todos os aplicativos* do menu suspenso tipo de **aplicativo** e, em seguida, **selecione Aplicar**.
1. Na caixa de pesquisa, digite *00000002-0000-0000-c0000-0000000000000.* Selecione o aplicativo e escolha **Propriedades**.
1. Se **habilitado para os usuários fazerlogin** estiver definido como *Não,* defina o valor como *Sim*e **selecione Salvar**.
1. Depois de habilitar o aplicativo, tente ativar o Azure AD DS novamente.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os usuários não conseguem entrar no domínio gerenciado pelos Serviços de Domínio do AD do Azure

Se um ou mais usuários no seu inquilino Azure AD não puderem entrar no domínio gerenciado pelo Azure AD DS, complete as seguintes etapas de solução de problemas:

* **Formato de credenciais** - Tente usar o `dee@aaddscontoso.onmicrosoft.com`formato UPN para especificar credenciais, tais como . O formato UPN é a maneira recomendada de especificar credenciais no Azure AD DS. Certifique-se de que este UPN está configurado corretamente no Azure AD.

    O *SAMAccountName* para sua conta, como *AADDSCONTOSO\driley* pode ser gerado automaticamente se houver vários usuários com o mesmo prefixo UPN em seu inquilino ou se o seu prefixo UPN for excessivamente longo. Portanto, o formato *SAMAccountName* para sua conta pode ser diferente do que você espera ou usa em seu domínio local.

* **Sincronização de senhas** - [Certifique-se][cloud-only-passwords] de que você habilitou a sincronização de senha para usuários somente na nuvem ou para [ambientes híbridos usando o Azure AD Connect][hybrid-phs].
    * **Contas sincronizadas híbridas:** Se as contas de usuário afetadas estiverem sincronizadas a partir de um diretório local, verifique as seguintes áreas:
    
      * Você implantou ou atualizou para a [versão mais recente recomendada do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
      * Você configurou o Azure AD Connect para [executar uma sincronização completa][hybrid-phs].
      * Dependendo do tamanho do seu diretório, pode levar um tempo para que contas de usuário e hashes credenciais estejam disponíveis no Azure AD DS. Certifique-se de esperar o suficiente antes de tentar autenticar contra o domínio gerenciado.
      * Se o problema persistir após verificar as etapas anteriores, tente reiniciar o *Serviço de Sincronização AD do Microsoft Azure*. A partir do servidor Azure AD Connect, abra um prompt de comando e execute os seguintes comandos:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Contas somente na nuvem**: Se a conta de usuário afetada for uma conta de usuário somente na nuvem, certifique-se de que o [usuário alterou sua senha depois que você habilitou o Azure AD DS][cloud-only-passwords]. Essa redefinição de senha faz com que os hashes de credenciais necessários para os Serviços de Domínio Ad do Azure sejam gerados.

* **Verifique se a conta de usuário está ativa**: Por padrão, cinco tentativas de senha inválidas dentro de 2 minutos no domínio gerenciado fazem com que uma conta de usuário seja bloqueada por 30 minutos. O usuário não pode fazer login enquanto a conta estiver bloqueada. Após 30 minutos, a conta de usuário é desbloqueada automaticamente.
  * Tentativas de senha inválidas no domínio gerenciado pelo Azure AD DS não bloqueiam a conta de usuário no Azure AD. A conta de usuário é bloqueada apenas dentro do domínio gerenciado. Verifique o status da conta de usuário no *Active Directory Administrative Console (ADAC)* usando a [VM de gerenciamento,][management-vm]não no Azure AD.
  * Você também pode [configurar políticas de senha fina sacada][password-policy] para alterar o limite e a duração de bloqueio padrão.

* **Contas externas** - Verifique se a conta de usuário afetada não é uma conta externa no inquilino Azure AD. Exemplos de contas externas incluem `dee@live.com` contas da Microsoft como ou contas de usuário de um diretório AD externo do Azure. O Azure AD DS não armazena credenciais para contas de usuários externos, portanto, eles não podem fazer login no domínio gerenciado.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Há um ou mais alertas no seu domínio gerenciado

Se houver alertas ativos no domínio gerenciado pelo Azure AD DS, ele poderá impedir que o processo de autenticação funcione corretamente.

Para ver se há algum alerta ativo, [verifique o estado de saúde de um domínio gerenciado pelo Azure AD DS][check-health]. Se algum alerta for mostrado, [soque e resolva-os][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os usuários removidos do seu locatário do Azure AD não são removidos do seu domínio gerenciado

O Azure AD protege contra a exclusão acidental de objetos do usuário. Quando você exclui uma conta de usuário de um inquilino Azure AD, o objeto de usuário correspondente é movido para a lixeira. Quando esta operação de exclusão é sincronizada com o domínio gerenciado pelo Azure AD DS, a conta de usuário correspondente é marcada como desativada. Esse recurso ajuda você a recuperar ou desexcluir a conta de usuário.

A conta de usuário permanece no estado desativado no domínio gerenciado pelo Azure AD DS, mesmo se você recriar uma conta de usuário com a mesma UPN no diretório Azure AD. Para remover a conta de usuário do domínio gerenciado pelo Azure AD DS, você precisa excluí-la à força do inquilino azure AD.

Para remover totalmente uma conta de usuário de um domínio gerenciado pelo Azure AD DS, exclua o usuário permanentemente `-RemoveFromRecycleBin` do seu inquilino Azure AD usando o cmdlet [Remove-MsolUser][Remove-MsolUser] PowerShell com o parâmetro.

## <a name="next-steps"></a>Próximas etapas

Se você continuar a ter problemas, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
