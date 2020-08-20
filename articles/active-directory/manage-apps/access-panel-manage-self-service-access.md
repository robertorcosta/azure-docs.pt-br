---
title: Como usar o acesso de aplicativo de autoatendimento | Microsoft Docs
description: Habilite o acesso do aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ff3ca98c0fa5929a5e1c0ad5678222253130b6
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641782"
---
# <a name="how-to-use-self-service-application-access"></a>Como usar o acesso de aplicativo de autoatendimento

Antes que os usuários possam Autodetectar aplicativos de meus aplicativos, você precisa habilitar o **acesso de aplicativo de autoatendimento** a todos os aplicativos que você deseja permitir que os usuários autodescubram e solicitem acesso ao.

Esse recurso é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI e é altamente recomendável como parte de uma implantação de aplicativos moderna com o Azure Active Directory.

Usando esse recurso, você pode:

-   Permitir que os usuários descubram aplicativos por conta própria no [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) sem incomodar o grupo de TI.

-   Adicione esses usuários a um grupo pré-configurado para que você possa ver quem solicitou acesso, remover o acesso e gerenciar as funções atribuídas a eles.

-   Ou permita que um aprovador de negócios aprove solicitações de acesso ao aplicativo para que o grupo de TI não precise fazer isso.

-   Também é possível configurar até 10 pessoas que podem aprovar o acesso a esse aplicativo.

-   Opcionalmente, permita que um aprovador de negócios defina as senhas que esses usuários podem usar para entrar no aplicativo, diretamente no [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) do aprovador de negócios.

-   É possível, ainda, atribuir automaticamente o autoatendimento atribuído aos usuários diretamente a uma função de aplicativo.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Habilite o acesso do aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos

O acesso de aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários descubram aplicativos por conta própria e, ainda, permitir que o grupo de negócios aprove o acesso a esses aplicativos. Você pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários para Aplicativos de logon único com senha, diretamente de seus painéis de acesso.

Para habilitar o acesso de aplicativos de autoatendimento a um aplicativo, siga as etapas abaixo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione na lista o aplicativo ao qual você deseja habilitar o acesso do autoatendimento.

7. Após o carregamento do aplicativo, clique em **Autoatendimento** no menu de navegação esquerdo do aplicativo.

8. Para habilitar o acesso de aplicativos de autoatendimento a este aplicativo, coloque o controle de alternância **Permitir aos usuários solicitar acesso a esse aplicativo?** na posição **Sim.**

9. Em seguida, para selecionar o grupo ao qual os usuários que solicitam acesso a esse aplicativo devem ser adicionados, clique no seletor ao lado do rótulo **A qual grupo os usuários atribuídos devem ser adicionados?** e selecione um grupo.

10. **Opcional:** se quiser exigir uma aprovação de negócios antes que os usuários tenham permissão de acesso, coloque o controle de alternância **Exigir aprovação antes de conceder acesso a esse aplicativo?** na posição **Sim**.

11. **Opcional: somente para aplicativos que usam logon único com senha,** se quiser permitir que os aprovadores de negócios especifiquem as senhas que são enviadas para esse aplicativo aos usuários aprovados, coloque o controle de alternância **Permitir que os aprovadores definam senhas do usuário para este aplicativo?** na posição **Sim**.

12. **Opcional:** para especificar os aprovadores de negócios que têm permissão para aprovar o acesso ao aplicativo, clique no seletor ao lado do rótulo **Quem tem permissão para aprovar o acesso a esse aplicativo?** para selecionar até 10 aprovadores de negócios individuais.

    * Não há suporte para grupos.

13. **Opcional:** **para aplicativos que expõem funções**, se quiser atribuir usuários aprovados de autoatendimento a uma função, clique no seletor ao lado de **A que função os usuários devem ser atribuídos neste aplicativo?** para selecionar a função a que esses usuários devem ser atribuídos.

14. Clique no botão **Salvar** na parte superior da folha para terminar.

Após você concluir a configuração do aplicativo de autoatendimento, os usuários poderão navegar para seu [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) e clicar no botão **+Adicionar** para encontrar os aplicativos aos quais você habilitou o acesso de autoatendimento. Aprovadores de negócios também recebem uma notificação em seu [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/). Você pode habilitar um email que notifica a eles quando um usuário solicitar acesso a um aplicativo que requer sua aprovação. 

Essas aprovações dão suporte a fluxos de trabalho de aprovação únicos, o que significa que, se você especificar vários aprovadores, qualquer aprovador individual poderá aprovar o acesso ao aplicativo.

## <a name="next-steps"></a>Próximas etapas
[Configuração do Azure Active Directory para gerenciamento de grupo de autoatendimento](../users-groups-roles/groups-self-service-management.md)
