---
title: Adicione um usuário como criador de laboratório no Azure Lab Services
description: Este artigo mostra como adicionar um usuário à função de Criador do Laboratório para uma conta de laboratório no Azure Lab Services. Os criadores do laboratório podem criar laboratórios dentro desta conta de laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444765"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Adicione criadores de laboratório a uma conta de laboratório no Azure Lab Services
Este artigo mostra como adicionar usuários como criadores de laboratório a uma conta de laboratório no Azure Lab Services. Esses usos, então, podem criar laboratórios de sala de aula na conta do laboratório. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Adicione a conta de usuário da Microsoft à função de Criador do Laboratório
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso, poderá pular esta etapa. Para usar outra conta de usuário e criar um laboratório de curso, siga estas etapas: 

Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de **Criador de Laboratório**:

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** e clique em **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de Acesso -> botão Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se você estiver adicionando um usuário de conta não-Microsoft como um criador de laboratório, consulte o [Adicionar um usuário de conta não-Microsoft como uma](#add-a-non-microsoft-account-user-as-a-lab-creator) seção de criador de laboratório. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Adicione um usuário de conta não-Microsoft como um criador de laboratório
Para adicionar um usuário como criador de laboratório, você usa suas contas de e-mail. Os seguintes tipos de contas de e-mail podem ser usados:

- Uma conta de e-mail fornecida pelo Office 365 Azure Active Directory (AAD) da sua universidade. 
- Uma conta de e-mail `@msn.com`da `@live.com`Microsoft, como, `@outlook.com` `@hotmail.com`, ou .
- Uma conta de e-mail não-Microsoft, como uma fornecida pelo Yahoo ou Google. No entanto, esses tipos de contas devem ser vinculadas a uma conta da Microsoft.
- Uma conta do GitHub. Esta conta deve ser vinculada a uma conta da Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Usando uma conta de e-mail não-Microsoft
Os criadores/instrutores do laboratório podem usar contas de e-mail não-Microsoft para se registrar e fazer login em um laboratório de sala de aula.  No entanto, o login no portal de Serviços de Laboratório exige que os instrutores primeiro criem uma conta microsoft vinculada ao seu endereço de e-mail não-Microsoft.

Muitos instrutores podem já ter uma conta microsoft vinculada aos seus endereços de e-mail não-Microsoft. Por exemplo, os instrutores já têm uma conta microsoft se usaram seu endereço de e-mail com outros produtos ou serviços da Microsoft, como Office, Skype, OneDrive ou Windows.  

Quando os instrutores entram no portal de Serviços de Laboratório, eles são solicitados para seu endereço de e-mail e senha. Se o instrutor tentar fazer login com uma conta não-Microsoft que não tenha uma conta Microsoft vinculada, o instrutor receberá a seguinte mensagem de erro: 

![Mensagem de erro](../media/how-to-configure-student-usage/cant-find-account.png)

Para se inscrever em uma conta da [http://signup.live.com](http://signup.live.com)Microsoft, os instrutores devem ir para .  


### <a name="using-a-github-account"></a>Usando uma conta do GitHub
Os instrutores também podem usar uma conta gitHub existente para se registrar e fazer login em um laboratório de sala de aula. Se o instrutor já tiver uma conta microsoft vinculada à sua conta do GitHub, então ele pode fazer login e fornecer sua senha como mostrado na seção anterior. Se eles ainda não vincularam sua conta do GitHub a uma conta da Microsoft, eles devem selecionar **opções de login**:

![Link de opções de login](../media/how-to-configure-student-usage/signin-options.png)

Na página **De opções de login,** selecione **Entrar com o GitHub**.

![Faça login com o link do GitHub](../media/how-to-configure-student-usage/signin-github.png)

Finalmente, eles são solicitados a criar uma conta microsoft vinculada à sua conta do GitHub. Acontece automaticamente quando o instrutor seleciona **Next**.  O instrutor é então imediatamente inscrito e conectado ao laboratório da sala de aula.


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
