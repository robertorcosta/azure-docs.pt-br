---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186345"
---
Para registrar um aplicativo no locatário do Azure Active Directory B2C, você pode usar a experiência **Aplicativos** atual ou a nossa nova experiência **Registros de Aplicativo (versão prévia)** unificada. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *testapp1*.
1. Em **Aplicativo Web/API Web**, selecione **Sim**.
1. Para **URL de resposta**, insira `https://jwt.ms`
1. Selecione **Criar**.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *testapp1*.
1. Selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade**.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira `https://jwt.ms` na caixa de texto URL.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.

Depois que o registro do aplicativo for concluído, habilite o fluxo de concessão implícita:

1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. Em **Concessão implícita**, selecione ambas as caixas de seleção **Tokens de acesso** e **Tokens de ID**.
1. Clique em **Salvar**.