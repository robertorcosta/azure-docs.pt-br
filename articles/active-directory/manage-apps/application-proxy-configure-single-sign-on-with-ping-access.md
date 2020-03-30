---
title: Autenticação baseada em cabeçalho com o PingAccess para Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Publique aplicativos com o PingAccess e o Proxy de Aplicativo a fim de oferecer suporte à autenticação baseada em cabeçalho.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367988"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticação baseada em cabeçalho para logon único com Proxy de Aplicativo e PingAccess

O Proxy do Aplicativo Azure Active Directory (Azure AD) fez parceria com o PingAccess para que seus clientes Azure AD possam acessar mais de seus aplicativos. O PingAccess expande as [ofertas existentes do Proxy de Aplicativo](application-proxy.md) para incluir acesso de logon único a aplicativos que usam cabeçalhos para autenticação.

## <a name="whats-pingaccess-for-azure-ad"></a>O que é pingaccess para Azure AD?

Com o PingAccess for Azure AD, você pode dar aos usuários acesso e logon único (SSO) a aplicativos que usam cabeçalhos para autenticação. O Proxy de Aplicativo trata esses aplicativos como qualquer outro, usando o Azure AD para autenticar o acesso e, depois, passando o tráfego por meio do serviço de conector. PingAccess fica na frente dos aplicativos e traduz o token de acesso do Azure AD em um cabeçalho. Em seguida, o aplicativo recebe a autenticação no formato que pode ler.

Os usuários não perceberão algo diferente quando entrarem para usar seus aplicativos corporativos. Eles ainda podem trabalhar de qualquer lugar e em qualquer dispositivo. Os conectores proxy do aplicativo direcionam o tráfego remoto para todos os aplicativos sem levar em conta o seu tipo de autenticação, para que eles ainda balancem cargas automaticamente.

## <a name="how-do-i-get-access"></a>Como posso obter acesso?

Como esse cenário vem de uma parceria entre o Azure Active Directory e o PingAccess, você precisa de licenças para ambos os serviços. No entanto, as assinaturas do Azure Active Directory Premium incluem uma licença básica PingAccess que abrange até 20 aplicativos. Se você precisar publicar mais de 20 aplicativos com base em cabeçalho, poderá adquirir uma licença adicional do PingAccess.

Para obter mais informações, consulte [Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicar seu aplicativo no Azure

Este artigo é para as pessoas publicarem um aplicativo com este cenário pela primeira vez. Além de detalhar as etapas de publicação, ele orienta você a começar com o Proxy de aplicativos e o PingAccess. Se você já configurou ambos os serviços, mas deseja uma atualização nas etapas de publicação, pule para a [seção Adicionar seu aplicativo ao Azure AD com](#add-your-application-to-azure-ad-with-application-proxy) a seção Proxy do aplicativo.

> [!NOTE]
> Como esse cenário é uma parceria entre o Azure AD e o PingAccess, algumas das instruções estão no site do Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Instalar um conector do Proxy de Aplicativo

Se você já habilitou o Proxy de aplicativo e instalou um conector, você pode pular essa seção e ir para [Adicionar seu aplicativo ao Azure AD com proxy de aplicativo.](#add-your-application-to-azure-ad-with-application-proxy)

O conector Proxy do aplicativo é um serviço do Windows Server que direciona o tráfego de seus funcionários remotos para seus aplicativos publicados. Para obter instruções de instalação mais [detalhadas, consulte Tutorial: Adicione um aplicativo local para acesso remoto através do Proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Faça login no [portal do Azure Active Directory](https://aad.portal.azure.com/) como administrador de aplicativos. A página **central do diretório ativo do Azure** é exibida.
1. Selecione o serviço de download do**aplicativo de proxy** > do aplicativo do >  **azure****active.** A **página de download do conector proxy do aplicativo** é exibida.

   ![Download do conector proxy do aplicativo](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Siga as instruções de instalação.

Baixar o conector deve ativar automaticamente o Proxy do aplicativo para o seu diretório, mas se não, você pode selecionar **Ativar proxy de aplicativo**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Adicione seu aplicativo ao Azure AD com proxy de aplicativo

Há duas ações necessárias no portal do Azure. Primeiro, você precisa publicar seu aplicativo com o Proxy de Aplicativo. Em seguida, você precisa coletar algumas informações sobre o aplicativo que você pode usar durante as etapas do PingAccess.

#### <a name="publish-your-application"></a>Publicar seu aplicativo

Primeiro você terá que publicar seu aplicativo. Esta ação envolve:

- Adicionando seu aplicativo no local ao Azure AD
- Atribuir um usuário para testar o aplicativo e escolher o SSO baseado em cabeçalho
- Configurando o URL de redirecionamento do aplicativo
- Concessão de permissões para usuários e outros aplicativos usarem seu aplicativo local

Para publicar seu próprio aplicativo no local:

1. Se você não fez na última seção, faça login no [portal do Azure Active Directory](https://aad.portal.azure.com/) como administrador de aplicativos.
1. Selecione **aplicativos Corporativos** > **Novo aplicativo** > **Adicione um aplicativo no local**. A página Adicionar sua própria página **de aplicativo no local** é exibida.

   ![Adicione seu próprio aplicativo no local](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Preencha os campos necessários com informações sobre sua nova aplicação. Use a orientação abaixo para as configurações.

   > [!NOTE]
   > Para obter um passo a passo mais detalhado desta etapa, consulte [Adicionar um aplicativo local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **URL interno**: Normalmente você fornece a URL que o leva à página de login do aplicativo quando você está na rede corporativa. Para este cenário, o conector precisa tratar o proxy PingAccess como a primeira página do aplicativo. Use este formato: `https://<host name of your PingAccess server>:<port>`. A porta é a 3000 por padrão, mas você pode configurá-la no PingAccess.

      > [!WARNING]
      > Para este tipo de tipo de tipo `https` de tipo de `http`tipo de tipo de tipo de tipo de acessação única, a URL interna deve ser usada e não pode usar .

   1. **Método de pré-autenticação**: Escolha **o Diretório Ativo do Azure**.
   1. **Traduzir URL em cabeçalhos**: Escolha **Não**.

   > [!NOTE]
   > Se este for seu primeiro aplicativo, use a porta 3000 para iniciar e retorne para atualizar essa configuração se alterar a configuração de PingAccess. Para aplicativos subseqüentes, a porta precisará corresponder ao Ouvinte configurado no PingAccess. Saiba mais sobre [ouvintes no PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Selecione **Adicionar**. A página de visão geral do novo aplicativo é exibida.

Agora, atribua um usuário para testes de aplicativos e escolha o registro único baseado em cabeçalho:

1. Na barra lateral do aplicativo, selecione **Usuários e grupos** > **Adicione usuários** > **e grupos (Número\<> Selecionados).** Uma lista de usuários e grupos aparece para você escolher.

   ![Mostra a lista de usuários e grupos](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selecione um usuário para testes de aplicativos e selecione **Selecionar**. Verifique se essa conta de teste tem acesso ao aplicativo local.
1. Selecione **Atribuir**.
1. Na barra lateral do aplicativo, selecione **'Cabeçalho** > de sinal**único'.**

   > [!TIP]
   > Se esta for a primeira vez que você usa o logon único com base em cabeçalhos, será necessário instalar o PingAccess. Para certificar-se de que sua assinatura do Azure seja automaticamente associada à sua instalação do PingAccess, use o link nesta página de logon único para baixar o PingAccess. Você pode abrir o site de download agora ou voltar a esta página mais tarde.

   ![Mostra a tela de logon baseada em cabeçalho e o PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selecione **Salvar**.

Em seguida, certifique-se de que sua URL redirecionada está definida para sua URL externa:

1. Na barra lateral central **do Azure Active Directory,** selecione registros do**Aplicativo**de Diretório >  **Ativo do Azure**. Uma lista de aplicativos é exibida.
1. Selecione seu aplicativo.
1. Selecione o link ao lado **de UrIs de redirecionamento,** mostrando o número de URIs de redirecionamento configurados para clientes web e públicos. O ** \<nome do aplicativo> - Página de autenticação** é exibida.
1. Verifique se a URL externa que você atribuiu ao seu aplicativo anteriormente está na lista **Redirecionar URIs.** Se não for, adicione a URL externa agora, usando um tipo uri de redirecionamento da **Web**e selecione **Salvar**.

Finalmente, configure seu aplicativo local para que os usuários tenham acesso à leitura e outros aplicativos tenham acesso à leitura/gravação:

1. Na barra lateral de **registros** do aplicativo para o seu aplicativo, selecione **permissões** > de API**Adicione uma permissão** > **Microsoft APIs** > **Microsoft Graph**. A página **de permissões de API** de solicitação para **o Microsoft Graph** é exibida, que contém as APIs do Diretório Ativo do Windows Azure.

   ![Mostra a página de permissões da API de solicitação](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selecione **permissões delegadas** > **usuário.Ler****User** > .
1. Selecione **as permissões** > do aplicativo**Application.ReadWrite.All****Application** > .
1. Selecione **Adicionar Permissões**.
1. Na página de permissões da **API,** selecione o consentimento do anúncio **de Grant \<para o seu nome de diretório>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Coletar informações sobre as etapas do PingAccess

Você precisa coletar essas três informações (todas guids) para configurar seu aplicativo com pingAccess:

| Nome do campo Azure AD | Nome do campo PingAccess | Formato de dados |
| --- | --- | --- |
| **ID do Aplicativo (cliente)** | **ID do cliente** | GUID |
| **ID do Diretório (locatário)** | **Emissor** | GUID |
| `PingAccess key` | **Segredo do Cliente** | Cadeia aleatória |

Para coletar essas informações:

1. Na barra lateral central **do Azure Active Directory,** selecione registros do**Aplicativo**de Diretório >  **Ativo do Azure**. Uma lista de aplicativos é exibida.
1. Selecione seu aplicativo. A página **de registros do Aplicativo** para o seu aplicativo é exibida.

   ![Visão geral do registro de um aplicativo](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Ao lado do valor **de ID do aplicativo (cliente),** selecione o ícone **Copiar para a área de transferência** e, em seguida, copie e salve-o. Você especifica esse valor mais tarde como ID do cliente do PingAccess.
1. Em seguida, o valor **de ID do Diretório (inquilino),** também selecione **Copiar para área de transferência,** em seguida, copie e salve-o. Você especifica esse valor mais tarde como emissor do PingAccess.
1. Na barra lateral dos registros do **App** para o seu aplicativo, selecione **Certificados e segredos** > **Novo segredo do cliente**. A **página 'Adicionar uma página secreta do cliente'** é exibida.

   ![Mostra a página adicionar um cliente secreto](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Em **Descrição,** digite `PingAccess key`.
1. Em **Expirações,** escolha como definir a tecla PingAccess: **Em 1 ano**, **em 2 anos**ou **nunca**.
1. Selecione **Adicionar**. A tecla PingAccess aparece na tabela de segredos do cliente, com uma seqüência aleatória que preenche automaticamente no campo **VALOR.**
1. Ao lado do campo **VALUE** da tecla PingAccess, selecione o ícone **Copiar para a área de transferência** e, em seguida, copie e salve-o. Você especifica esse valor mais tarde como o segredo do cliente do PingAccess.

**Atualize `acceptMappedClaims` o campo:**

1. Faça login no [portal do Azure Active Directory](https://aad.portal.azure.com/) como administrador de aplicativos.
1. Selecione registros do**Aplicativo**de >  **Diretório Ativo do Azure**. Uma lista de aplicativos é exibida.
1. Selecione seu aplicativo.
1. Na barra lateral da página de registros do **App** para o seu aplicativo, **selecione Manifest**. O código JSON manifesto para o registro do seu aplicativo é exibido.
1. Procure o `acceptMappedClaims` campo e altere `True`o valor para .
1. Selecione **Salvar**.

### <a name="use-of-optional-claims-optional"></a>Uso de sinistros opcionais (opcional)

As reivindicações opcionais permitem que você adicione reivindicações padrão, mas não incluídas por padrão, que todo usuário e inquilino tem. Você pode configurar reivindicações opcionais para o seu aplicativo modificando o manifesto do aplicativo. Para obter mais informações, consulte o [artigo manifesto do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Exemplo para incluir endereço de e-mail no access_token que o PingAccess consumirá:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Uso da política de mapeamento de sinistros (opcional)

[Política de mapeamento de sinistros (pré-visualização)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) para atributos que não existem no AzureAD. O mapeamento de sinistros permite que você migre aplicativos antigos on-prem para a nuvem adicionando reivindicações personalizadas adicionais que são apoiadas por seus ADFS ou objetos de usuário

Para fazer seu aplicativo usar uma reclamação personalizada e incluir campos adicionais, certifique-se de que você também [criou uma política de mapeamento de sinistros personalizados e a atribuiu ao aplicativo](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Para usar uma declaração personalizada, você também deve ter uma política personalizada definida e atribuída ao aplicativo. Essa política deve incluir todos os atributos personalizados necessários.
>
> Você pode fazer definição de política e atribuição através do PowerShell ou do Microsoft Graph. Se você estiver fazendo isso no PowerShell, `New-AzureADPolicy` talvez seja necessário primeiro usá-lo e, em seguida, atribuí-lo ao aplicativo com `Add-AzureADServicePrincipalPolicy`. Para obter mais informações, consulte [Atribuição de diretiva de mapeamento de sinistros](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Exemplo:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Habilitar o PingAccess para usar reclamações personalizadas

Permitir que o PingAccess use reclamações personalizadas é opcional, mas necessário se você espera que o aplicativo consuma reclamações adicionais.

Quando você configuraro pingaccess na etapa seguinte, a Sessão da Web que você criará (Configurações >Sessões web >de acesso) deve ter o Perfil de **Solicitação** **desmarcado e a atualização de atributos do usuário definidos** como **Não**

## <a name="download-pingaccess-and-configure-your-application"></a>Baixe PingAccess e configure seu aplicativo

Agora que você concluiu todas as etapas de instalação do Azure Active Directory, será possível prosseguir para a configuração do PingAccess.

As etapas detalhadas para a parte pingaccess deste cenário continuam na documentação de Identidade ping. Siga as instruções em [Configure PingAccess for Azure AD para proteger aplicativos publicados usando o Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) no site da Ping Identity.

Essas etapas ajudam você a instalar o PingAccess e configurar uma conta pingAccess (se você ainda não tiver uma). Em seguida, para criar uma conexão Azure AD OpenID Connect (OIDC), você configura um provedor de token com o valor de **ID do Diretório (inquilino)** que você copiou do portal Azure AD. Em seguida, para criar uma sessão web no PingAccess, `PingAccess key` você usa o ID e valores do **aplicativo (cliente).** Em seguida, configure o mapeamento de identidade e crie um host virtual, site e aplicativo.

### <a name="test-your-application"></a>Teste seu aplicativo

Quando você tiver concluído todas essas etapas, sua aplicação deve estar em funcionamento. Para testá-lo, abra um navegador e navegue até a URL externa que você criou quando publicou o aplicativo no Azure. Faça login com a conta de teste que você atribuiu ao aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Configure o PingAccess para Azure AD para proteger aplicativos publicados usando o Proxy de aplicativo AD do Microsoft Azure](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md)
- [Solucionar problemas e mensagens de erro do Application Proxy](application-proxy-troubleshoot.md)
