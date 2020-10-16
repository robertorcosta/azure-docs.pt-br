---
title: Problemas ao entrar em aplicativos configurados para logon único baseado em SAML
description: Diretrizes para os erros específicos ao entrar em um aplicativo que você configurou para logon único federado baseado em SAML com o Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.openlocfilehash: 715cbb78cc33ce2ae9b22e743abfea81afc2cb55
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108177"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problemas ao entrar em aplicativos configurados para logon único baseado em SAML
Para solucionar os problemas de entrada abaixo, recomendamos o seguinte para diagnosticar melhor e automatizar as etapas de resolução:

- Instale a [extensão do navegador seguro meus aplicativos](access-panel-extension-problem-installing.md) para ajudar a Azure Active Directory (Azure AD) a fornecer melhor diagnóstico e resoluções ao usar a experiência de teste no portal do Azure.
- Reproduza o erro usando a experiência de teste na página de configuração do aplicativo no portal do Azure. Saiba mais em [depurar aplicativos de logon único baseados em SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

Se você usar a [experiência de teste](../azuread-dev/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir as etapas abaixo para abrir a página de configuração de logon único baseada em SAML.

Para abrir a página de configuração de logon único baseado em SAML:
1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador**.
1.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
1.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .
1.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.
1.  Selecione **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.
    Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos**.
1.  Selecione o aplicativo para o qual você deseja configurar o logon único.
1. Depois que o aplicativo for carregado, selecione **logon único** no menu de navegação esquerdo do aplicativo.
1. Selecione SSO baseado em SAML.

## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Causa possível**

O `Issuer` atributo enviado do aplicativo para o Azure ad na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD.

Na página configuração do SSO baseado em SAML, na seção **configuração básica do SAML** , verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o aplicativo
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Causa possível**

O `AssertionConsumerServiceURL` valor na solicitação SAML não corresponde ao padrão ou ao valor da URL de resposta configurado no Azure AD. O `AssertionConsumerServiceURL` valor na solicitação SAML é a URL que você vê no erro.

**Resolução**

Verifique se o `AssertionConsumerServiceURL` valor na solicitação SAML corresponde ao valor da URL de resposta configurado no Azure AD. 

Verifique ou atualize o valor na caixa de texto URL de resposta para corresponder ao `AssertionConsumerServiceURL` valor na solicitação SAML.   
    
Depois de atualizar o valor da URL de resposta no Azure AD, e ele corresponder ao valor enviado pelo aplicativo na solicitação SAML, você deverá ser capaz de entrar no aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Causa possível**

O usuário não teve acesso concedido para o aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, consulte [início rápido: atribuir usuários a um aplicativo](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Causa possível**

O Azure Active Directory não oferece suporte para a solicitação SAML enviada pelo aplicativo para logon único. Alguns problemas comuns são:
- Campos obrigatórios ausentes na solicitação SAML
- Método codificado de solicitação SAML

**Resolução**

1. Capture a solicitação SAML. Siga o tutorial [como depurar o logon único baseado em SAML para aplicativos no Azure ad](../azuread-dev/howto-v1-debug-saml-sso-issues.md) para saber como capturar a solicitação SAML.
1. Entre em contato com o fornecedor do aplicativo e compartilhe as seguintes informações:
    - Solicitação SAML
    - [Requisitos de protocolo SAML de logon único do Azure AD](../develop/single-sign-on-saml-protocol.md)

O fornecedor do aplicativo deve validar que eles dão suporte à implementação SAML do Azure AD para logon único.

## <a name="misconfigured-application"></a>Aplicativo configurado incorretamente
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Causa possível**

O `Issuer` atributo enviado do aplicativo para o Azure ad na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. 

Verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.

## <a name="certificate-or-key-not-configured"></a>Chave ou certificado não configurado
`Error AADSTS50003: No signing key configured.`

**Causa possível**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:
1. Na tela de configuração do SSO baseado em SAML, selecione **criar novo certificado** na seção **certificado de autenticação SAML** .
1. Selecione data de validade e clique em **salvar**.
1. Marque **Tornar o novo certificado ativo** para substituir o certificado ative. Em seguida, clique em **Salvar** na parte superior do painel e aceite para ativar o certificado de substituição.
1. Na seção **Certificado de Autenticação SAML**, clique em **remover** para remover o certificado **Não Usado**.

## <a name="saml-request-not-present-in-the-request"></a>A solicitação SAML não está presente na solicitação
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Causa possível**

O Azure AD não pôde identificar a solicitação SAML dentro dos parâmetros de URL na solicitação HTTP. Isso pode acontecer se o aplicativo não estiver usando a associação de redirecionamento HTTP ao enviar a solicitação SAML ao Azure AD.

**Resolução**

O aplicativo precisa enviar a solicitação SAML codificada para o cabeçalho Location usando a associação de redirecionamento HTTP. Para obter mais informações sobre como implementá-la, leia a seção sobre Associação de redirecionamento HTTP no [Documento de especificação de protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>O Azure AD está enviando o token para um ponto de extremidade incorreto
**Causa possível**

Durante o logon único, se a solicitação de entrada não contiver uma URL de resposta explícita (URL de serviço do consumidor de asserção), o Azure AD selecionará qualquer uma das URLs de resposta configuradas para esse aplicativo. Mesmo que o aplicativo tenha uma URL de resposta explícita configurada, o usuário pode ser redirecionado https://127.0.0.1:444 . 

Quando o aplicativo foi adicionado como um aplicativo inexistente na galeria, o Azure Active Directory criou essa URL de resposta como um valor padrão. Esse comportamento mudou e o Azure Active Directory não adiciona mais essa URL por padrão. 

**Resolução**

Exclua as URLs de resposta não utilizadas configuradas para o aplicativo.

Na página configuração do SSO baseado em SAML, na seção **URL de resposta (URL do serviço do consumidor de asserção)** , exclua as URLs de resposta não usadas ou padrão criadas pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as declarações SAML enviadas para um aplicativo
Para saber como personalizar as declarações de atributo SAML enviadas ao seu aplicativo, consulte [mapeamento de declarações em Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="errors-related-to-misconfigured-apps"></a>Erros relacionados a aplicativos configurados incorretamente
Verifique se as configurações do portal correspondem às que você tem no seu aplicativo. Especificamente, compare o ID do Aplicativo/Cliente, URLs de Resposta, Chaves/Segredos do Cliente e URI da ID do Aplicativo.

Compare o recurso que você está solicitando acesso no código com as permissões configuradas na guia **Recursos Necessários** para certificar-se de que está solicitando somente os recursos que você configurou.

## <a name="next-steps"></a>Próximas etapas
- [Série de Início Rápido no Gerenciamento de Aplicativos](add-application-portal-assign-users.md)
- [Como depurar o logon único baseado em SAML em aplicativos no do Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
- [Requisitos de protocolo SAML de logon único do Azure AD](../develop/active-directory-single-sign-on-protocol-reference.md)
