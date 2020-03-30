---
title: Crie coleções para portais meus aplicativos no Azure Active Directory | Microsoft Docs
description: Use minhas coleções de aplicativos para personalizar páginas de meus aplicativos para uma experiência mais simples do Meus Aplicativos para seus usuários finais. Organize aplicativos em grupos com guias separadas.
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
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120088"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Crie coleções no portal Meus Aplicativos

Seus usuários podem usar o portal Meus Aplicativos para visualizar e iniciar os aplicativos baseados na nuvem aos que eles têm acesso. Por padrão, todos os aplicativos que um usuário pode acessar são listados juntos em uma única página. Para organizar melhor esta página para seus usuários, se você tiver uma licença Azure AD Premium P1 ou P2, você pode configurar coleções. Com uma coleção, você pode agrupar aplicativos relacionados (por exemplo, por função de trabalho, tarefa ou projeto) e exibi-los em uma guia separada. Uma coleção essencialmente aplica um filtro aos aplicativos que um usuário já pode acessar, de modo que o usuário vê apenas os aplicativos na coleção que foram atribuídos a eles.

> [!NOTE]
> Este artigo aborda como um admin pode habilitar e criar coleções. Para obter informações para o usuário final sobre como usar o portal e coleções Meus Aplicativos, consulte [Acesso e uso de coleções](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-the-latest-my-apps-features"></a>Habilite os recursos mais recentes do Meus Aplicativos

1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como administrador de usuário ou administrador global.

2. Vá para as > **configurações do usuário**do **diretório ativo do Azure**.

3. Em **visualizações de recursos do usuário,** selecione **Gerenciar configurações de visualização de recursos do usuário**.

4. Em **Usuários podem usar recursos de visualização para Meus Aplicativos,** escolha uma das seguintes opções:
   * **Selecionado** - Habilita os recursos para um grupo específico. Use a **opção Selecionar uma** opção de grupo para selecionar o grupo para o qual deseja ativar os recursos.  
   * **All** - Habilita os recursos para todos os usuários.

> [!NOTE]
> Para abrir o portal Meus Aplicativos, `https://myapps.microsoft.com` os usuários podem usar o `https://myapps.microsoft.com/contoso.com`link ou o link personalizado para sua organização, como . Depois de ativar a nova experiência do Meus Aplicativos, o banner **An updated My Applications está disponível** será exibido na parte superior da página Meus Aplicativos, e os usuários podem selecionar **Experimente** para ver a nova experiência. Para parar de usar a nova experiência, os usuários podem selecionar **Sim** no banner **Leave new experience** no topo da página.

## <a name="create-a-collection"></a>Criar uma coleção

Para criar uma coleção, você deve ter uma licença Azure AD Premium P1 ou P2.

1. Abra o [**portal Azure**](https://portal.azure.com/) e faça login como administrador com uma licença Azure AD Premium P1 ou P2.

2. Vá para **a Azure Active Directory** > **Enterprise Applications**.

3. Em **Gerenciar,** selecione **Coleções**.

4. Selecione **Nova coleção**. Na página **nova coleção,** digite um **Nome** para a coleção (recomendamos não usar "coleção" no nome. Em seguida, digite uma **descrição**.

   ![Nova página de coleção](media/acces-panel-collections/new-collection.png)

5. Selecione a guia **Aplicativos.** Selecione **+ Adicione aplicativo**e, em seguida, na página Adicionar **aplicativos,** selecione todos os aplicativos que deseja adicionar à coleção ou use a caixa **Pesquisar** para encontrar aplicativos.

   ![Adicione um aplicativo à coleção](media/acces-panel-collections/add-applications.png)

6. Quando terminar de adicionar aplicativos, selecione **Adicionar**. A lista de aplicativos selecionados é exibida. Você pode usar as setas para cima para alterar a ordem dos aplicativos na lista. Para mover um aplicativo para baixo ou excluí-lo da coleção, selecione o menu **Mais** (**...**).

7. Selecione a guia **'Proprietários'.** Selecione **+ Adicione usuários e grupos**e, em seguida, na página Adicionar usuários e **grupos,** selecione os usuários ou grupos aos seus usuários ou grupos que deseja atribuir a propriedade. Quando terminar de selecionar usuários e grupos, escolha **Selecionar**.

9. Selecione a guia **Usuários e grupos.** Selecione **+ Adicione usuários e grupos**e, em seguida, na página Adicionar usuários e **grupos,** selecione os usuários ou grupos aos os que deseja atribuir a coleção. Ou use a caixa **pesquisar** para encontrar usuários ou grupos. Quando terminar de selecionar usuários e grupos, escolha **Selecionar**.

   ![Adicionar usuários e grupos](media/acces-panel-collections/add-users-and-groups.png)

11. Selecione **Revisão + Criar**. As propriedades da nova coleção aparecem.


## <a name="view-audit-logs"></a>Exibir logs de auditoria

Os registros de auditoria registram as operações de coleções do Meus Aplicativos, incluindo ações de criação de coleções para o usuário final. Os seguintes eventos são gerados a partir de Meus Aplicativos:

* Criar coleção
* Editar coleção
* Excluir coleção
* Inicie um aplicativo (usuário final)
* Adição de aplicativos de autoatendimento (usuário final)
* Exclusão de aplicativos de autoatendimento (usuário final)

Você pode acessar registros de auditoria no [portal Do Zure](https://portal.azure.com) selecionando**os registros** de auditoria de**aplicativos corporativos** >  **do Azure Active Directory** > na seção Atividade. Para **serviço,** selecione **Meus aplicativos**.

## <a name="get-support-for-my-account-pages"></a>Obtenha suporte para páginas da Minha Conta

Na página Meus aplicativos, um usuário pode selecionar **Minha conta** > **Exibir minha conta** para abrir as configurações de sua conta. Na página Azure AD **My Account,** os usuários podem gerenciar suas informações de segurança, dispositivos, senhas e muito mais. Eles também podem acessar as configurações da conta do Office.

No caso de você precisar enviar uma solicitação de suporte para um problema com a página da conta Azure AD ou a página da conta do Office, siga estas etapas para que sua solicitação seja roteada corretamente: 

* Para problemas com a página **Azure AD "Minha conta",** abra uma solicitação de suporte dentro do portal Azure. Vá para o >  **portal Azure****Active Directory** > Nova solicitação**de suporte**.

* Para problemas com a página **"Minha conta"** do Office, abra uma solicitação de suporte dentro do centro de administração do Microsoft 365. Vá para o suporte do >  **centro de admin Microsoft 365**.**Support** 

## <a name="next-steps"></a>Próximas etapas
[Experiências do usuário final para aplicativos no Active Directory Domain Services do Azure](end-user-experiences.md)