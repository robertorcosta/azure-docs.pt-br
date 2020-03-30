---
title: Problemas ao entrar no aplicativo de assinatura único federado não-galeria
description: Diretrizes para os problemas específicos que você pode enfrentar ao entrar em um aplicativo configurado para o logon único federado baseado em SAML com Azure AD
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd951f0b4d2f4887630e29cbd3b0ae429b9f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367868"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao entrar em um aplicativo inexistente na galeria configurado para logon único federado

Para solucionar os problemas de login abaixo, recomendamos que você siga estas sugestões para obter um melhor diagnóstico e automatizar as etapas de resolução:

- Instale a [extensão do navegador seguro meus aplicativos](access-panel-extension-problem-installing.md) para ajudar o Azure Active Directory (Azure AD) a fornecer melhores diagnósticos e resoluções ao usar a experiência de teste no portal Azure.
- Reproduza o erro usando a experiência de teste na página de configuração do aplicativo no portal Azure. Saiba mais sobre [aplicativos de login único baseados em Debug SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório

*Erro AADSTS70001: O `https://contoso.com` aplicativo com identificador não foi encontrado no diretório*.

**Possível causa**

O atributo que o Emissor envia do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor do Identificador configurado no aplicativo Azure AD.

**Resolução**

Certifique-se `Issuer` de que o atributo na solicitação SAML corresponde ao valor identificador configurado no Azure AD. Se você usar a [experiência de teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro meus aplicativos, você não precisará seguir manualmente essas etapas.

1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global** ou **co-administrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Verifique se o valor na caixa de texto identificador corresponde ao valor do identificador exibido no erro.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o aplicativo. 

*Erro AADSTS50011: O `https://contoso.com` endereço de resposta não corresponde aos endereços de resposta configurados para o aplicativo* 

**Possível causa** 

O valor de AssertionConsumerServiceURL na solicitação SAML não corresponde ao valor da URL de resposta ou ao padrão configurado no Azure AD. O valor de AssertionConsumerServiceURL na solicitação SAML é a URL que você vê no erro. 

**Resolução** 

Certifique-se `Issuer` de que o atributo na solicitação SAML corresponde ao valor identificador configurado no Azure AD. Se você usar a [experiência de teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro meus aplicativos, você não precisará seguir manualmente essas etapas.
 
1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global** ou **co-administrador.** 

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal. 

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**. 

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory. 

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos. 

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**
  
6. Selecione o aplicativo para o qual deseja configurar o logon único

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Verifique ou atualize o valor na caixa `AssertionConsumerServiceURL` de texto do URL de resposta para corresponder ao valor na solicitação SAML.    
    
Depois de atualizar o valor da URL de resposta no Azure AD, e corresponder ao valor enviado pelo aplicativo na solicitação SAML, você deve ser capaz de fazer login no aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função

*Erro AADSTS50105: O `brian\@contoso.com` usuário assinado não é atribuído a uma função para o aplicativo*

**Possível causa**

O usuário não teve acesso concedido para o aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, siga os passos abaixo. Se você usar a [experiência de teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal Azure com a Extensão do Navegador Seguro meus aplicativos, você não precisará seguir manualmente essas etapas.

1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos.**

6. Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7. Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um breve período, os usuários que você selecionou poderão iniciar esses aplicativos usando os métodos descritos na seção de descrição da solução.

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida

*Erro AADSTS75005: a solicitação não é uma mensagem de protocolo Saml2 válida.*

**Possível causa**

O Azure AD não oferece suporte para a solicitação SAML enviada pelo aplicativo para logon único. Alguns problemas comuns são:

-   Campos obrigatórios ausentes na solicitação SAML

-   Método codificado de solicitação SAML

**Resolução**

1.  Capturar solicitação SAML. siga o tutorial em [como depurar o logon único baseado em SAML em aplicativos no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) para saber mais sobre como capturar a solicitação SAML.

2.  Contate o fornecedor do aplicativo e compartilhe:

    -   Solicitação SAML

    -   [Requisitos de protocolo SAML de logon único do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

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

Erro AADSTS50003: nenhuma chave de assinatura configurada.

**Possível causa**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global** ou **co-administrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se você não ver o aplicativo que deseja aparecer aqui, use o controle **Filtro** na parte superior da **Lista todos os aplicativos** e defina a opção **Mostrar** para Todos **os Aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. clique em **Criar novo certificado** na seção **Certificado de Autenticação SAML**.

9. Selecione a data de validade. Em seguida, clique **em Salvar.**

10. Marque **Tornar o novo certificado ativo** para substituir o certificado ative. Em seguida, clique em **Salvar** na parte superior do painel e aceite para ativar o certificado de substituição.

11. Na seção **Certificado de Autenticação SAML**, clique em **remover** para remover o certificado **Não Usado**.

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

Para saber como personalizar as declarações de atributo SAML enviadas para seu aplicativo, consulte [Mapeamento de declarações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
[Requisitos de protocolo SAML de logon único do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
