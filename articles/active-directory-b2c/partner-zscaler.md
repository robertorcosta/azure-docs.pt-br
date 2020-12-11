---
title: Tutorial para configurar o Azure Active Directory B2C com o Zscaler
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com o Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ff51c2a71dfcaec580733a92e265628ac816e229
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095919"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Tutorial para configurar o acesso privado do Zscaler com o Azure Active Directory B2C para acesso híbrido seguro

Neste tutorial, saiba como integrar a autenticação de Azure AD B2C com o [ZPA (Zscaler Private Access)](https://www.zscaler.com/products/zscaler-private-access). O ZPA fornece acesso seguro e baseado em políticas a aplicativos e ativos privados sem o custo, os aborrecimentos ou os riscos de segurança de uma VPN (rede virtual privada). A oferta de acesso híbrido seguro do Zscaler permite uma superfície de ataque zero para aplicativos voltados para o consumidor quando combinado com Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- [Um locatário Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) que está vinculado à sua assinatura do Azure.

- [Assinatura do ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Descrição do cenário

A integração do ZPA inclui os seguintes componentes:

- Azure AD B2C – o IdP (provedor de identidade) responsável por verificar as credenciais do usuário. Também é responsável por inscrever um novo usuário.

- ZPA-o serviço responsável por proteger o aplicativo Web impondo o [acesso de confiança zero](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).

- Aplicativo Web-hospeda o serviço que o usuário está tentando acessar.

O diagrama a seguir mostra como o ZPA se integra ao Azure AD B2C.

![Imagem mostra o diagrama de arquitetura Zscaler](media/partner-zscaler/zscaler-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega a um portal do usuário do ZPA ou a um aplicativo de acesso do navegador ZPA.
| 2. | O ZPA requer as informações de contexto do usuário antes de poder decidir se deseja permitir que o usuário acesse o aplicativo Web. Para autenticar o usuário, o ZPA executa um redirecionamento SAML para a página de logon Azure AD B2C.  
| 3. | O usuário chega na página de logon do Azure AB B2C. Se for um novo usuário, o usuário se inscreverá para criar uma nova conta. Um usuário existente entrará usando suas credenciais existentes. Azure AD B2C valida a identidade do usuário.
| 4. | Após a autenticação bem-sucedida, Azure AD B2C redireciona o usuário de volta para ZPA junto com a Asserção SAML. ZPA verifica a Asserção SAML e define o contexto do usuário.
| 5. | ZPA avalia as políticas de acesso para o usuário. Se o usuário tiver permissão para acessar o aplicativo Web, a conexão poderá passar.

## <a name="onboard-to-zpa"></a>Integração ao ZPA

Este tutorial pressupõe que você já tenha uma configuração funcional do ZPA. Se você estiver começando a usar o ZPA, consulte o [Guia de configuração passo a passo para o ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Integrar ao Azure AD B2C

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Parte 1-configurar Azure AD B2C como um IdP em ZPA

Para configurar Azure AD B2C como um [IDP (provedor de identidade) em ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign):

1. Faça logon no [portal de administração do ZPA](https://admin.private.zscaler.com)

2. Vá para **Administração**  >  **configuração do IDP**

3. Selecione **Adicionar configuração IDP**

4. Para obter **1 informação do IDP**, insira o seguinte:

   a. **Nome**: Azure ad B2C

   b. **Logon único**: Selecionar usuário

   c. **Domínios**: escolha os domínios de autenticação que você deseja associar a este IDP e, em seguida, selecione **concluído**

   ![Imagem mostra as informações do IDP](media/partner-zscaler/add-idp-configuration.png)

5. Selecione **Avançar**

6. Para **2 metadados de SP**:

   a. Copie a URL do provedor de serviços e anote-a para uso posterior.

   b. Copie a ID da entidade do provedor de serviços e anote-a para uso posterior.

   ![Imagem mostra as informações de metadados do SP](media/partner-zscaler/sp-metadata.png)

7. Selecione **Pausar**

O restante da configuração do IdP será retomado após a configuração de Azure AD B2C.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>Parte 2-configurar a política personalizada no Azure AD B2C

>[!Note]
>Esta etapa será necessária somente se você ainda não tiver políticas personalizadas. Se você já tiver uma ou mais políticas personalizadas, poderá ignorar esta etapa.

O artigo [introdução às políticas personalizadas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) fornece instruções sobre como configurar políticas personalizadas no seu locatário do Azure ad B2C.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Parte 3-registrar o ZPA como um aplicativo SAML no Azure AD B2C

O artigo [registrar um aplicativo SAML no Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) fornece instruções sobre como configurar um aplicativo saml no Azure ad B2C. Na [etapa 3,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata), você será fornecido com a URL de metadados do SAML do IDP usada pelo Azure ad B2C. Você precisará disso para uso posterior.

Siga as instruções até a [etapa 4,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). Na etapa 4,2 da instrução, você será solicitado a atualizar o manifesto do aplicativo. Atualize as propriedades da seguinte maneira:

- **identifierUris**: Use a ID de entidade do provedor de serviço anotada na **parte 1, etapa 6a**.

- **samlMetadataUrl**: ignore essa propriedade, pois ZPA não hospeda uma URL de metadados do SAML.

- **replyUrlsWithType**: Use a URL do provedor de serviços anotada na **parte 1, etapa 6B**.

- **logoutUrl**: ignore essa propriedade, pois ZPA não dá suporte a uma URL de logoff.

O restante das etapas não são relevantes para este tutorial.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Parte 4-extrair os metadados de SAML do IdP do Azure AD B2C

Na etapa anterior, você precisa obter uma URL de metadados SAML no seguinte formato:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

em que `<tenant-name>` é o nome do seu locatário de Azure ad B2C e `<policy-name>` é o nome da política SAML personalizada que você criou na última etapa.

Por exemplo, https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata

Abra um navegador da Web e navegue até a URL de metadados do SAML. Quando a página for carregada, clique com o botão direito do mouse em qualquer lugar da página. Selecione **salvar página como** e salve o arquivo em seu computador; Você usará isso na próxima parte.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>Parte 5-concluir a configuração do IdP no ZPA

Conclua a [configuração do IDP no portal de administração do ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) que foi parcialmente configurado na parte 1:

1. Faça logon no [portal de administração do ZPA](https://admin.private.zscaler.com)

2. Vá para **Administração**  >  **configuração do IDP**.

3. Selecione o IdP que foi configurado na parte 1 e selecione **retomar**.

4. Para **3 criar IDP**

   a. Vá para **arquivo de metadados IDP**  >  **Selecione Arquivo** e carregue o arquivo de metadados que você salvou na parte 4.

   b. Verifique se o **status** da configuração IDP está **habilitado**.

   c. Selecione **Salvar**.

      ![Imagem mostra a criar informações IDP](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testar a solução

Acesse um portal do usuário do ZPA ou um aplicativo de acesso ao navegador e teste o processo de inscrição ou entrada. Ele deve resultar em uma autenticação SAML bem-sucedida.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Introdução às políticas personalizadas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [Registrar um aplicativo SAML no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Guia de configuração passo a passo para ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Configurando um IdP para logon único](https://help.zscaler.com/zpa/configuring-idp-single-sign)
