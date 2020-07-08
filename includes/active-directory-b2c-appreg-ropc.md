---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f941f67105227b3973386b015344d5fa787c3485
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84317400"
---
Para registrar um aplicativo no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativos** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *ROPC_Auth_app*.
1. Deixe os outros valores como estão e, então, selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. Em **Tipo de cliente padrão**, selecione **Sim** para tratar o aplicativo como um cliente público. Essa configuração é necessária para o fluxo ROPC.
1. Clique em **Salvar**.
1. No menu esquerdo, selecione **Manifesto** para abrir o editor de manifesto. 
1. Defina o atributo **oauth2AllowImplicitFlow** como *true*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Clique em **Salvar**.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Aplicativos (Herdado)** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *ROPC_Auth_app*.
1. Para **Cliente nativo**, selecione **Sim**.
1. Deixe os outros valores como estão e então selecione **Criar**.
1. Registre a **ID DO APLICATIVO** para uso em uma etapa posterior.