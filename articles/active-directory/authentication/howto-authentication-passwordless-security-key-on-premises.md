---
title: Login da chave de segurança sem senha para recursos no local (visualização) - Azure Active Directory
description: Saiba como ativar o login de chave de segurança sem senha para recursos no local usando o Azure Active Directory (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b29f84931c169ffe1c2c81d5e32201cbc63fc88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942869"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Habilite o login da chave de segurança sem senha para os recursos no local com o Azure Active Director (visualização)

Este documento se concentra em permitir a autenticação sem senha aos recursos locais para ambientes com a **adesão do Azure AD** e **a ad híbrida do Azure AD juntou-se aos** dispositivos Windows 10. Essa funcionalidade fornece sso de acessação única perfeita para recursos no local usando chaves de segurança compatíveis com a Microsoft.

|     |
| --- |
| As chaves de segurança FIDO2 são um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO para recursos locais usando chaves FIDO2

O Azure Active Directory (AD) pode emitir Tickets (TGTs) de concessão de ingressos kerberos para um ou mais de seus domínios do Active Directory. Essa funcionalidade permite que os usuários entrem no Windows com credenciais modernas, como chaves de segurança FIDO2 e acessem recursos tradicionais baseados no Active Directory. Os tickets de serviço kerberos e a autorização continuam a ser controlados por seus controladores de domínio do Active Directory no local.

Um objeto Azure AD Kerberos Server é criado em seu Diretório Ativo no local e, em seguida, publicado com segurança no Azure Active Directory. O objeto não está associado a nenhum servidor físico. É simplesmente um recurso que pode ser usado pelo Azure Active Directory para gerar TGTs Kerberos para o seu Domínio de Diretório Ativo.

![Obtendo um bilhete de concessão de ingressos (TGT) do Azure AD e AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. O usuário faz a assinatura do dispositivo Windows 10 com uma chave de segurança FIDO2 e autentica-se no Azure AD.
1. O Azure AD verifica o diretório de uma chave de servidor Kerberos que corresponde ao domínio AD no local do usuário.
   1. O Azure AD gera um Kerberos TGT para o domínio AD no local do usuário. O TGT só inclui o SID do usuário. Nenhum dado de autorização está incluído no TGT.
1. O TGT é devolvido ao cliente juntamente com seu Azure AD Primary Refresh Token (PRT).
1. A máquina cliente entra em contato com um controlador de domínio AD no local e negocia o TGT parcial por um TGT totalmente formado.
1. A máquina cliente agora tem um Azure AD PRT e um TGT de diretório ativo completo e pode acessar recursos em nuvem e locais.

## <a name="requirements"></a>Requisitos

As organizações devem concluir as etapas para habilitar o [sinal de chave de segurança sem senha para dispositivos Windows 10 (visualização)](howto-authentication-passwordless-security-key.md) antes de completar as etapas deste artigo.

As organizações também devem atender aos seguintes requisitos de software.

- Os dispositivos devem estar executando o Windows 10 Insider Build 18945 ou mais novo.
- Você deve ter a versão 1.4.32.0 ou posterior do [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Para obter mais informações sobre as opções de autenticação híbrida Azure AD disponíveis, consulte [Escolha o método de autenticação certo para a solução de identidade híbrida do Azure Active Directory](../../security/fundamentals/choose-ad-authn.md) e [Selecione qual tipo de instalação usar no Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Os controladores de domínio do Windows Server devem ter os seguintes patches instalados:
    - Para windows server 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Para windows server 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Cenários com suporte

O cenário suporta a inscrição única (SSO) em ambos os cenários a seguir:

- Para recursos em nuvem como o Office 365 e outros aplicativos habilitados para SAML.
- Para recursos locais e autenticação integrada do Windows em sites da Web. Os recursos podem incluir sites da Web e sites do SharePoint que requerem autenticação iis e/ou recursos que usam autenticação NTLM.

### <a name="unsupported-scenarios"></a>Cenários sem suporte

Os seguintes cenários não são suportados:

- O domínio AD DS (Windows Server Active Directory Domain Services, serviços de domínio do diretório ativo do Windows Server) juntou-se à implantação (somente dispositivos no local).
- Cenários RDP, VDI e Citrix usando uma chave de segurança.
- S/MIME usando uma chave de segurança.
- "Corra como" usando uma chave de segurança.
- Faça login em um servidor usando a chave de segurança.

## <a name="create-kerberos-server-object"></a>Criar objeto de servidor Kerberos

Os administradores usam as ferramentas PowerShell do servidor Azure AD Connect para criar um objeto Azure AD Kerberos Server em seu diretório local. Execute as seguintes etapas em cada domínio e floresta em sua organização que contenham usuários ad do Azure:

1. Atualize para a versão mais recente do Azure AD Connect. As instruções supõem que você já configurou o Azure AD Connect para suportar seu ambiente híbrido.
1. No Azure AD Connect Server, abra um prompt PowerShell elevado e navegue para`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Execute os seguintes comandos powershell para criar um novo objeto de servidor Azure AD Kerberos no domínio do Active Directory no local e no inquilino do Azure Active Directory.

> [!NOTE]
> Substitua `contoso.corp.com` no exemplo a seguir o nome de domínio do Active Directory no local.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Visualização e verificação do Servidor Azure AD Kerberos

Você pode visualizar e verificar o recém-criado Azure AD Kerberos Server usando o seguinte comando:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Este comando produz as propriedades do Azure AD Kerberos Server. Você pode rever as propriedades para verificar se tudo está em ordem.

| Propriedade | Descrição |
| --- | --- |
| ID | O ID único do objeto AD DS DC. Este ID é às vezes referido como "slot" ou é "ID de ramo". |
| DomainDnsName | O nome de domínio DNS do domínio active directory. |
| ComputerAccount | O objeto de conta do computador do objeto Azure AD Kerberos Server (o DC). |
| UserAccount | O objeto de conta de usuário desativado que contém a chave de criptografia Ad Kerberos Server TGT do Azure AD. O DN desta conta é`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Versão chave | A versão-chave da chave de criptografia Azure AD Kerberos Server TGT. A versão é atribuída quando a chave é criada. A versão é então incrementada cada vez que a chave é girada. Os incrementos são baseados em meta-dados de replicação e provavelmente maiores que um. Por exemplo, o *KeyVersion* inicial poderia ser *192272*. Na primeira vez que a tecla é girada, a versão pode avançar para *212621*. O importante a verificar é que o *KeyVersion* para o objeto no local e o *CloudKeyVersion* para o objeto de nuvem são os mesmos. |
| KeyUpdatedOn | A data e a hora em que a chave de criptografia Azure AD Kerberos Server TGT foi atualizada ou criada. |
| Chaveatualizada | O DC onde a chave de criptografia Azure AD Kerberos Server TGT foi atualizada pela última vez. |
| CloudId | O ID do Objeto Azure AD. Deve coincidir com o ID acima. |
| CloudDomainDnsNome | O *DomainDnsName* do Objeto Ad do Azure. Deve corresponder ao *DomainDnsName* acima. |
| CloudKeyVersion | O *KeyVersion* do Objeto AD do Azure. Deve coincidir com o *KeyVersion* acima. |
| Cloudkeyupdatedon | A *chaveUpdatedOn* do objeto Azure AD. Deve coincidir com a *chaveUpdatedOn* acima. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Girando a tecla Azure AD Kerberos Server

As chaves krbtgt de criptografia Do Azure AD Kerberos Server devem ser giradas regularmente. Recomenda-se que você siga o mesmo cronograma que você usa para girar todas as outras chaves krbtgt do Active Directory Domain Controller.

> [!WARNING]
> Existem outras ferramentas que podem girar as chaves krbtgt, no entanto, você deve usar as ferramentas mencionadas neste documento para girar as chaves krbtgt do seu Azure AD Kerberos Server. Isso garante que as chaves sejam atualizadas tanto no local quanto no Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Removendo o Servidor Azure AD Kerberos

Se você quiser reverter o cenário e remover o Azure AD Kerberos Server do Active Directory active no local e do Azure Active Directory, execute o seguinte comando:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Cenários multiflorestais e multidomínios

O objeto de servidor Azure AD Kerberos é representado no Azure AD como um objeto *KerberosDomain.* Cada domínio do Active Directory no local é representado como um único objeto *KerberosDomain* no Azure AD.

Por exemplo, sua organização tem uma floresta de `contoso.com` `fabrikam.com`diretórioativo ativo com dois domínios, e . Se você optar por permitir que o Azure AD emita TGTs Kerberos para toda a floresta, existem dois objetos *KerberosDomain* no Azure AD. Um objeto *KerberosDomain* para `contoso.com` `fabrikam.com`, e um para . Se você tem várias florestas de Diretório Ativo, há um objeto *KerberosDomain* para cada domínio em cada floresta.

Você precisa executar as etapas para [criar o objeto de servidor Kerberos](#create-kerberos-server-object) em cada domínio e floresta em sua organização que contenham usuários ad do Azure.

## <a name="known-behavior"></a>Comportamento conhecido

O login com o FIDO é bloqueado se sua senha tiver expirado. A expectativa é que o usuário refina sua senha antes de poder fazer login usando FIDO.

## <a name="troubleshooting-and-feedback"></a>Solução de problemas e feedback

Se você quiser compartilhar feedback ou encontrar problemas durante a visualização deste recurso, compartilhe através do aplicativo Windows Feedback Hub usando as seguintes etapas:

1. Inicie **o Feedback Hub** e certifique-se de que você está conectado.
1. Enviar feedback a seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar logs, use a opção para **recriar meu problema**

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-this-work-in-my-on-premises-environment"></a>Isso funciona no meu ambiente local?

Esse recurso não funciona para um ambiente ad DS (Active Directory Domain Services, serviço de domínio de diretório ativo) no local.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Minha organização requer autenticação de dois fatores para acessar recursos. O que posso fazer para apoiar essa exigência?

As chaves de segurança vêm em uma variedade de fatores de forma. Entre em contato com o fabricante do dispositivo de interesse para discutir como seus dispositivos podem ser habilitados com um PIN ou biométrico como um segundo fator.

### <a name="can-admins-set-up-security-keys"></a>Os admins podem configurar as chaves de segurança?

Estamos trabalhando neste recurso para disponibilidade geral (GA) deste recurso.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Onde posso ir para encontrar chaves de segurança compatíveis?

[Chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>O que faço se perder minha chave de segurança?

Você pode remover chaves do portal Azure navegando até a página **de informações de segurança** e removendo a chave de segurança.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Eu não sou capaz de usar FIDO imediatamente depois de criar uma máquina adesão a azure AD híbrido

Se estiver limpa instalando uma máquina aderida a um Azure AD híbrido, após o processo de adesão e reinicialização do domínio, você deve fazer login com uma senha e esperar que a política seja sincronizada antes de poder usar o FIDO para fazer login.

- Verifique seu status `dsregcmd /status` atual digitando em uma janela de comando e verifique se tanto *azureAdJoined* quanto *o DomainJoined* estão mostrando *SIM*.
- Esse atraso é uma limitação conhecida para dispositivos unidos por domínio e não é específico do FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Não consigo obter SSO para o meu recurso de rede NTLM depois de assinar com a FIDO e obter um prompt de credencial

Certifique-se de que os controladores de domínio sejam corrigidos para responder a tempo de atender à sua solicitação de recursos. Para verificar se você pode ver um controlador de domínio `nltest /dsgetdc:contoso /keylist /kdc`que está executando o recurso, revise a saída de .

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre sem senha](concept-authentication-passwordless.md)
