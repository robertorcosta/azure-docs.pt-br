---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 03329252c0ed4231585d1717d9361a2aef35b36f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186975"
---
Para registrar um aplicativo no locatário do Azure Active Directory B2C, você pode usar a experiência **Aplicativos** atual ou a nossa nova experiência **Registros de Aplicativo (versão prévia)** unificada. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *ROPC_Auth_app*.
1. Para **Cliente nativo**, selecione **Sim**.
1. Deixe os outros valores como estão e, em seguida, selecione **criar**.
1. Registre a **ID DO APLICATIVO** para uso em uma etapa posterior.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *ROPC_Auth_app*.
1. Deixe os outros valores como estão e, em seguida, selecione **registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. Em **tipo de cliente padrão**, selecione **Sim** para tratar o aplicativo como um cliente público. Essa configuração é necessária para o fluxo ROPC.
1. Clique em **Salvar**.