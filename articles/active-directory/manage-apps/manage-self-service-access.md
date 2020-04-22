---
title: Como configurar a atribuição de aplicativo de autoatendimento | Microsoft Docs
description: Habilite o acesso do aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos
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
ms.topic: article
ms.date: 04/20/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e09fd63ee6121ac9bf7f3c2be00f0ac22f752f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731703"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Como configurar a atribuição de aplicativo de autoatendimento

Antes que seus usuários possam se autodescobrir aplicativos a partir de seu painel de acesso My Apps, você precisa habilitar o acesso de **aplicativos de autoatendimento** a quaisquer aplicativos aos quais você deseja permitir que os usuários se autodescubram e solicitem acesso. Esta funcionalidade está disponível para aplicativos que foram adicionados da [Azure AD Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ou foram adicionados via consentimento do usuário ou [administrador](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience). 

Esse recurso é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI e é altamente recomendável como parte de uma implantação de aplicativos moderna com o Azure Active Directory.

Usando esse recurso, você pode:

-   Deixe os usuários se auto-descobrirem aplicativos do painel de [acesso Meus Aplicativos](https://myapps.microsoft.com/) sem incomodar o grupo de TI.

-   Adicione esses usuários a um grupo pré-configurado para que você possa ver quem solicitou acesso, remover o acesso e gerenciar as funções atribuídas a eles.

-   Ou permita que um aprovador de negócios aprove solicitações de acesso ao aplicativo para que o grupo de TI não precise fazer isso.

-   Também é possível configurar até 10 pessoas que podem aprovar o acesso a esse aplicativo.

-   Opcionalmente, permita que um aprovador de negócios defina as senhas que esses usuários podem usar para entrar no aplicativo, diretamente no [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) do aprovador de negócios.

-   É possível, ainda, atribuir automaticamente o autoatendimento atribuído aos usuários diretamente a uma função de aplicativo.

> [!NOTE]
> Uma licença Azure Active Directory Premium (P1 ou P2) é necessária para que os usuários solicitem a adesão a um aplicativo de autoatendimento e que os proprietários aprovem ou neguem solicitações. Sem uma licença Premium do Azure Active Directory, os usuários não podem adicionar aplicativos de autoatendimento.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Habilite o acesso do aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos

O acesso a aplicativos de autoatendimento é uma ótima maneira de permitir que os usuários autodescubram aplicativos e, opcionalmente, permitem que o grupo de negócios aprove o acesso a esses aplicativos. Para o login único de senha nos aplicativos, você também pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários a partir de seus próprios painéis de acesso My Apps.

Para habilitar o acesso de aplicativos de autoatendimento a um aplicativo, siga as etapas abaixo:

1. Faça login no [portal Azure](https://portal.azure.com) como administrador global.

2. Selecione **O Diretório Ativo do Azure**. No menu de navegação à esquerda, selecione **aplicativos Enterprise**.

3. Selecione o aplicativo na lista. Se você não ver o aplicativo, comece a digitar seu nome na caixa de pesquisa. Ou use os controles do filtro para selecionar o tipo de aplicativo, status ou visibilidade e, em seguida, **selecione Aplicar**.

4. No menu de navegação à esquerda, selecione **Self-service**.

5. Para habilitar o acesso de aplicativos de autoatendimento a este aplicativo, coloque o controle de alternância **Permitir aos usuários solicitar acesso a esse aplicativo?** na posição **Sim.**

6. Ao lado de qual grupo deve ser **Select group**adicionado aos **usuários atribuídos?** Escolha um grupo e clique em **Selecionar**. Quando a solicitação de um usuário for aprovada, ela será adicionada a esse grupo. Ao visualizar a adesão deste grupo, você poderá ver quem teve acesso ao aplicativo por meio do acesso ao autoatendimento.
  
    > [!NOTE]
    > Essa configuração não suporta grupos sincronizados a partir de locais.

7. **Opcional:** Para exigir a aprovação dos negócios antes que os usuários tenham acesso, **Yes**defina a **aprovação de Exigir antes de conceder acesso a este aplicativo?**

8. **Opcional: Para aplicativos que usam somente o login único de senha,** para permitir que os aprovadores de negócios especifiquem as senhas enviadas para este **Yes**aplicativo para usuários aprovados, defina os **aprovadores de Permitir que definam as senhas do usuário para este aplicativo?**

9. **Opcional:** Para especificar os aprovadores de negócios que podem aprovar o acesso a este aplicativo, **Select approvers**ao lado **de Quem pode aprovar o acesso a este aplicativo?** Em seguida, clique em **Selecionar**.

    >[!NOTE]
    >Não há suporte para grupos. Você pode selecionar até 10 aprovadores individuais de negócios. Se você especificar vários aprovadores, qualquer aprovador único pode aprovar uma solicitação de acesso.

10. **Opcional:** **Para aplicativos que expõem funções,** atribuir usuários aprovados por autoatendimento a uma função, ao lado **Select Role**da função para qual função os usuários devem **ser atribuídos neste aplicativo?** Em seguida, clique em **Selecionar**.

11. Clique no botão **Salvar** na parte superior do painel para concluir.

Depois de concluir a configuração do aplicativo self-service, os usuários podem navegar até o [painel de acesso meus aplicativos](https://myapps.microsoft.com/) e clicar no botão Adicionar aplicativos de **autoatendimento** para encontrar os aplicativos habilitados com acesso a autoatendimento. Os aprovadores de negócios também veem uma notificação em seu [painel de acesso Meus Aplicativos](https://myapps.microsoft.com/). Você pode habilitar um email que notifica a eles quando um usuário solicitar acesso a um aplicativo que requer sua aprovação.

## <a name="next-steps"></a>Próximas etapas
[Configuração do Azure Active Directory para gerenciamento de grupo de autoatendimento](../users-groups-roles/groups-self-service-management.md)
