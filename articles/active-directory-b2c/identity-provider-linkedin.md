---
title: Configurar a inscrição e a entrada com uma conta do LinkedIn
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do LinkedIn em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259451"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do LinkedIn usando o Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Criar um aplicativo do LinkedIn

Para usar uma conta do LinkedIn como um [provedor de identidade](authorization-code-flow.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do LinkedIn, poderá se inscrever em [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Entre no [site de Desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) com suas credencias de conta do LinkedIn.
1. Selecione **meus aplicativos**e, em seguida, clique em **criar aplicativo**.
1. Insira o **nome do aplicativo**, a página do LinkedIn, a URL **da**política de **privacidade**e o logotipo do **aplicativo**.
1. Concorde com os **termos de uso da API do** LinkedIn e clique em **criar aplicativo**.
1. Selecione a guia **autenticação** . Em **chaves de autenticação**, copie os valores para **ID do cliente** e segredo do **cliente**. Você precisará de ambos para configurar o LinkedIn como um provedor de identidade em seu locatário. O **segredo do cliente** é uma credencial de segurança importante.
1. Selecione o lápis de edição ao lado de **URLs de redirecionamento autorizado para seu aplicativo**e, em seguida, selecione **Adicionar URL de redirecionamento**. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` , substituindo `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C. Selecione **Atualização**.
2. Por padrão, seu aplicativo do LinkedIn não é aprovado para escopos relacionados à entrada. Para solicitar uma revisão, selecione a guia **produtos** e, em seguida, selecione **entrar com o LinkedIn**. Quando a revisão for concluída, os escopos necessários serão adicionados ao seu aplicativo.
   > [!NOTE]
   > Você pode exibir os escopos que atualmente são permitidos para seu aplicativo na guia **autenticação** na seção **escopos do OAuth 2,0** .

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurar uma conta do LinkedIn como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **LinkedIn**.
1. Insira um **Nome**. Por exemplo, *LinkedIn*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo do LinkedIn que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Clique em **Salvar**.

## <a name="migration-from-v10-to-v20"></a>Migração de v 1.0 para v 2.0

O LinkedIn [atualizou recentemente suas APIs de v 1.0 para v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, Azure AD B2C só é capaz de obter o nome completo do usuário do LinkedIn durante a inscrição. Se um endereço de email for um dos atributos coletados durante a inscrição, o usuário deverá inserir manualmente o endereço de email e validá-lo.
