---
title: Como atribuir usuários e grupos a um aplicativo | Microsoft Docs
description: Atribua usuários ao aplicativo para conceder acesso
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd6b13dc56f8f948d50e2b3564712ed8f5b1476
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376441"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Atribuir usuários e grupos a um aplicativo no Azure Active Directory
Este artigo mostra como atribuir usuários ou grupos a um aplicativo no Azure AD (Azure Active Directory). Primeiro os usuários devem ser atribuídos a um aplicativo para que um administrador possa conceder a eles acesso para fazer o seguinte:

-   Acessar um aplicativo **navegando diretamente para a URL do aplicativo** (também conhecido como logon iniciado pelo provedor de serviços).

-   Acessar um aplicativo usando a **URL de Acesso do Usuário** na página **Propriedades** de um aplicativo (também conhecido como logon iniciado pelo provedor de identidade).

-   Ver um aplicativo em seu [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) ou aplicativo móvel.

-   Ver um aplicativo em seu [Inicializador de Aplicativos do Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

A disponibilidade da atribuição baseada em grupo é determinada pelo seu contrato de licença. A atribuição baseada em grupo tem suporte apenas para grupos de segurança. Atualmente, as associações de grupo aninhadas e os grupos O365 não têm suporte.

## <a name="configure-the-application-to-require-assignment"></a>Configurar o aplicativo para exigir atribuição

Um aplicativo pode ser configurado para exigir atribuição antes que possa ser acessado. Para exigir atribuição:

1. Faça logon no portal do Azure com uma conta de administrador ou como um proprietário do aplicativo em **aplicativos empresariais**.
2. Clique no item **Todos os serviços** no menu principal.
3. Escolha o diretório que você está usando para o aplicativo.
4. Clique na guia **Aplicativos empresariais**.
5. Selecione o aplicativo na lista de aplicativos associada ao diretório.
6. Clique no guia **Propriedades**.
7. Altere a opção **Atribuição de usuário necessária?** para Sim.
8. Clique no botão **Salvar** na parte superior da tela.

## <a name="assign-users"></a>Atribuir usuários

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global ou como um proprietário de aplicativo não administrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

    * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7.  Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8.  Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9.  Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um curto período de tempo, os usuários selecionados poderão iniciar esses aplicativos usando os métodos descritos na seção Descrição da solução.

## <a name="assign-groups"></a>Atribuir grupos

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou como um proprietário de aplicativo não administrador com uma licença de Azure ad Premium atribuída.

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

    * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7.  Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8.  Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9.  Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo do grupo** que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **grupo** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção próxima ao logotipo ou ao perfil do grupo para adicionar o usuário na lista **Selecionado**.

12. **Opcional:** caso queira **adicionar mais de um grupo**, digite outro **nome de grupo completo** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse grupo à lista **Selecionado**.

13. Ao concluir a seleção dos grupos, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos grupos selecionados.

Após um curto período de tempo, os usuários dentro dos grupos que você selecionou poderão iniciar esses aplicativos usando os métodos descritos na seção Descrição da solução. Se esses grupos forem dinâmicos, pode haver algum atraso de processamento adicional nessas atribuições que aparecem para os usuários nesses grupos atribuídos.

## <a name="enable-self-service-application-access"></a>Habilitar acesso a aplicativo de autoatendimento

O acesso de aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários descubram aplicativos por conta própria e, ainda, permitir que o grupo de negócios aprove o acesso a esses aplicativos. Você pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários para Aplicativos de logon único com senha, diretamente de seus painéis de acesso.

Para habilitar o acesso de aplicativos de autoatendimento a um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

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

    >[!NOTE]
    >Não há suporte para grupos.
    >
    >

13. **Opcional:** **para aplicativos que expõem funções**, se você quiser atribuir usuários aprovados por autoatendimento a uma função, clique no seletor ao lado do **a qual função os usuários devem ser atribuídos neste aplicativo?** para selecionar a função à qual esses usuários devem ser atribuídos.

14. Clique no botão **Salvar** na parte superior do painel para concluir.

Após você concluir a configuração do aplicativo de autoatendimento, os usuários poderão navegar para seu [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) e clicar no botão **+Adicionar** para encontrar os aplicativos aos quais você habilitou o acesso de autoatendimento. Aprovadores de negócios também recebem uma notificação em seu [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/). Você pode habilitar um email que notifica a eles quando um usuário solicitar acesso a um aplicativo que requer sua aprovação. 

Essas aprovações dão suporte a fluxos de trabalho de aprovação únicos, o que significa que, se você especificar vários aprovadores, qualquer aprovador individual poderá aprovar o acesso ao aplicativo.

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
