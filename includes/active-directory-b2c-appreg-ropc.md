---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: cea3245176e6c38137d68e3ad4b47477bedc78be
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529174"
---
Para registrar um aplicativo no locatário do Azure Active Directory B2C, você pode usar a experiência **Aplicativos** atual ou a nossa nova experiência **Registros de Aplicativo (versão prévia)** unificada. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Aplicativos**e selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *ROPC_Auth_app.*
1. Para **Cliente nativo**, selecione **Sim**.
1. Deixe os outros valores como eles são e, em seguida, selecione **Criar**.
1. Registre a **ID DO APLICATIVO** para uso em uma etapa posterior.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *ROPC_Auth_app.*
1. Deixe os outros valores como estão e, em seguida, selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. No **tipo cliente Padrão,** selecione **Sim** para tratar o aplicativo como um cliente público. Esta configuração é necessária para o fluxo DE ROPC.
1. Clique em **Salvar**.
1. No menu à esquerda, selecione **Manifest** para abrir o editor do manifesto. 
1. Defina o atributo **oaut2AllowImplicitFlow** como *verdadeiro:*
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Clique em **Salvar**.