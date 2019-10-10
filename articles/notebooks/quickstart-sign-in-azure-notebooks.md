---
title: Vá até o Azure Notebooks
description: Entre no Azure Notebooks e definir uma ID de usuário para você acessar os projetos salvos e compartilhar notebooks com outras pessoas.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 0c12c8673ff61dcca62c72addcbf0d9b33c2c252
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973056"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Início Rápido: Entrar e definir uma ID de usuário

Embora você sempre possa exibir os blocos de anotações do Azure Notebooks sem precisar entrar, você deve entrar para executar os blocos de anotações, acessar projetos salvos e blocos de anotações e compartilhar seus blocos de anotações com outras pessoas.

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
