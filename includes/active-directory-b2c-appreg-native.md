---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: bdc9c8f5a782704ec7f8cbaa31dd4cc27ed376cc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468975"
---
Para registrar um aplicativo no locatário do Azure Active Directory B2C, você pode usar a experiência **Aplicativos** atual ou a nossa nova experiência **Registros de Aplicativo (versão prévia)** unificada. [Saiba mais sobre a experiência de versão prévia](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *nativeapp1*.
1. Para **Cliente nativo**, selecione **Sim**.
1. Insira um **URI de redirecionamento personalizado** com um esquema exclusivo. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Há duas considerações importantes ao escolher um URI de redirecionamento:
    * **Exclusivo**: O esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. No exemplo `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` é o esquema. Esse padrão deve ser seguido. Se dois aplicativos compartilharem o mesmo esquema, o usuário terá a opção de escolher um aplicativo. Se o usuário escolher incorretamente, o acesso falhará.
    * **Completo**: O URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra após o domínio. Por exemplo, `//oauth/` funciona e `//oauth` falha. Não inclua caracteres especiais no URI, por exemplo, sublinhados.
1. Selecione **Criar**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *nativeapp1*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional ou qualquer provedor de identidade**.
1. Em **URI de Redirecionamento**, use a lista suspensa para selecionar **Cliente Público/Nativo (móvel e área de trabalho)** .
1. Insira um URI de redirecionamento com um esquema exclusivo. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Há duas considerações importantes ao escolher um URI de redirecionamento:
    * **Exclusivo**: O esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. No exemplo `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` é o esquema. Esse padrão deve ser seguido. Se dois aplicativos compartilharem o mesmo esquema, o usuário terá a opção de escolher um aplicativo. Se o usuário escolher incorretamente, o acesso falhará.
    * **Completo**: O URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra após o domínio. Por exemplo, `//oauth/` funciona e `//oauth` falha. Não inclua caracteres especiais no URI, por exemplo, sublinhados.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.