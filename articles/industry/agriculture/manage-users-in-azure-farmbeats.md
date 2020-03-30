---
title: Gerencie usuários no Azure FarmBeats
description: Este artigo descreve como gerenciar usuários no Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502055"
---
# <a name="manage-users"></a>Gerenciar usuários

O Azure FarmBeats inclui o gerenciamento de usuários para pessoas que fazem parte da instância do Azure Active Directory (Azure AD). Você pode adicionar usuários à sua instância Azure FarmBeats para acessar as APIs, visualizar os mapas gerados e acessar a telemetria do sensor da fazenda.

## <a name="prerequisites"></a>Pré-requisitos

- A instalação do Azure FarmBeats é necessária. Para obter mais informações, consulte [Install Azure FarmBeats](install-azure-farmbeats.md).
- Os IDs de e-mail dos usuários que você deseja adicionar ou remover da sua instância Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Gerenciar usuários do Azure FarmBeats

O Azure FarmBeats usa o Azure AD para autenticação, controle de acesso e funções. Você pode adicionar usuários no inquilino Azure AD como usuários no Azure FarmBeats.

> [!NOTE]
> Se um usuário não for um usuário inquilino do Azure AD, siga as instruções na seção **Adicionar usuários ad do Azure** para concluir a configuração.

O Azure FarmBeats suporta dois tipos de funções de usuário:

 - **Administrador**: Acesso completo às APIs do Azure FarmBeats Datahub. Os usuários nesta função podem consultar todos os objetos Do Azure FarmBeats Datahub e executar todas as operações do FarmBeats Accelerator.
 - **Somente leitura**: Acesso somente à leitura das APIs do FarmBeats Datahub. Os usuários podem visualizar as APIs do Datahub, os painéis do acelerador e os mapas. Os usuários com acesso somente à leitura não podem realizar operações como gerar mapas, associar dispositivos ou criar fazendas.

## <a name="add-users-to-azure-farmbeats"></a>Adicionar usuários ao Azure FarmBeats

Para adicionar usuários ao Azure FarmBeats:

1. Faça login no Acelerador e selecione o ícone **Configurações.**
2. Selecione **controle de acesso**.

    ![O painel de configurações das fazendas](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Digite o ID de e-mail do usuário ao que deseja conceder acesso.
4. Selecione a função **desejada, Admin** ou **Read-Only**.
5. Selecione **Adicionar função**.

O usuário adicionado agora pode acessar o Azure FarmBeats (tanto datahub quanto accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Excluir usuários do Azure FarmBeats

Para remover usuários do sistema Azure FarmBeats:

1. Faça login no Acelerador e selecione o ícone **Configurações.**
2. Selecione **controle de acesso**.
3. Selecione **Excluir**.

   O usuário é excluído do sistema. Você receberá a seguinte mensagem de confirmação:

   ![Mensagem de confirmação do Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Adicionar usuários do Microsoft Azure Active Directory

> [!NOTE]
> Os usuários do Azure FarmBeats precisam existir no inquilino Azure AD antes de atribuí-los a aplicativos e funções. Se um usuário não existir no inquilino Azure AD, siga as instruções nesta seção. Pule as instruções, se um usuário já existir no inquilino Azure AD.

Siga as etapas para adicionar usuários ao Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com/).
2. No canto superior direito, selecione sua conta e mude para o inquilino Azure AD associado ao FarmBeats.
3. Selecione usuários de diretório ativo do >  **Azure****.**

    Uma lista de usuários do Azure AD é exibida.

4. Para adicionar um usuário ao diretório, selecione **Novo usuário**. Para adicionar um usuário externo, selecione **Novo usuário convidado**.

    ![O painel "Todos os usuários"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Selecione o nome do novo usuário e, em seguida, complete os campos necessários para esse usuário.
6. Selecione **Criar**.

Para obter informações sobre o gerenciamento de usuários do Azure AD, consulte [Adicionar ou excluir usuários no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Próximas etapas

Você adicionou com sucesso os usuários à sua instância Azure FarmBeats. Agora, aprenda a [criar e gerenciar fazendas.](manage-farms-in-azure-farmbeats.md#create-farms)
