---
title: Configure configurações de uso em laboratórios de sala de aula do Azure Lab Services
description: Aprenda a configurar o número de alunos para um laboratório, registre-os no laboratório, controle o número de horas que eles podem usar a VM e muito mais.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159448"
---
# <a name="add-and-manage-lab-users"></a>Adicionar e gerenciar os usuários do laboratório

Este artigo descreve como adicionar usuários de alunos a um laboratório, registrá-los no laboratório, controlar o número de horas adicionais que podem usar a máquina virtual (VM) e muito mais. 

## <a name="add-users-to-a-lab"></a>Adicionar usuários a um laboratório

Nesta seção, você adiciona alunos a um laboratório manualmente ou carregando um arquivo CSV. Faça o seguinte:

1. No painel esquerdo, selecione **Usuários**. 

    Por padrão, a opção **restringir o acesso** é ligada e, a menos que estejam na lista de usuários, os alunos não podem se registrar no laboratório mesmo que tenham um link de inscrição. Apenas usuários listados podem se registrar no laboratório usando o link de registro que você envia. Neste procedimento, você pode adicionar usuários à lista. Alternativamente, você pode desativar **o acesso restrito,** o que permite que os alunos se inscrevam no laboratório desde que tenham o link de inscrição. 

1. Na parte superior do painel **Usuários,** selecione **Adicionar usuários**e, em seguida, **selecione Adicionar por endereço de e-mail**. 

    ![O botão "Adicionar usuários"](../media/how-to-configure-student-usage/add-users-button.png)

1. No painel **Adicionar usuários,** digite os endereços de e-mail dos alunos em linhas separadas ou em uma única linha separada por ponto e vírgula. 

    ![Adicionar endereços de e-mail dos usuários](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Selecione **Salvar**. 

    A lista exibe os endereços de e-mail e os status dos usuários atuais, estejam registrados no laboratório ou não. 

    ![Lista de usuários](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Depois que os alunos são registrados no laboratório, a lista exibe seus nomes. O nome mostrado na lista é construído usando o primeiro e último nome dos alunos no Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Adicionar usuários ao carregar um arquivo CSV

Você também pode adicionar usuários carregando um arquivo CSV que contém seus endereços de e-mail.

1. No Microsoft Excel, crie um arquivo CSV que liste os endereços de e-mail dos alunos em uma coluna.

    ![Lista de usuários em um arquivo CSV](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. Na parte superior do painel **Usuários,** selecione **Adicionar usuários**e, em seguida, selecione **Upload CSV**.

    ![O botão "Carregar CSV"](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Selecione o arquivo CSV que contém os endereços de e-mail dos alunos e, em seguida, selecione **Abrir**.

    A **janela Adicionar usuários** exibe a lista de endereços de e-mail do arquivo CSV. 

    ![A janela "Adicionar usuários" com endereços de e-mail do arquivo CSV](../media/how-to-configure-student-usage/add-users-window.png)

1. Selecione **Salvar**. 

1. No painel **Usuários,** veja a lista de alunos adicionados. 

    ![Lista de usuários adicionados no painel "Usuários"](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Enviar convites aos usuários

Para enviar um link de registro para novos usuários, use um dos seguintes métodos. 

Se a opção **restringir o acesso** estiver habilitada para o laboratório, apenas os usuários listados poderão usar o link de registro para se registrar no laboratório. Essa opção é habilitada por padrão. 

### <a name="invite-all-users"></a>Convide todos os usuários

Este método mostra como enviar e-mail com um link de inscrição e uma mensagem opcional para todos os alunos listados.

1. No painel **Usuários,** selecione **Convidar todos**. 

    ![O botão "Convidar todos"](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Na janela **Enviar convite por e-mail,** digite uma mensagem opcional e, em seguida, selecione **Enviar**. 

    O email inclui automaticamente o link de registro. Para obter e salvar o link de registro separadamente, selecione a elipse (**...**) na parte superior do painel **Usuários** e, em seguida, selecione o **link Registro**. 

    ![A janela "Enviar link de inscrição por e-mail"](../media/tutorial-setup-classroom-lab/send-email.png)

    A coluna **Convite** da lista **Usuários** exibe o status do convite para cada usuário adicionado. O status deve mudar para **Envio** e, em seguida, para **Enviado na \<data>**. 

### <a name="invite-selected-users"></a>Convidar usuários selecionados

Este método mostra como convidar apenas determinados alunos e obter um link de inscrição que você pode compartilhar com outras pessoas.

1. No painel **Usuários,** selecione um aluno ou vários alunos na lista. 

1. Na linha para o aluno selecionado, selecione o ícone do **envelope** ou, na barra de ferramentas, **selecione Convidar**. 

    ![Convidar usuários selecionados](../media/how-to-configure-student-usage/invite-selected-users.png)

1. Na janela **Enviar convite por e-mail,** digite uma **mensagem**opcional e selecione **Enviar**. 

    ![Envie e-mail para usuários selecionados](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    O painel **Usuários** exibe o status desta operação na coluna **Convite** da tabela. O e-mail de convite inclui o link de inscrição que os alunos podem usar para se inscrever no laboratório.

## <a name="get-the-registration-link"></a>Obtenha o link de inscrição

Nesta seção, você pode obter o link de inscrição do portal e enviá-lo usando seu próprio aplicativo de e-mail. 

1. No painel **Usuários,** selecione **O link Cadastrar**.

    ![Link de registro do aluno](../media/how-to-configure-student-usage/registration-link-button.png)

1. Na janela de registro do **usuário,** selecione **Copiar**e selecione **'Feito'.** 

    ![A janela "Registro de usuário"](../media/how-to-configure-student-usage/registration-link.png)

    O link é copiado para a área de transferência. 
    
1. No seu aplicativo de e-mail, cole o link de inscrição e envie o e-mail para um aluno para que o aluno possa se inscrever para a aula. 

## <a name="view-registered-users"></a>Exibir usuários registrados

1. Acesse o site do [Azure Lab Services.](https://labs.azure.com) 
1. Selecione **Entrar**e digite suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft.
1. Na página **Meus laboratórios,** selecione o laboratório cujo uso você deseja rastrear. 
1. No painel esquerdo, selecione **Usuários**ou selecione o azulejo **Usuários.** 

    O painel **Usuários** exibe uma lista de alunos que se registraram em seu laboratório.  

    ![Lista de usuários registrados](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Definir cotas para usuários

Você pode definir uma cota de horas para cada aluno fazendo o seguinte: 

1. No painel **Usuários,** selecione **Cota \<por usuário: número> hora** na barra de ferramentas. 
1. Na **janela Cota por usuário,** especifique o número de horas que deseja dar a cada aluno fora do horário de aula programado e selecione **Salvar**.

    ![A janela "Cota por usuário"](../media/how-to-configure-student-usage/quota-per-user.png)    

    Os valores alterados agora são exibidos na **Cota por usuário: \<número de horas>** botão na barra de ferramentas e na lista de usuários, conforme mostrado aqui:

    ![Horas de cota por usuário](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > O [tempo de funcionamento programado das VMs](how-to-create-schedules.md) não conta com a cota que é atribuída a um aluno. A cota é para o tempo fora do horário programado que um aluno gasta em VMs. 

## <a name="set-additional-quotas-for-specific-users"></a>Defina cotas adicionais para usuários específicos

Você pode especificar cotas para certos alunos além das cotas comuns que foram definidas para todos os usuários na seção anterior. Por exemplo, se você, como instrutor, definir a cota para todos os alunos para 10 horas e definir uma cota adicional de 5 horas para um aluno específico, esse aluno recebe 15 (10 + 5) horas de cota. Se você mudar a cota comum mais tarde para, digamos, 15, o aluno recebe 20 (15 + 5) horas de cota. Lembre-se que esta cota geral está fora do horário programado. O tempo que um aluno passa em um laboratório de VM durante o horário programado não conta com essa cota. 

Para definir cotas adicionais, faça o seguinte:

1. No painel **Usuários,** selecione um aluno da lista e selecione **Ajustar cota** na barra de ferramentas. 

    ![O botão "Ajustar cota"](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. Na **cota Ajustar \<para o endereço de e-mail de usuários ou usuários selecionados>, **digite o número de horas adicionais de laboratório que deseja conceder ao aluno ou aluno selecionado e, em seguida, selecione **Aplicar**. 

    ![O "Ajustar cota..." Janela](../media/how-to-configure-student-usage/additional-quota.png)

    A coluna **Uso** exibe a cota atualizada para os alunos selecionados. 

    ![Novo uso para o usuário](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Contas estudantis

Para adicionar alunos a um laboratório de sala de aula, você usa suas contas de e-mail. Os alunos podem ter os seguintes tipos de contas de e-mail:

- Uma conta de e-mail de estudante fornecida pelo azure Active Directory da sua universidade para o Office 365. 
- Uma conta de e-mail de domínio da Microsoft, como *outlook.com,* *hotmail.com,* *msn.com*ou *live.com.*
- Uma conta de e-mail não-Microsoft, como uma fornecida pelo Yahoo! ou Google. No entanto, esses tipos de contas devem ser vinculadas a uma conta da Microsoft.
- Uma conta do GitHub. Esta conta deve ser vinculada a uma conta da Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Use uma conta de e-mail não-Microsoft
Os alunos podem usar contas de e-mail não-Microsoft para se registrar e fazer login em um laboratório de sala de aula.  No entanto, o registro exige que eles primeiro criem uma conta microsoft vinculada ao seu endereço de e-mail não-Microsoft.

Muitos alunos já podem ter uma conta microsoft vinculada ao seu endereço de e-mail não-Microsoft. Por exemplo, os alunos já têm uma conta microsoft se usaram seu endereço de e-mail com outros produtos ou serviços da Microsoft, como Office, Skype, OneDrive ou Windows.  

Quando os alunos usam o link de inscrição para entrar em uma sala de aula, eles são solicitados para seu endereço de e-mail e senha. Os alunos que tentarem fazer login com uma conta não-Microsoft que não esteja vinculada a uma conta da Microsoft receberão a seguinte mensagem de erro: 

![Mensagem de erro no login](../media/how-to-configure-student-usage/cant-find-account.png)

Aqui está um link para os alunos [se inscreverem em uma conta da Microsoft.](http://signup.live.com)  

> [!IMPORTANT]
> Quando os alunos entram em um laboratório de sala de aula, eles não têm a opção de criar uma conta Microsoft. Por essa razão, recomendamos que você inclua http://signup.live.comeste link de inscrição, no e-mail de registro do laboratório de sala de aula que você envia para alunos que estão usando contas não-Microsoft.

### <a name="use-a-github-account"></a>Use uma conta do GitHub
Os alunos também podem usar uma conta gitHub existente para se registrar e fazer login em um laboratório de sala de aula. Se eles já tiverem uma conta microsoft vinculada à sua conta do GitHub, os alunos podem fazer login e fornecer sua senha, conforme mostrado na seção anterior. 

Se eles ainda não vincularam sua conta do GitHub a uma conta da Microsoft, eles podem fazer o seguinte:

1. Selecione o link **de opções de login,** conforme mostrado aqui:

    ![O link "Opções de login"](../media/how-to-configure-student-usage/signin-options.png)

1. Na janela **Opções de login,** selecione **Entrar com o GitHub**.

    ![O link "Entrar com o GitHub"](../media/how-to-configure-student-usage/signin-github.png)

    No prompt, os alunos criam uma conta microsoft vinculada à sua conta do GitHub. A vinculação acontece automaticamente quando eles selecionam **Next**. Eles são imediatamente inscritos e conectados ao laboratório da sala de aula.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Exportar uma lista de usuários para um arquivo CSV

1. Vá para o painel **Usuários.**
1. Na barra de ferramentas, selecione a elipse (**...**), e, em seguida, selecione **Exportar CSV**. 

    ![O botão "Exportar CSV"](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- Para administradores: [Crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- Para proprietários de laboratórios: [Crie e gerencie laboratórios](how-to-manage-classroom-labs.md) e [configure e publique modelos](how-to-create-manage-template.md)
- Para usuários de laboratório: [Acesse laboratórios de sala de aula](how-to-use-classroom-lab.md)
