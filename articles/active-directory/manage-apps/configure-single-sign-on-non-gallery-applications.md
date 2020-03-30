---
title: SAML single sign-on - aplicativos não-galeria - Plataforma de identidade Microsoft | Microsoft Docs
description: Configure o SSO (Single-On) para aplicativos que não são galerias na plataforma de identidade Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244206"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Configure o login único baseado em SAML em aplicativos que não são da galeria

Quando você [adiciona um aplicativo de galeria](add-gallery-app.md) ou um aplicativo web que não é [galeria](add-non-gallery-app.md) aos seus aplicativos Azure AD Enterprise, uma das opções únicas de login disponíveis para você é o login único baseado [em SAML](what-is-single-sign-on.md#saml-sso). Escolha o SAML sempre que possível para aplicativos que autenticam usando um dos protocolos SAML. Com o logon único SAML, o Azure AD realiza a autenticação do aplicativo usando a conta do Azure AD do usuário. O Azure AD comunica as informações do logon para o aplicativo por meio de um protocolo de conexão. Você pode mapear usuários para funções específicas de aplicativos com base em regras definidas em suas reivindicações SAML. Este artigo descreve como configurar o logon único baseado em SAML para um aplicativo inexistente na galeria. 

> [!NOTE]
> Adicionando um aplicativo da galeria? Encontre instruções de instalação passo a passo na [lista de tutoriais de aplicativo SaaS](../saas-apps/tutorial-list.md)

Para configurar o login único do SAML para um aplicativo sem galeria sem escrever código, você precisa ter uma assinatura juntamente com uma licença Azure AD Premium e o aplicativo deve suportar o SAML 2.0. Para obter mais informações sobre as versões do Azure AD, visite [Preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Antes de começar

Se o aplicativo não tiver sido adicionado ao inquilino Azure AD, consulte [Adicionar um aplicativo que não seja galeria](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Etapa 1. Editar a Configuração Básica do SAML

1. Entre no [portal do Azure](https://portal.azure.com) como administrador do aplicativo em nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

2. Navegue pelos aplicativos **do Azure Active Directory** > **Enterprise** e selecione o aplicativo na lista. 
   
   - Para pesquisar o aplicativo, no menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e, em seguida, **Aplicar**. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

3. Na seção **Gerenciar**, selecione **Logon único**. 

4. Selecione **SAML**. A página **Configurar logon único com SAML – Visualização** será exibida.

   ![Passo 1 Editar a configuração básica do SAML](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Para editar as opções de configuração básica do SAML, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **Configuração Básica do SAML**.

1. Insira as configurações a seguir. Você deve obter os valores do fornecedor de aplicativos. Pode inserir os valores manualmente ou carregar um arquivo de metadados para extrair o valor dos campos.

    | Configuração Básica do SAML | Iniciado por SP | iniciado por idP | Descrição |
    |:--|:--|:--|:--|
    | **Identificador (ID da Entidade)** | Obrigatório para alguns aplicativos | Obrigatório para alguns aplicativos | Identifica exclusivamente o aplicativo. O Azure AD envia o identificador para o aplicativo como o parâmetro Audience do token SAML. O aplicativo deve validá-lo. Esse valor também aparece como a ID da entidade em todos os metadados SAML fornecidos pelo aplicativo. Digite uma URL que usa<subdomain>o seguinte padrão: 'https:// .contoso.com' *Você pode encontrar esse valor como o elemento **Emissor** na solicitação **De AuthnRequest** (SAML) enviada pelo aplicativo.* |
    | **URL de resposta** | Obrigatório | Obrigatório | Especifica onde o aplicativo espera receber o token SAML. A URL de resposta também é chamada de URL do ACS (Serviço do Consumidor de Declaração). Você pode usar os campos de URL de resposta adicionais para especificar URLs de resposta múltipla. Por exemplo, você pode precisar de URLs de resposta adicionais para vários subdomínios. Ou, para fins de teste, você pode especificar URLs de resposta múltipla (URLs host locais e URLs públicas) ao mesmo tempo. |
    | **URL de inscrição** | Obrigatório | Não especifique | Quando um usuário abre essa URL, o provedor de serviço redireciona para o Azure AD a fim de autenticar e conectar o usuário. O Azure AD usa a URL para iniciar o aplicativo no Office 365 ou no painel de acesso do Azure AD. Quando em branco, o Azure AD executa o logon iniciado pelo IdP quando um usuário inicia o aplicativo do Office 365, do Painel de Acesso AD do Azure ou da URL SSO do Azure AD.|
    | **Estado de Retransmissão** | Opcional | Opcional | Especifica para onde o aplicativo deve redirecionar o usuário depois que a autenticação é concluída. Normalmente, o valor é uma URL válida para o aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente. Para obter mais informações, pergunte ao fornecedor do aplicativo.
    | **Logout URL** | Opcional | Opcional | Usada para enviar as respostas de Logoff do SAML novamente ao aplicativo.

Para obter mais informações, consulte [o protocolo SAML de inscrição única](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Etapa 2. Configurar Atributos e declarações de usuário 

Quando um usuário autentica no aplicativo, o Azure AD emite um token SAML com informações (ou reclamações) sobre o usuário que os identifica exclusivamente. Por padrão, essas informações incluem o nome de usuário, endereço de e-mail, primeiro nome e sobrenome do usuário. Você pode precisar personalizar essas reivindicações se, por exemplo, o aplicativo exigir valores de reivindicação específicos ou um formato **de Nome** diferente do nome de usuário. Os requisitos para aplicativos da galeria são descritos nos [tutoriais específicos do aplicativo](../saas-apps/tutorial-list.md) ou você pode solicitá-los ao fornecedor do aplicativo. As etapas gerais para configurar atributos e declarações de usuário são descritas abaixo.

1. Na seção **Atributos e declarações de usuário**, selecione o ícone **Editar** (um lápis) no canto superior direito.

   ![Passo 2 Configurar atributos e reclamações do Usuário](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Verifique o **Valor do Identificador de Nome**. O valor padrão é *user.principalname*. O identificador de usuário identifica exclusivamente cada usuário dentro do aplicativo. Por exemplo, se o endereço de email é tanto o nome de usuário quanto o identificador exclusivo, defina o valor como *user.mail*.

3. Para modificar o **Valor do Identificador de Nome**, selecione o ícone **Editar** (um lápis) para o campo **Valor do Identificador de Nome**. Faça as alterações apropriadas na origem e no formato do identificador, conforme necessário. Para obter detalhes, confira [Como editar a NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid). Salve as alterações quando terminar. 
 
4. Para configurar as declarações de grupo, selecione o ícone **Editar** para o campo **Grupos retornados na declaração**. Para obter detalhes, confira [Configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md).

5. Para adicionar uma declaração, selecione **Adicionar nova declaração** na parte superior da página. Insira o **Nome** e selecione a fonte apropriada. Se você selecionar a origem **Atributo**, precisará escolher o **Atributo de origem** que deseja usar. Se você selecionar a origem **Tradução**, precisará escolher a **Transformação** e o **Parâmetro 1** que deseja usar. Para obter detalhes, confira [Como adicionar declarações específicas do aplicativo](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Salve as alterações quando terminar. 

6. Selecione **Salvar**. A nova declaração será exibida na tabela.

   > [!NOTE]
   > Para obter outras maneiras de personalizar o token SAML do Azure AD para seu aplicativo, confira os recursos a seguir.
   >- Para criar funções personalizadas por meio do portal do Azure, confira [Configurar declarações de função](../develop/active-directory-enterprise-app-role-management.md).
   >- Para personalizar as declarações por meio do PowerShell, confira [Personalizar declarações – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Para modificar o manifesto do aplicativo a fim de configurar declarações opcionais para seu aplicativo, confira [Configurar declarações opcionais](../develop/active-directory-optional-claims.md).
   >- Para definir políticas de tempo de vida de token para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID, confira [Configurar tempos de vida de tokens](../develop/active-directory-configurable-token-lifetimes.md). Ou para restringir sessões de autenticação por meio do acesso condicional do Azure AD, confira [Funcionalidades do gerenciamento de sessão de autenticação](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Etapa 3. Gerenciar o certificado de autenticação SAML

O Azure AD usa um certificado para assinar os tokens SAML que ele envia para o aplicativo. Você precisa deste certificado para configurar a confiança entre o Azure AD e o aplicativo. Para obter detalhes sobre o formato de certificado, consulte a documentação do aplicativo SAML. Para obter mais informações, consulte [Gerenciar certificados para opções de](manage-certificates-for-federated-single-sign-on.md) assinatura de certificado único federado e [avançado no token SAML](certificate-signing-options.md).

A partir do Azure AD, você pode baixar o certificado ativo no formato Base64 ou Raw diretamente da página principal Configurar único de inscrição com a página **SAML.** Alternativamente, você pode obter o certificado ativo baixando o arquivo XML de metadados do aplicativo ou usando a URL de metadados da federação do aplicativo. Para visualizar, criar ou baixar seus certificados (ativos ou inativos), siga estas etapas.

1. Acesse a seção **Certificado de Autenticação SAML**. 

   ![Passo 3 Gerenciar o certificado de assinatura SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Verifique se que o certificado tem:

   - *A data de validade desejada.* Você pode configurar a data de validade por até três anos no futuro.
   - *Um status de ativo para o certificado desejado.* Se o status estiver **inativo,** mude o status para **Ativo**. Para alterar o status, clique com o botão direito do mouse na linha do certificado desejado e selecione **Tornar o certificado ativo**.
   - *A opção correta de assinatura e algoritmo.*
   - *O endereço de e-mail de notificação correto(es).* Quando o certificado ativo está perto da data de validade, o Azure AD envia uma notificação para o endereço de e-mail configurado neste campo.

2. Para baixar o certificado, selecione uma das opções para o formato Base64, formato Raw ou Metadata Da Federação XML. O Azure AD também fornece a **URL de Metadados de Federação do Aplicativo**, na qual você pode acessar os metadados específicos do aplicativo no formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

3. Para gerenciar, criar ou importar um certificado, selecione o ícone **Editar** (um lápis) no canto superior direito da seção Certificado de **Assinatura SAML.**

   ![Certificado de assinatura SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Tome qualquer uma das seguintes ações:

   - Para criar um certificado, selecione **Novo Certificado**, selecione a **Data de Validade** e, em seguida, **Salvar**. Para ativar o certificado, selecione o menu de contexto (**...**) e selecione **Tornar o certificado ativo**.
   - Para carregar um certificado com as credenciais de chave privada e do PFX, selecione **Importar Certificado** e procure o certificado. Insira a **Senha do PFX** e, em seguida, selecione **Adicionar**.  
   - Para configurar as opções avançadas de assinatura de certificado, use as opções a seguir. Para obter descrições dessas opções, confira o artigo [Opções avançadas de assinatura de certificado](certificate-signing-options.md).
      - Na lista suspensa **Opção de Assinatura**, escolha **Assinar resposta SAML**, **Assinar declaração SAML** ou **Assinar resposta e declaração SAML**.
      - Na lista suspensa **Algoritmo de Assinatura**, escolha **SHA-1** ou **SHA-256**.
   - Para notificar outras pessoas quando o certificado ativo estiver próximo da data de validade, insira os endereços nos campos **Endereços de email de notificação**.

4. Se você fez alterações, **selecione Salvar** na parte superior do Certificado de **Assinatura SAML.** 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Etapa 4. Configurar o aplicativo para usar o Azure AD

A seção **Configurar o \<nome_do_aplicativo>** lista os valores que precisam ser configurados no aplicativo, de modo que ele use o Azure AD como um provedor de identidade SAML. Os valores necessários variam de acordo com o aplicativo. Para obter detalhes, consulte a documentação do aplicativo SAML. Para encontrar a documentação, vá para o nome do ** \<aplicativo Configurar>** título e selecione Exibir **instruções passo a passo**. A documentação aparece na página **configurar configuração.** Essa página orienta você a preencher os valores de **URL de login,** **Azure AD**e **Logout URL** no nome do aplicativo **Configuração \<>** título.

1. Role para baixo até a seção **Configurar \<applicationName >**. 
   
   ![Passo 4 Configure o aplicativo](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Copie o valor de cada linha dessa seção, conforme necessário, e siga as instruções específicas do aplicativo para adicionar o valor ao aplicativo. Para aplicativos da galeria, veja a documentação selecionando **Exibir instruções passo a passo**. 
   - Os valores da **URL de Logon** e da **URL de Logoff** são resolvidos para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitações SAML para sua instância do Azure AD. 
   - O **Identificador do Azure AD** é o valor do **Emissor** no token SAML emitido para o aplicativo.
2. Depois de colar todos os valores nos campos apropriados, selecione **Salvar**.

## <a name="step-5-validate-single-sign-on"></a>Etapa 5. Validar logon único

Depois de configurar seu aplicativo para usar o Azure AD como um provedor de identidade baseado em SAML, você pode testar as configurações para ver se o login único funciona para sua conta. 

2. Role a página até a seção **Validar o logon único com o <applicationName>**.

   ![Passo 5 Validar o único login](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Selecione **Validar**. As opções de teste são exibidas.

4. Selecione **Entrar como o usuário atual**. 

Se o logon for bem-sucedido, você estará pronto para atribuir usuários e grupos ao aplicativo SAML.
Se uma mensagem de erro for exibida, conclua as seguintes etapas:

1. Copie e cole as informações específicas na caixa **Como é o erro?**.

    ![Obter diretrizes de resolução](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selecione **Obter diretrizes de resolução**. A causa raiz e as orientações para resolução aparecem.  Neste exemplo, o usuário não estava atribuído ao aplicativo.

3. Leia as diretrizes de resolução e, em seguida, se possível, corrija o problema.

4. Execute o teste novamente até que ele seja concluído com êxito.

Para obter mais informações, consulte [Debug SAML-based single sign-on to applications in Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Próximas etapas

- [Atribuir usuários e grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)
