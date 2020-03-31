---
title: Federação direta com um provedor de identidade para B2B - Azure AD
description: Federação direta com um provedor de identidade SAML ou WS-Fed para que os hóspedes possam entrar em seus aplicativos AD do Azure
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050891"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federação direta com AD FS e provedores de terceiros para usuários convidados (visualização)
|     |
| --- |
| Federação direta é um recurso de pré-visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como criar uma federação direta com outra organização para a colaboração B2B. Você pode configurar uma federação direta com qualquer organização cujo provedor de identidade (IdP) suporte o protocolo SAML 2.0 ou WS-Fed.
Quando você configura uma federação direta com o IdP de um parceiro, novos usuários convidados desse domínio podem usar sua própria conta organizacional gerenciada por IdP para entrar no seu inquilino Azure AD e começar a colaborar com você. Não há necessidade de o usuário convidado criar uma conta AD azure separada.
> [!NOTE]
> Os usuários convidados da federação direta devem fazer login `https://myapps.microsoft.com/?tenantid=<tenant id>` usando `https://portal.azure.com/<tenant id>`um link que inclua o `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`contexto do inquilino (por exemplo, ou , ou no caso de um domínio verificado, ). Links diretos para aplicativos e recursos também funcionam desde que incluam o contexto do locatário. Os usuários diretos da federação atualmente não podem fazer login usando pontos finais comuns que não têm contexto de inquilino. Por exemplo, `https://myapps.microsoft.com` `https://portal.azure.com`usar `https://teams.microsoft.com` , ou resultará em um erro.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando um usuário convidado é autenticado com federação direta?
Depois de configurar uma federação direta com uma organização, todos os novos usuários convidados que você convidar serão autenticados usando federação direta. É importante notar que a criação da federação direta não altera o método de autenticação para usuários convidados que já resgataram um convite seu. Estes são alguns exemplos:
 - Se os usuários convidados já resgatarem convites de você, e você posteriormente configurar a federação direta com sua organização, esses usuários convidados continuarão a usar o mesmo método de autenticação que usaram antes de você configurar a federação direta.
 - Se você configurar uma federação direta com uma organização parceira e convidar os usuários convidados, e então a organização parceira se mudar para o Azure AD, os usuários convidados que já resgataram convites continuarão a usar a federação direta, desde que o direto política de federação em seu inquilino existe.
 - Se você excluir a federação direta com uma organização parceira, todos os usuários convidados que atualmente usam federação direta não poderão fazer login.

Em qualquer um desses cenários, você pode atualizar o método de autenticação de um usuário convidado excluindo a conta de usuário convidado do seu diretório e reconvidando-as.

A federação direta está vinculada a espaços de nomes de domínio, como contoso.com e fabrikam.com. Ao estabelecer uma configuração direta de federação com OD FS ou um IdP de terceiros, as organizações associam um ou mais espaços de nome de domínio a esses IdPs. 

## <a name="end-user-experience"></a>Experiência do usuário final 
Com a federação direta, os usuários convidados entram no seu inquilino Azure AD usando sua própria conta organizacional. Quando eles estão acessando recursos compartilhados e são solicitados para o login, os usuários diretos da federação são redirecionados para seu IdP. Após o login bem-sucedido, eles são devolvidos ao Azure AD para acessar recursos. Os tokens de atualização dos usuários da federação direta são válidos por 12 horas, o [comprimento padrão para token de atualização de passagem](../develop/active-directory-configurable-token-lifetimes.md#exceptions) no Azure AD. Se o IdP federado tiver o SSO ativado, o usuário experimentará o SSO e não verá nenhum prompt de login após a autenticação inicial.

## <a name="limitations"></a>Limitações

### <a name="dns-verified-domains-in-azure-ad"></a>Domínios verificados por DNS no Azure AD
O domínio com o que você deseja federar ***não*** deve ser verificado pelo DNS no Azure AD. Você está autorizado a configurar uma federação direta com inquilinos AD não gerenciados (verificados por e-mail ou "virais") porque eles não são verificados pelo DNS.

### <a name="authentication-url"></a>URL de autenticação
A federação direta só é permitida para políticas em que o domínio da URL de autenticação corresponda ao domínio de destino ou quando a URL de autenticação é um desses provedores de identidade permitidos (esta lista está sujeita a alterações):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Por exemplo, ao configurar a federação direta `https://fabrikam.com/adfs` para **fabrikam.com,** a URL de autenticação passará pela validação. Um host no mesmo domínio também `https://sts.fabrikam.com/adfs`passará, por exemplo. No entanto, `https://fabrikamconglomerate.com/adfs` a `https://fabrikam.com.uk/adfs` URL de autenticação ou para o mesmo domínio não passará.

### <a name="signing-certificate-renewal"></a>Renovação do certificado de assinatura
Se você especificar a URL de metadados nas configurações do provedor de identidade, o Azure AD renovará automaticamente o certificado de assinatura quando expirar. No entanto, se o certificado for girado por qualquer motivo antes do tempo de expiração ou se você não fornecer uma URL de metadados, o Azure AD não poderá renová-lo. Neste caso, você precisará atualizar o certificado de assinatura manualmente.

### <a name="limit-on-federation-relationships"></a>Limite nas relações da federação
Atualmente, um máximo de 1.000 relações de federação é apoiado. Esse limite inclui [federações internas](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) e federações diretas.

### <a name="limit-on-multiple-domains"></a>Limite em vários domínios
Atualmente, não apoiamos a federação direta com vários domínios do mesmo inquilino.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Posso configurar uma federação direta com um domínio para o qual existe um inquilino não gerenciado (verificado por e-mail) ? 
Sim. Se o domínio não tiver sido verificado e o inquilino não tiver sofrido uma [aquisição de um admin,](../users-groups-roles/domains-admin-takeover.md)você pode configurar uma federação direta com esse domínio. Os inquilinos não gerenciados ou verificados por e-mail são criados quando um usuário resgata um convite B2B ou realiza uma inscrição de autoatendimento para o Azure AD usando um domínio que não existe atualmente. Você pode configurar uma federação direta com esses domínios. Se você tentar configurar uma federação direta com um domínio verificado pelo DNS, seja no portal do Azure ou via PowerShell, você verá um erro.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se a autenticação direta da federação e do e-mail for em tempo único, qual método tem precedência?
Quando a federação direta é estabelecida com uma organização parceira, ela tem precedência sobre a autenticação de senha única por e-mail para novos usuários convidados dessa organização. Se um usuário convidado resgatar um convite usando autenticação de senha única antes de configurar a federação direta, ele continuará a usar autenticação de senha única. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>A federação direta aborda problemas de login devido a uma locação parcialmente sincronizada?
Não, o recurso [de senha única de e-mail](one-time-passcode.md) deve ser usado neste cenário. Uma "locação parcialmente sincronizada" refere-se a um inquilino Azure AD parceiro onde as identidades dos usuários no local não são totalmente sincronizadas com a nuvem. Um convidado cuja identidade ainda não existe na nuvem, mas que tenta resgatar seu convite B2B não poderá fazer login. O recurso de senha única permitiria que este convidado assinasse. O recurso da federação direta aborda cenários onde o hóspede tem sua própria conta organizacional gerenciada pelo IdP, mas a organização não tem nenhuma presença no Azure AD.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Passo 1: Configurar o provedor de identidade da organização parceira
Primeiro, sua organização parceira precisa configurar seu provedor de identidade com as reivindicações necessárias e confiando nos fundos partidários. 

> [!NOTE]
> Para ilustrar como configurar um provedor de identidade para federação direta, usaremos o Active Directory Federation Services (AD FS) como exemplo. Veja o artigo [Configure federação direta com AD FS](direct-federation-adfs.md), que dá exemplos de como configurar o AD FS como um provedor de identidade SAML 2.0 ou WS-Fed em preparação para a federação direta.

### <a name="saml-20-configuration"></a>Configuração do SAML 2.0

O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo SAML com requisitos específicos listados abaixo. Para obter mais informações sobre como configurar uma confiança entre seu provedor de identidade SAML e o Azure AD, consulte [Usar um Provedor de Identidade SAML 2.0 (IdP) para o Single Sign-On](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> O domínio-alvo para federação direta não deve ser verificado pelo DNS no Azure AD. O domínio url de autenticação deve corresponder ao domínio de destino ou deve ser o domínio de um provedor de identidade permitido. Consulte a seção [Limitações](#limitations) para obter detalhes. 

#### <a name="required-saml-20-attributes-and-claims"></a>Atributos e reclamações saml 2.0 necessários
As tabelas a seguir mostram requisitos para atributos e reclamações específicos que devem ser configurados no provedor de identidade de terceiros. Para configurar a federação direta, os seguintes atributos devem ser recebidos na resposta SAML 2.0 do provedor de identidade. Esses atributos podem ser configurados vinculando-se ao arquivo XML do serviço de token de segurança on-line ou inserindo-os manualmente.

Atributos necessários para a resposta SAML 2.0 do IdP:

|Atributo  |Valor  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Público     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O uri emissor do IdP parceiro, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |


Reivindicações necessárias para o token SAML 2.0 emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configuração ws-fed 
O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo WS-Fed com alguns requisitos específicos conforme listado abaixo. Atualmente, os dois provedores ws-fed foram testados para compatibilidade com a Azure AD incluem AD FS e Shibboleth. Para obter mais informações sobre como estabelecer uma confiança de parte confiável entre um provedor compatível com o WS-Fed com o Azure AD, consulte o "Papel de Integração do STS usando protocolos WS" disponível no [Azure AD Identity Provider Compatibility Docs](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> O domínio-alvo para federação direta não deve ser verificado pelo DNS no Azure AD. O domínio url de autenticação deve corresponder ao domínio de destino ou ao domínio de um provedor de identidade permitido. Consulte a seção [Limitações](#limitations) para obter detalhes. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Atributos e reivindicações do WS-Fed necessários

As tabelas a seguir mostram requisitos para atributos e reivindicações específicos que devem ser configurados no provedor de identidade WS-Fed de terceiros. Para configurar a federação direta, os seguintes atributos devem ser recebidos na mensagem WS-Fed do provedor de identidade. Esses atributos podem ser configurados vinculando-se ao arquivo XML do serviço de token de segurança on-line ou inserindo-os manualmente.

Atributos necessários na mensagem WS-Fed do IdP:
 
|Atributo  |Valor  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Público     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O uri emissor do IdP parceiro, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |

Reivindicações necessárias para o token WS-Fed emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|ImutávelID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Passo 2: Configure federação direta no Azure AD 
Em seguida, você configurará a federação com o provedor de identidade configurado na etapa 1 do Azure AD. Você pode usar o portal Azure AD ou powerShell. Pode levar de 5 a 10 minutos até que a política direta da federação entre em vigor. Durante este tempo, não tente resgatar um convite para o domínio direto da federação. Os atributos a seguir são obrigatórios:
- Uri emissor do IdP parceiro
- Ponto final de autenticação passiva do IdP do parceiro (apenas https é suportado)
- Certificado

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Para configurar federação direta no portal Azure AD

1. Vá para o [portal Azure.](https://portal.azure.com/) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Relações organizacionais**.
3. Selecione **provedores de identidade**e selecione **Novo IdP alimentado por SAML/WS**.

    ![Captura de tela mostrando botão para adicionar um novo IDP SAML ou WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Na página **IdP Do Novo SAML/WS-Fed,** no **protocolo do provedor de identidade,** selecione **SAML** ou **WS-FED**.

    ![Captura de tela mostrando o botão de análise na página SAML ou WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Digite o nome de domínio da organização parceira, que será o nome de domínio alvo da federação direta
6. Você pode carregar um arquivo de metadados para preencher detalhes de metadados. Se você optar por inserir metadados manualmente, digite as seguintes informações:
   - Nome de domínio do IdP do parceiro
   - ID da entidade do IdP do parceiro
   - Solicitação passiva ponto final do IdP do parceiro
   - Certificado
   > [!NOTE]
   > A URL de metadados é opcional, porém recomendamos fortemente. Se você fornecer a URL de metadados, o Azure AD poderá renovar automaticamente o certificado de assinatura quando expirar. Se o certificado for girado por qualquer motivo antes do prazo de validade ou se você não fornecer uma URL de metadados, o Azure AD não poderá renová-lo. Neste caso, você precisará atualizar o certificado de assinatura manualmente.

7. Selecione **Salvar**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Para configurar a federação direta no Azure AD usando o PowerShell

1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD. (Se você precisar de etapas detalhadas, o quickstart para adicionar um usuário convidado inclui a seção [Instale o módulo AzureADPreview mais recente](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Execute o comando a seguir: 
   ```powershell
   Connect-AzureAD
   ```
1. No prompt de entrada, entre com a conta de Administrador Global gerenciada. 
2. Execute os seguintes comandos, substituindo os valores do arquivo de metadados da federação. Para AD FS Server e Okta, o arquivo da federação `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`é federationmetadata.xml, por exemplo: . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Passo 3: Teste federação direta no Azure AD
Agora teste sua configuração direta de federação convidando um novo usuário convidado B2B. Para obter detalhes, consulte [Adicionar usuários de colaboração AZure AD B2B no portal Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Como edito uma relação direta de federação?

1. Vá para o [portal Azure.](https://portal.azure.com/) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Relações organizacionais**.
3. Selecione **provedores de identidade**
4. Em **provedores de identidade SAML/WS-Fed,** selecione o provedor.
5. No painel de detalhes do provedor de identidade, atualize os valores.
6. Selecione **Salvar**.


## <a name="how-do-i-remove-direct-federation"></a>Como removo a federação direta?
Você pode remover sua configuração direta da federação. Se você fizer isso, os usuários convidados diretos da federação que já resgataram seus convites não poderão fazer login. Mas você pode dar-lhes acesso aos seus recursos novamente, excluindo-os do diretório e reconvidando-os. Para remover a federação direta com um provedor de identidade no portal Azure AD:

1. Vá para o [portal Azure.](https://portal.azure.com/) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Relações organizacionais**.
3. Selecione **provedores de identidade**.
4. Selecione o provedor de identidade e, em seguida, **selecione Excluir**. 
5. Clique em **Sim** para confirmar a exclusão. 

Para remover a federação direta com um provedor de identidade usando o PowerShell:
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute o comando a seguir: 
   ```powershell
   Connect-AzureAD
   ```
3. No prompt de entrada, entre com a conta de Administrador Global gerenciada. 
4. Insira o seguinte comando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
