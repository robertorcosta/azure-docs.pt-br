---
title: Comece e crie um laboratório de Azure Lab Services da equipe
description: Saiba como começar e criar um laboratório de Azure Lab Services das equipes.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946590"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Comece e crie um laboratório de serviços de laboratório das equipes

Este artigo mostra como adicionar um aplicativo Azure Lab Services às equipes. Em seguida, como criar um laboratório de equipes.

## <a name="add-a-lab-services-app-to-teams"></a>Adicionar um aplicativo de serviços de laboratório às equipes

Você pode adicionar serviços de laboratório diretamente em seus canais de equipes e, em seguida, o aplicativo está disponível para todos na equipe usarem. Execute estas três etapas:

1. Navegue até o canal de equipes em que você deseja adicionar o aplicativo e selecione **+** para adicionar uma guia clicando em "..." na parte superior da janela direita. 
1. Procure **Azure Lab Services** nas opções da guia e adicione este aplicativo. 

    > [!NOTE]
    > Somente os **proprietários** da equipe poderão criar laboratórios para a equipe.
1. Selecione uma conta de serviços de laboratório, que você gostaria de usar para criar laboratórios de sala de aula nesta equipe. 

    O Azure Lab Services usa o logon único no [site do Azure Lab Services](https://labs.azure.com) e efetua pull de todas as contas de laboratório às quais você tem acesso. 

    As contas que estão no mesmo locatário das equipes e para as quais você tem acesso de **proprietário**, **colaborador**ou **criador** são exibidas. 

   ![Bem-vindo ao ALS](./media/integrate-with-teams/welcome.png) 
1. Pressione **salvar** e o aplicativo será adicionado às equipes e a guia será adicionada ao canal. 

    Agora você pode selecionar a guia **Azure Lab Services** do seu canal e começar a gerenciar laboratórios, conforme descrito na etapa a seguir.

    Depois que um membro de uma equipe adiciona a guia, ela aparece para todos no canal. Quaisquer usuários que têm acesso ao aplicativo obtêm acesso de logon único com as credenciais que usam para o Microsoft Teams. Quaisquer usuários que não tenham acesso ao aplicativo verão a guia no Teams, mas serão bloqueados até que você conceda a permissão para o aplicativo local e a versão do aplicativo publicada no Portal do Azure.

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

Depois que a conta do laboratório for selecionada, os proprietários da equipe poderão criar laboratórios para a equipe. Todo o processo de criação de laboratório e todas as tarefas no nível de laboratório podem ser executados dentro das equipes. Os usuários terão a opção de criar vários laboratórios dentro da mesma equipe e o proprietário da equipe, com acesso apropriado no nível da conta do laboratório, verão apenas os laboratórios associados à equipe específica.

## <a name="giving-access-to-users-of-the-lab-account"></a>Fornecendo acesso a usuários da conta do laboratório

O provisionamento de acesso a usuários no nível da conta do laboratório precisa ocorrer no portal [do Azure](https://ms.portal.azure.com/) .

1. Na portal do Azure, navegue até sua conta do Azure Lab Services. 
1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** , selecione **+ Adicionar** na barra de ferramentas e, em seguida, selecione **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de Acesso -> botão Adicionar atribuição de função](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar criador de laboratório](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>Criando laboratórios de sala de aula

O processo de criação de laboratórios de sala de aula é o mesmo se você estiver criando laboratórios de equipes ou do [site de serviços de laboratório](https://labs.azure.com). 

Para obter detalhes sobre como configurar este artigo, contorno do processo de criação de laboratórios: [gerenciar laboratórios da sala de aula em Azure Lab Services](how-to-manage-classroom-labs.md).

## <a name="deleting-classroom-labs"></a>Excluindo laboratórios de sala de aula

Um laboratório criado dentro das equipes pode ser excluído no [site do Lab Services](https://labs.azure.com) , excluindo o laboratório diretamente, conforme descrito em [gerenciar laboratórios de sala de aula no Azure Lab Services](how-to-manage-classroom-labs.md). 

A exclusão de laboratório também é disparada quando a equipe é excluída. Se a equipe na qual o laboratório foi criado for excluída, o laboratório será excluído automaticamente 24 horas depois que a sincronização da lista de usuários automática for disparada. 

A exclusão da guia ou a desinstalação do aplicativo não resultará na exclusão do laboratório. Se a guia for excluída, os usuários na lista de membros da equipe ainda poderão acessar as VMs no [site do Lab Services](https://labs.azure.com) , a menos que a exclusão do laboratório seja disparada explicitamente excluindo o laboratório no site ou excluindo a equipe. 

## <a name="next-steps"></a>Próximas etapas

Quando um laboratório é criado dentro das equipes, a lista de usuários do laboratório é automaticamente populada e sincronizada com a associação da equipe. Todos na equipe, incluindo proprietários, membros e convidados, serão adicionados automaticamente à lista de usuários do laboratório. Os serviços de laboratório do Azure manterão uma sincronização com a associação da equipe e uma sincronização automática será disparada a cada 24 horas. Para obter detalhes, confira:

[Gerenciar listas de usuários do Lab Services de equipes](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Veja também

Consulte também os seguintes artigos:

- [Visão geral do uso de Azure Lab Services nas equipes](lab-services-within-teams-overview.md)
- [Gerenciar um pool de VMs no Lab Services das equipes](how-to-manage-vm-pool-within-teams.md)
- [Criar agendas de serviços de laboratório de equipes](how-to-create-schedules-within-teams.md)
- [Acessar uma VM (exibição de aluno) no Lab Services das equipes](how-to-access-vm-for-students-within-teams.md)

