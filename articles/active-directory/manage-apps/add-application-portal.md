---
title: 'Início Rápido: Adicionar um aplicativo a seu locatário do Azure AD (Azure Active Directory)'
description: Este início rápido usa o portal do Azure para adicionar um aplicativo de galeria ao locatário do Azure AD (Azure Active Directory).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044c55e64c6532bf08d31b2c052e4ee9f5069ff1
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956093"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Início Rápido: Adicionar um aplicativo a seu locatário do Azure AD (Azure Active Directory)

O Azure AD (Azure Active Directory) tem uma galeria que contém milhares de aplicativos pré-integrados. Muitos dos aplicativos que sua organização usa provavelmente já estão na galeria. Este início rápido usa o portal do Azure para adicionar um aplicativo de galeria ao locatário do Azure AD. Este guia de início rápido concentra-se na adição de um aplicativo que já faz parte da galeria. Os aplicativos que não estão incluídos na galeria também podem ser integrados ao Azure AD, mas isso não é abordado neste guia de início rápido. 

Depois que um aplicativo é adicionado ao locatário do Azure AD, você pode:

- Configure as propriedades do aplicativo.
- Gerenciar o acesso de usuário ao aplicativo com uma política de acesso condicional.
- Configure o logon único para que os usuários possam entrar no aplicativo com suas credenciais do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar um aplicativo ao locatário do Azure AD, é preciso:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- (Opcional: Conclusão de [Exibir seus aplicativos](view-applications-portal.md)).

>[!IMPORTANT]
>Recomendamos usar um ambiente que não seja de produção para testar as etapas neste início rápido.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Adicionar um aplicativo ao seu locatário do Azure AD

Para adicionar um aplicativo de galeria ao locatário do Azure AD:

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.
2. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**. O painel **Todos os aplicativos** é aberto e exibe uma amostra aleatória dos aplicativos em seu locatário do Azure AD.
3. No painel **Aplicativos empresariais**, selecione **Novo aplicativo**. 
    ![Selecione Novo aplicativo para adicionar um aplicativo da galeria ao seu locatário](media/add-application-portal/new-application.png)
4. Mude para a nova experiência de versão prévia da galeria: Na faixa na parte superior da **página Adicionar um aplicativo**, selecione o link que diz **Clique aqui para experimentar a galeria de aplicativos nova e aprimorada**.
5. O painel **Procurar na Galeria do Azure AD (versão prévia)** é aberto e exibe blocos de aplicativos para plataformas de nuvem, aplicativos locais e aplicativos em destaque. Aplicativos listados na seção **Aplicativos em destaque** têm ícones que indicam se eles dão suporte ao SSO (logon único) federado e ao provisionamento.
    ![Pesquisar por um aplicativo por nome ou categoria](media/add-application-portal/browse-gallery.png)
6. Você pode procurar na galeria pelo aplicativo que deseja adicionar ou pode pesquisá-lo inserindo seu nome na caixa de pesquisa. Em seguida, selecione o aplicativo entre os resultados. No formulário, você pode editar o nome do aplicativo para atender às necessidades de sua organização. Neste exemplo, alteramos o nome para **GitHub-test**.
    ![Mostra como adicionar um aplicativo da galeria](media/add-application-portal/create-application.png)
7. Selecione **Criar**. Uma página de introdução é exibida com as opções para configurar o aplicativo para a organização.

Você terminou de adicionar um aplicativo. O próximo guia de início rápido mostra como alterar o logotipo e editar outras propriedades do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Configurar um aplicativo](add-application-portal-configure.md)
- [Configurar logon único](add-application-portal-setup-sso.md)
- [Excluir um aplicativo](delete-application-portal.md)