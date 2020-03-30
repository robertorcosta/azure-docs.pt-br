---
title: Problemas ao entrar no aplicativo de galeria de assinatura única federada | Microsoft Docs
description: Diretrizes para os erros específicos ao entrar em um aplicativo configurado para o logon único federado baseado em SAML com Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 874d273e26a728afc0a1dc1a16852016797067ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367892"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao entrar em um aplicativo na galeria configurado para logon único federado

Para solucionar os problemas de login abaixo, recomendamos que você siga estas sugestões para obter um melhor diagnóstico e automatizar as etapas de resolução:

- Instale a [extensão do navegador seguro meus aplicativos](access-panel-extension-problem-installing.md) para ajudar o Azure Active Directory (Azure AD) a fornecer melhores diagnósticos e resoluções ao usar a experiência de teste no portal Azure.
- Reproduza o erro usando a experiência de teste na página de configuração do aplicativo no portal Azure. Saiba mais sobre [aplicativos de login único baseados em Debug SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório

*Erro AADSTS70001: O aplicativo com\/o identificador 'https: /contoso.com' não foi encontrado no diretório*.

**Possível causa**

O `Issuer` atributo enviado do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor identificador configurado para o aplicativo no Azure AD.

**Resolução**

Certifique-se `Issuer` de que o atributo na solicitação SAML corresponde ao valor identificador configurado no Azure AD. Se você usar a [experiência de teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro meus aplicativos, você não precisará seguir manualmente essas etapas.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global** ou **administrador.**

1.  Abra a extensão do diretório ativo do **Azure** selecionando **Todos os serviços** na parte superior do menu principal de navegação à esquerda.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o item Diretório Ativo do **Azure.**

1.  Selecione **aplicativos corporativos** no menu de navegação à mão esquerda do Azure Active Directory.

1.  Selecione **Todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos**.

1.  Selecione o aplicativo para o qual você deseja configurar o logon único.

1.  Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Verifique se o valor na caixa de texto identificador corresponde ao valor do identificador exibido no erro.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o aplicativo

*Erro AADSTS50011: O endereço\/de resposta 'https: /contoso.com' não corresponde aos endereços de resposta configurados para o aplicativo*

**Possível causa**

O `AssertionConsumerServiceURL` valor na solicitação SAML não corresponde ao valor ou padrão de URL de resposta configurado no Azure AD. O `AssertionConsumerServiceURL` valor na solicitação SAML é a URL que você vê no erro.

**Resolução**

Certifique-se `AssertionConsumerServiceURL` de que o valor na solicitação SAML corresponde ao valor de URL de resposta configurado no Azure AD. Se você usar a [experiência de teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro meus aplicativos, você não precisará seguir manualmente essas etapas.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global** ou **administrador.**

1.  Abra a extensão do diretório ativo do **Azure** selecionando **Todos os serviços** na parte superior do menu principal de navegação à esquerda.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o item Diretório Ativo do **Azure.**

1.  Selecione **aplicativos corporativos** no menu de navegação à mão esquerda do Azure Active Directory.

1.  Selecione **Todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos**.

1.  Selecione o aplicativo para o qual você deseja configurar o logon único.

1.  Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Verifique ou atualize o valor na caixa `AssertionConsumerServiceURL` de texto do URL de resposta para corresponder ao valor na solicitação SAML.    
    
Depois de atualizar o valor da URL de resposta no Azure AD, e corresponder ao valor enviado pelo aplicativo na solicitação SAML, você deve ser capaz de fazer login no aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função

*Erro AADSTS50105: O 'brian\@contoso.com' assinado no usuário não é atribuído a uma função para o aplicativo*.

**Possível causa**

O usuário não teve acesso concedido para o aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, siga os passos abaixo. Se você usar a [experiência de teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro meus aplicativos, você não precisará seguir manualmente essas etapas.

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global**.

1.  Abra a extensão do diretório ativo do **Azure** selecionando **Todos os serviços** na parte superior do menu principal de navegação à esquerda.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o item Diretório Ativo do **Azure.**

1.  Selecione **aplicativos corporativos** no menu de navegação à mão esquerda do Azure Active Directory.

1.  Selecione **Todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos**.

1.  Na lista de aplicativos, selecione aquele ao qual deseja atribuir a um usuário.

1.  Uma vez que o aplicativo seja carregado, selecione **Usuários e Grupos** no menu de navegação à esquerda do aplicativo.

1.  Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

1.  Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

1. Na caixa de pesquisa **Pesquisar por nome ou endereço de email**, digite o nome completo ou o endereço de email do usuário que você deseja adicionar.

1. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado da foto ou logotipo do perfil do usuário para adicionar o usuário à lista **Selecionada.**

1. **Opcional:** Se você quiser **adicionar mais de um usuário,** digite outro nome completo ou endereço de e-mail na caixa de pesquisa de nome ou endereço de **e-mail** e clique na caixa de seleção para adicionar o usuário à lista **Selecionada.**

1. Quando terminar de selecionar usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

1. **Opcional:** Clique no **seletor Selecionar função** no painel **Adicionar atribuição** para selecionar uma função para atribuir aos usuários selecionados.

1. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um curto período de tempo, os usuários selecionados poderão iniciar esses aplicativos usando os métodos descritos na seção de descrição da solução.

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida

*Erro AADSTS75005: a solicitação não é uma mensagem de protocolo Saml2 válida.*

**Possível causa**

O Azure Active Directory não oferece suporte para a solicitação SAML enviada pelo aplicativo para logon único. Alguns problemas comuns são:

-   Campos obrigatórios ausentes na solicitação SAML
-   Método codificado de solicitação SAML

**Resolução**

1. Capture a solicitação SAML. Siga o tutorial [Como depurar o login único baseado em SAML em aplicativos no Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md) para saber como capturar a solicitação SAML.

1. Entre em contato com o fornecedor do aplicativo e compartilhe as seguintes informações:

   -   Solicitação SAML

   -   [Requisitos de protocolo SAML de logon único do Azure AD](../develop/single-sign-on-saml-protocol.md)

O fornecedor de aplicativos deve validar que eles suportam a implementação do Azure AD SAML para o login único.

## <a name="misconfigured-application"></a>Aplicativo mal configurado

*Erro AADSTS650056: Aplicativo mal configurado. Isso pode ser devido a um dos seguintes: O cliente não listou nenhuma permissão nas permissões solicitadas no registro de solicitação do cliente. Ou, o admin não consentiu no inquilino. Ou, Verifique o identificador do aplicativo na solicitação para garantir que ele corresponda ao identificador de aplicativo cliente configurado. Entre em contato com seu admin para corrigir a configuração ou consentimento em nome do inquilino.*. .

**Possível causa**

O `Issuer` atributo enviado do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor identificador configurado para o aplicativo no Azure AD.

**Resolução**

Certifique-se `Issuer` de que o atributo na solicitação SAML corresponde ao valor identificador configurado no Azure AD. Se você usar a [experiência de teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro meus aplicativos, você não precisará seguir manualmente estas etapas:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global** ou **administrador.**

1.  Abra a extensão do diretório ativo do **Azure** selecionando **Todos os serviços** na parte superior do menu principal de navegação à esquerda.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o item Diretório Ativo do **Azure.**

1.  Selecione **aplicativos corporativos** no menu de navegação à mão esquerda do Azure Active Directory.

1.  Selecione **Todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos**.

1.  Selecione o aplicativo para o qual você deseja configurar o logon único.

1.  Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Verifique se o valor na caixa de texto identificador corresponde ao valor do identificador exibido no erro.


## <a name="certificate-or-key-not-configured"></a>Chave ou certificado não configurado

*Erro AADSTS50003: nenhuma chave de assinatura configurada.*

**Possível causa**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global** ou **administrador.**

1. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

1. Digite **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o item Diretório Ativo do **Azure.**

1. Selecione **aplicativos corporativos** no menu de navegação à mão esquerda do Azure Active Directory.

1. Selecione **Todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos**.

1. Selecione o aplicativo para o qual deseja configurar o logon único

1. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

1. Selecione **Criar novo certificado** na seção Certificado de assinatura **SAML.**

1. Selecione a data de validade e clique **em Salvar**.

1. Marque **Tornar o novo certificado ativo** para substituir o certificado ative. Em seguida, clique em **Salvar** na parte superior do painel e aceite para ativar o certificado de substituição.

1. Na seção **Certificado de Autenticação SAML**, clique em **remover** para remover o certificado **Não Usado**.

## <a name="saml-request-not-present-in-the-request"></a>A solicitação SAML não está presente na solicitação

*Erro AADSTS750054: SAMLRequest ou SAMLResponse devem estar presentes como parâmetros de seqüência de consulta na solicitação HTTP para vinculação de redirecionamento SAML.*

**Possível causa**

O Azure AD não pôde identificar a solicitação SAML dentro dos parâmetros de URL na solicitação HTTP. Isso pode acontecer se o aplicativo não estiver usando a vinculação http redirecionar ao enviar a solicitação SAML para o Azure AD.

**Resolução**

O aplicativo precisa enviar a solicitação SAML codificada no cabeçalho de localização usando a vinculação http redirecionamento. Para obter mais informações sobre como implementá-la, leia a seção sobre Associação de redirecionamento HTTP no [Documento de especificação de protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>O Azure AD está enviando o token para um ponto final incorreto

**Possível causa**

Durante a login única, se a solicitação de login não contiver uma URL de resposta explícita (Url de serviço ao consumidor de afirmação), o Azure AD selecionará qualquer um dos URLs de confiança configurados para esse aplicativo. Mesmo que o aplicativo tenha uma URL de resposta explícita https://127.0.0.1:444configurada, o usuário pode ser redirecionado . 

Quando o aplicativo foi adicionado como um aplicativo inexistente na galeria, o Azure Active Directory criou essa URL de resposta como um valor padrão. Esse comportamento mudou e o Azure Active Directory não adiciona mais essa URL por padrão. 

**Resolução**

Exclua os URLs de resposta não utilizados configurados para o aplicativo.

1.  Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global** ou **administrador.**

2.  Abra a extensão do diretório ativo do **Azure** selecionando **Todos os serviços** na parte superior do menu principal de navegação à esquerda.

3.  Digite **"Azure Active Directory"** na caixa de pesquisa do filtro e selecione o item Diretório Ativo do **Azure.**

4.  Selecione **aplicativos corporativos** no menu de navegação à mão esquerda do Azure Active Directory.

5.  Selecione **Todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos**.

6.  Selecione o aplicativo para o qual você deseja configurar o logon único.

7.  Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Na **URL de resposta (Url do Serviço de Defesa do Consumidor)**, exclua URLs de resposta não utilizadas ou padrão criadas pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as declarações SAML enviadas para um aplicativo

Para saber como personalizar as reclamações de atributoS SAML enviadas ao seu aplicativo, consulte [Mapeamento de sinistros no Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Próximas etapas

[Como depurar o logon único baseado em SAML em aplicativos no do Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
