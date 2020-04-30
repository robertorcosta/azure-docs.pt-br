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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731703"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Como configurar a atribuição de aplicativo de autoatendimento

Antes que os usuários possam detectar aplicativos automaticamente do painel de acesso de meus aplicativos, você precisa habilitar o **acesso de aplicativo de autoatendimento** a todos os aplicativos para os quais deseja permitir que os usuários autodescubram e solicitem acesso. Essa funcionalidade está disponível para aplicativos que foram adicionados da [Galeria do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), o [Azure proxy de aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ou foram adicionados por meio do [consentimento do usuário ou do administrador](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience). 

Esse recurso é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI e é altamente recomendável como parte de uma implantação de aplicativos moderna com o Azure Active Directory.

Usando esse recurso, você pode:

-   Permita que os usuários autodescubram aplicativos do [painel de acesso meus aplicativos](https://myapps.microsoft.com/) sem incomodar o grupo de ti.

-   Adicione esses usuários a um grupo pré-configurado para que você possa ver quem solicitou acesso, remover o acesso e gerenciar as funções atribuídas a eles.

-   Ou permita que um aprovador de negócios aprove solicitações de acesso ao aplicativo para que o grupo de TI não precise fazer isso.

-   Também é possível configurar até 10 pessoas que podem aprovar o acesso a esse aplicativo.

-   Opcionalmente, permita que um aprovador de negócios defina as senhas que esses usuários podem usar para entrar no aplicativo, diretamente no [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) do aprovador de negócios.

-   É possível, ainda, atribuir automaticamente o autoatendimento atribuído aos usuários diretamente a uma função de aplicativo.

> [!NOTE]
> Uma licença Azure Active Directory Premium (P1 ou P2) é necessária para que os usuários solicitem a União de um aplicativo de autoatendimento e que os proprietários aprovem ou neguem solicitações. Sem uma licença de Azure Active Directory Premium, os usuários não podem adicionar aplicativos de autoatendimento.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Habilite o acesso do aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos

O acesso ao aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários autodescubram aplicativos e, opcionalmente, permitem que o grupo de negócios aprove o acesso a esses aplicativos. Para aplicativos de logon único com senha, você também pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários de seus próprios painéis de acesso dos meus aplicativos.

Para habilitar o acesso de aplicativos de autoatendimento a um aplicativo, siga as etapas abaixo:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global.

2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicativos empresariais**.

3. Selecione o aplicativo na lista. Se você não vir o aplicativo, comece a digitar seu nome na caixa de pesquisa. Ou use os controles de filtro para selecionar o tipo de aplicativo, o status ou a visibilidade e, em seguida, selecione **aplicar**.

4. No menu de navegação à esquerda, selecione **autoatendimento**.

5. Para habilitar o acesso de aplicativos de autoatendimento a este aplicativo, coloque o controle de alternância **Permitir aos usuários solicitar acesso a esse aplicativo?** na posição **Sim.**

6. Ao lado **de qual grupo os usuários atribuídos devem ser adicionados?**, clique em **selecionar grupo**. Escolha um grupo e, em seguida, clique em **selecionar**. Quando a solicitação de um usuário for aprovada, ela será adicionada a esse grupo. Ao exibir a associação deste grupo, você poderá ver quem recebeu acesso ao aplicativo por meio de acesso de autoatendimento.
  
    > [!NOTE]
    > Essa configuração não oferece suporte a grupos sincronizados do local.

7. **Opcional:** Para exigir aprovação de negócios antes que os usuários tenham permissão de acesso, defina a opção **exigir aprovação antes de conceder acesso a este aplicativo?** alterne para **Sim**.

8. **Opcional: para aplicativos que usam somente logon único de senha,** para permitir que os aprovadores de negócios especifiquem as senhas que são enviadas para este aplicativo para usuários aprovados, defina permitir que os **aprovadores definam as senhas do usuário para este aplicativo?** alterne para **Sim**.

9. **Opcional:** Para especificar os aprovadores de negócios que têm permissão para aprovar o acesso a esse aplicativo, ao lado de **quem tem permissão para aprovar o acesso a esse aplicativo?**, clique em **selecionar aprovadores**e selecione até 10 aprovadores de negócios individuais. Em seguida, clique em **Selecionar**.

    >[!NOTE]
    >Não há suporte para grupos. Você pode selecionar até 10 aprovadores de negócios individuais. Se você especificar vários Aprovadores, qualquer aprovador único poderá aprovar uma solicitação de acesso.

10. **Opcional:** **para aplicativos que expõem funções**, para atribuir usuários aprovados por autoatendimento a uma função, ao lado do **a qual função os usuários devem ser atribuídos neste aplicativo?**, clique em **selecionar função**e escolha a função à qual esses usuários devem ser atribuídos. Em seguida, clique em **Selecionar**.

11. Clique no botão **Salvar** na parte superior do painel para concluir.

Depois de concluir a configuração de aplicativo de autoatendimento, os usuários podem navegar até o [painel de acesso meus aplicativos](https://myapps.microsoft.com/) e clicar no botão **adicionar aplicativos de autoatendimento** para localizar os aplicativos que são habilitados com acesso de autoatendimento. Os aprovadores de negócios também veem uma notificação em seu [painel de acesso meus aplicativos](https://myapps.microsoft.com/). Você pode habilitar um email que notifica a eles quando um usuário solicitar acesso a um aplicativo que requer sua aprovação.

## <a name="next-steps"></a>Próximas etapas
[Configuração do Azure Active Directory para gerenciamento de grupo de autoatendimento](../users-groups-roles/groups-self-service-management.md)
