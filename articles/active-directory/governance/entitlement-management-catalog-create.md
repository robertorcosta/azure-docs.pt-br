---
title: Criar & gerenciar recursos na gestão de direitos - Azure AD
description: Saiba como criar um novo contêiner de recursos e pacotes de acesso no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbafb0c506fd34e975efbe08c75c15c8c0b3888e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128950"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Criar e gerenciar um catálogo de recursos na gestão de direitos AD do Azure

## <a name="create-a-catalog"></a>Criar um catálogo

Um catálogo é um contêiner de recursos e pacotes de acesso. Você cria um catálogo quando deseja agrupar recursos relacionados e pacotes de acesso. Quem criar o catálogo se torna o primeiro proprietário do catálogo. Um proprietário de catálogo pode adicionar proprietários adicionais de catálogo.

**Papel pré-requisito:** Administrador global, administrador de usuário ou criador do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catálogos**.

    ![Catálogos de gestão de direitos no portal Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Clique em **Novo catálogo**.

1. Digite um nome exclusivo para o catálogo e forneça uma descrição.

    Os usuários verão essas informações nos detalhes de um pacote de acesso.

1. Se você quiser que os pacotes de acesso neste catálogo estejam disponíveis para os usuários solicitarem assim que forem criados, defina **Habilitado** para **Sim**.

1. Se você quiser permitir que os usuários em diretórios externos selecionados possam solicitar pacotes de acesso neste catálogo, defina **Ativado para usuários externos** **como Sim**.

    ![Novo painel de catálogo](./media/entitlement-management-shared/new-catalog.png)

1. Clique **em Criar** para criar o catálogo.

### <a name="creating-a-catalog-programmatically"></a>Criando um catálogo programáticamente

Você também pode criar um catálogo usando o Microsoft Graph.  Um usuário em uma função apropriada com `EntitlementManagement.ReadWrite.All` um aplicativo que tenha a permissão delegada pode ligar para a API para [criar um accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Adicionar recursos a um catálogo

Para incluir recursos em um pacote de acesso, os recursos devem existir em um catálogo. Os tipos de recursos que você pode adicionar são grupos, aplicativos e sites SharePoint Online. Os grupos podem ser grupos de 365 grupos do Office 365 criados na nuvem ou grupos de segurança Azure AD criados na nuvem. Os aplicativos podem ser aplicativos corporativos Azure AD, incluindo aplicativos SaaS e seus próprios aplicativos federados para o Azure AD. Os sites podem ser sites do SharePoint Online ou coleções de sites do SharePoint Online.

**Papel pré-requisito:** Consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catálogos** e abra o catálogo ao que deseja adicionar recursos.

1. No menu à esquerda, clique em **Recursos**.

1. Clique **em Adicionar recursos**.

1. Clique em um tipo de recurso: **Grupos e Equipes,** **Aplicativos**ou **sites SharePoint.**

    Se você não ver um recurso que deseja adicionar ou não puder adicionar um recurso, certifique-se de ter a função de diretório AD do Azure e a função de gerenciamento de direitos necessárias. Você pode precisar ter alguém com as funções necessárias para adicionar o recurso ao seu catálogo. Para obter mais informações, consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Selecione um ou mais recursos do tipo que você gostaria de adicionar ao catálogo.

    ![Adicionar recursos a um catálogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Quando terminar, clique **em Adicionar**.

    Esses recursos agora podem ser incluídos em pacotes de acesso dentro do catálogo.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Adicionando um recurso a um catálogo programática

Você também pode adicionar um recurso a um catálogo usando o Microsoft Graph.  Um usuário em uma função apropriada, ou um catálogo e proprietário `EntitlementManagement.ReadWrite.All` de recursos, com um aplicativo que tenha a permissão delegada, pode ligar para a API para [criar um accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Remover recursos de um catálogo

Você pode remover recursos de um catálogo. Um recurso só pode ser removido de um catálogo se não estiver sendo usado em nenhum dos pacotes de acesso do catálogo.

**Papel pré-requisito:** Consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catálogos** e abra o catálogo do que deseja remover recursos.

1. No menu à esquerda, clique em **Recursos**.

1. Selecione os recursos que deseja remover.

1. Clique **em Remover** (ou clique na elipse (**...**) e, em seguida, clique em **Remover recurso**).

## <a name="add-additional-catalog-owners"></a>Adicionar proprietários adicionais de catálogo

O usuário que criou um catálogo se torna o primeiro proprietário do catálogo. Para delegar o gerenciamento de um catálogo, você adiciona os usuários à função de proprietário do catálogo. Isso ajuda a compartilhar as responsabilidades de gerenciamento do catálogo. 

Siga estas etapas para atribuir um usuário à função de proprietário do catálogo:

**Papel pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catálogos** e abra o catálogo ao que deseja adicionar administradores.

1. No menu à esquerda, clique **em Funções e administradores**.

    ![Catálogos de funções e administradores](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Clique **em Adicionar proprietários** para selecionar os membros para essas funções.

1. Clique **em Selecionar** para adicionar esses membros.

## <a name="edit-a-catalog"></a>Editar um catálogo

Você pode editar o nome e a descrição de um catálogo. Os usuários veem essas informações nos detalhes de um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catálogos** e abra o catálogo que deseja editar.

1. Na página **Visão Geral** do catálogo, clique em **Editar**.

1. Edite o nome, a descrição ou as configurações habilitadas do catálogo.

    ![Editar configurações de catálogo](./media/entitlement-management-shared/catalog-edit.png)

1. Clique em **Salvar**.

## <a name="delete-a-catalog"></a>Excluir um catálogo

Você pode excluir um catálogo, mas apenas se ele não tiver nenhum pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catálogos** e abra o catálogo que deseja excluir.

1. Na **visão geral**do catálogo, clique em **Excluir**.

1. Na caixa de mensagem que é exibida, clique em **Sim**.

### <a name="deleting-a-catalog-programmatically"></a>Excluindo um catálogo programáticamente

Você também pode excluir um catálogo usando o Microsoft Graph.  Um usuário em uma função apropriada com `EntitlementManagement.ReadWrite.All` um aplicativo que tenha a permissão delegada pode ligar para a API para [excluir um accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Próximas etapas

- [Delegar governança de acesso para acessar gerentes de pacotes](entitlement-management-delegate-managers.md)
