---
title: Entrar na versão prévia do Azure Notebooks
description: Entre rapidamente na versão prévia do Azure Notebooks e defina uma ID de usuário, que oferece a capacidade de acessar os projetos salvos e compartilhar notebooks com outras pessoas.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: b6572a7c0b965b2b72916db577b47eff4f1921c0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647009"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Início Rápido: Entrar e definir uma ID de usuário da versão prévia do Azure Notebooks

Embora você sempre possa exibir os blocos de anotações do Azure Notebooks sem precisar entrar, você deve entrar para executar os blocos de anotações, acessar projetos salvos e blocos de anotações e compartilhar seus blocos de anotações com outras pessoas.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="sign-in"></a>Entrar

1. Selecione **Entrar** na parte superior direita de [notebooks.azure.com](https://notebooks.azure.com/).

    ![Local do comando de entrada no Azure Notebooks](media/accounts/sign-in-command.png)

1. Quando solicitado, insira o endereço de email de uma Conta da Microsoft ou uma conta corporativa ou escolar e selecione **Próximo**. Os tipos de conta são descritos em [Sua conta de usuário para o Azure Notebooks](azure-notebooks-user-account.md). Se você não tiver uma Conta da Microsoft ou deseja fazer um para ser usada com blocos de anotações do Azure, selecione **Criar uma**:

    ![Criar novo comando de conta da Microsoft no prompt de entrada](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Se você tentar criar uma conta com um endereço de email que já tem uma conta associada a ele, poderá ver a mensagem "Não é possível inscrever-se aqui com um endereço corporativo ou de estudante. Use um email pessoal, como o Gmail ou o Yahoo!, ou obtenha um novo email do Outlook." Nesse caso, tente entrar com o email de trabalho sem criar uma conta.

1. Insira a senha quando solicitado.

1. Se você estiver entrando pela primeira vez, os blocos de anotações do Azure Notebooks solicitam permissão para acessar sua conta. Escolha **Sim** para continuar:

    ![Prompt de permissões da conta](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Defina uma ID de usuário

1. Após a primeira entrada, você receberá uma ID de usuário temporário, como "anon-idrca3". Sempre que você tiver uma ID de usuário que comece com “anon”, o Azure Notebooks solicita que você crie uma ID própria. Sua ID de usuário é usada em qualquer URL que você pode obter para compartilhar seus projetos e blocos de anotações, então, escolha algo que seja exclusivo e significativo para você.

    ![Prompt para inserir uma ID de usuário para o Azure Notebooks](media/accounts/create-user-id.png)

    Se você selecionar **Não, obrigado**, o Azure Notebooks continua a solicitar a você uma ID de usuário sempre que você entrar. Sua ID de usuário também pode ser definida a qualquer momento no seu [perfil de usuário](azure-notebooks-user-profile.md).

1. Depois de entrar com êxito, o Azure Notebooks navega para sua página de perfil público, no qual você pode selecionar **Editar Informações de Perfil** para preencher o restante das suas informações (para obter mais informações, consulte [seu perfil e a ID de usuário](azure-notebooks-user-profile.md)):

    ![Visão inicial de uma página de perfil do Azure Notebooks](media/accounts/profile-page-new.png)

> [!NOTE]
> Se você vir a mensagem "A ID de usuário já está em uso", tente usar uma ID diferente. As IDs do usuário são exclusivas entre todas as contas do Azure Notebooks, e o Azure Notebooks também reserva determinadas IDs de usuário, como nomes de marca da Microsoft.

## <a name="sign-out"></a>Sair

Para sair, selecione seu nome de usuário no canto superior direito da página, selecione **Sair**:

![Local do comando de entrada no Azure Notebooks](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Criar e compartilhar um notebook](quickstart-create-share-jupyter-notebook.md)
