---
title: Mensagem de erro aparece na página do aplicativo depois de fazer login | Microsoft Docs
description: Como resolver problemas com o login do Azure AD quando o aplicativo retorna uma mensagem de erro.
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
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185488"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Uma página do aplicativo mostra uma mensagem de erro após o usuário fazer o sinal

Neste cenário, o Azure Active Directory (Azure AD) assina o usuário. Mas o aplicativo exibe uma mensagem de erro e não permite que o usuário termine o fluxo de login. O problema é que o aplicativo não aceitou a resposta que o Azure AD emitiu.

Existem várias razões possíveis pelas quais o aplicativo não aceitou a resposta do Azure AD. Se a mensagem de erro não identificar claramente o que está faltando na resposta, tente o seguinte:

-   Se o aplicativo for a galeria AD do Azure, verifique se você seguiu as etapas de [Como depurar o login único baseado em SAML para aplicativos no Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Use uma ferramenta como [o Fiddler](https://www.telerik.com/fiddler) para capturar a solicitação, resposta e token Do SAML.

-   Envie a resposta SAML para o fornecedor do aplicativo e pergunte o que está faltando.

## <a name="attributes-are-missing-from-the-saml-response"></a>Atributos estão faltando na resposta SAML

Para adicionar um atributo na configuração do Azure AD que será enviado na resposta AD do Azure, siga estas etapas:

1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como administrador global ou co-administrador.

2. Na parte superior do painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não ver o aplicativo que deseja, use o controle **Filtro** na parte superior da **Lista todos os aplicativos**. Defina a opção **Mostrar** como "Todas as aplicações".

6. Selecione o aplicativo que deseja configurar para o login único.

7. Após as cargas do aplicativo, selecione **Somente o login** no painel de navegação.

8. Na seção **Atributos do Usuário,** **selecione Exibir e editar todos os outros atributos do usuário**. Aqui você pode alterar quais atributos enviar para o aplicativo no token SAML quando os usuários fizerem login.

   Para adicionar um atributo:

   1. Selecione **Adicionar Atributo**. Digite o **Nome**e selecione o **Valor** na lista de paradas.

   1.  Selecione **Salvar**. Você verá o novo atributo na tabela.

9. Salve a configuração.

   Da próxima vez que o usuário entrar no aplicativo, o Azure AD enviará o novo atributo na resposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>O aplicativo não identifica o usuário

O login no aplicativo falha porque a resposta SAML está perdendo um atributo como uma função. Ou falha porque o aplicativo espera um formato ou valor diferente para o atributo **NameID** (User Identifier).

Se você estiver usando [o provisionamento automatizado do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) para criar, manter e remover usuários no aplicativo, verifique se o usuário foi provisionado para o aplicativo SaaS. Para obter mais informações, consulte [Nenhum usuário está sendo provisionado para um aplicativo azure AD Gallery](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Adicione um atributo à configuração do aplicativo Azure AD

Para alterar o valor do Identificador de Usuário, siga estas etapas:

1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como administrador global ou co-administrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não ver o aplicativo que deseja, use o controle **Filtro** na parte superior da **Lista todos os aplicativos**. Defina a opção **Mostrar** como "Todas as aplicações".

6. Selecione o aplicativo que deseja configurar para SSO.

7. Após as cargas do aplicativo, selecione **Somente o login** no painel de navegação.

8. Em **atributos do Usuário,** selecione o identificador exclusivo para o usuário na lista de paradas do Identificador de **Usuário.**

## <a name="change-the-nameid-format"></a>Alterar o formato NameID

Se o aplicativo esperar outro formato para o atributo **NameID** (User Identifier), consulte [Editando nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) para alterar o formato NameID.

O Azure AD seleciona o formato do atributo **NameID** (User Identifier) com base no valor selecionado ou no formato solicitado pelo aplicativo no SAML AuthRequest. Para obter mais informações, consulte a seção "NameIDPolicy" do [protocolo SAML de login único](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>O aplicativo espera um método de assinatura diferente para a resposta SAML

Para alterar quais partes do token SAML são assinadas digitalmente pelo Azure AD, siga estas etapas:

1. Abra o [portal Azure](https://portal.azure.com/) e faça login como administrador global ou co-administrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não ver o aplicativo que deseja, use o controle **Filtro** na parte superior da **Lista todos os aplicativos**. Defina a opção **Mostrar** como "Todas as aplicações".

6. Selecione o aplicativo que deseja configurar para o login único.

7. Após as cargas do aplicativo, selecione **Somente o login** no painel de navegação.

8. Em **Certificado de Assinatura SAML,** selecione Mostrar **configurações avançadas de assinatura de certificados**.

9. Selecione a **opção de assinatura** que o aplicativo espera entre essas opções:

   * **Assinar resposta SAML**
   * **Assinar resposta e afirmação do SAML**
   * **Assinar afirmação SAML**

   Da próxima vez que o usuário entrar no aplicativo, o Azure AD assinará a parte da resposta SAML que você selecionou.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>O aplicativo espera o algoritmo de assinatura SHA-1

Por padrão, o Azure AD assina o token SAML usando o algoritmo mais seguro. Recomendamos que você não altere o algoritmo de assinatura para *SHA-1* a menos que o aplicativo exija SHA-1.

Para alterar o algoritmo de assinatura, siga estas etapas:

1. Abra o [portal Azure](https://portal.azure.com/) e faça login como administrador global ou co-administrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não ver o aplicativo que deseja, use o controle **Filtro** na parte superior da **Lista todos os aplicativos**. Defina a opção **Mostrar** como "Todas as aplicações".

6. Selecione o aplicativo que deseja configurar para o único login.

7. Após as cargas do aplicativo, selecione **O único login** no painel de navegação do lado esquerdo do aplicativo.

8. Em **Certificado de Assinatura SAML,** selecione Mostrar **configurações avançadas de assinatura de certificados**.

9. Selecione **SHA-1** como algoritmo **de assinatura**.

   Da próxima vez que o usuário entrar no aplicativo, o Azure AD assinará o token SAML usando o algoritmo SHA-1.

## <a name="next-steps"></a>Próximas etapas
[Como depurar o login único baseado em SAML em aplicativos no Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
