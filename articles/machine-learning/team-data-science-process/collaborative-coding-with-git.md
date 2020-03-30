---
title: Codificação colaborativa com Git – Processo de Ciência de Dados da Equipe
description: Como fazer o desenvolvimento de código de colaboração para projetos de ciência de dados usando o Git com o planejamento do Agile.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721890"
---
# <a name="collaborative-coding-with-git"></a>Codificação colaborativa com o Git

Este artigo descreve como usar o Git como a estrutura colaborativa de desenvolvimento de códigos para projetos de ciência de dados. O artigo abrange como vincular código no Azure Repos a itens de trabalho [de desenvolvimento ágil](agile-development.md) em Azure Boards, como fazer revisões de código e como criar e mesclar solicitações de puxar para alterações.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Vincule um item de trabalho a uma filial do Azure Repos 

O Azure DevOps fornece uma maneira conveniente de conectar um item de trabalho de usuário ou de tarefa do Azure Boards com um ramo de repositório do Azure Repos Git. Você pode vincular sua história ou tarefa do usuário diretamente ao código associado a ele. 

Para conectar um item de trabalho a um novo ramo, selecione as **ações** elipses (**...**) ao lado do item de trabalho, e no menu de contexto, role para e selecione **Nova filial**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

No Criar uma caixa de diálogo **de ramificação,** forneça o novo nome da filial e o repositório e ramo base Azure Repos Git. O repositório base deve estar no mesmo projeto Azure DevOps que o item de trabalho. O ramo base pode ser o ramo principal ou outro ramo existente. Selecione **Criar ramificação**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Você também pode criar uma nova filial usando o seguinte comando git bash no Windows ou Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Se você não especificar um \<nome de filial base `master`>, a nova filial será baseada em . 

Para mudar para o seu ramo de trabalho, execute o seguinte comando: 

```bash
git checkout <working branch name>
```

Depois de mudar para o ramo de trabalho, você pode começar a desenvolver artefatos de código ou documentação para completar o item de trabalho. Correr `git checkout master` muda você de `master` volta para o ramo.

É uma boa prática criar uma filial do Git para cada item de trabalho do User Story. Em seguida, para cada item de trabalho da Tarefa, você pode criar uma filial baseada no ramo User Story. Organize os ramos em uma hierarquia que corresponda à relação História-Tarefa do Usuário quando você tiver várias pessoas trabalhando em diferentes Histórias de Usuário para o mesmo projeto ou em tarefas diferentes para a mesma história do usuário. Você pode minimizar conflitos fazendo com que cada membro da equipe trabalhe em um ramo diferente, ou em códigos diferentes ou outros artefatos ao compartilhar uma filial. 

O diagrama a seguir mostra a estratégia de ramificação recomendada para TDSP. Você pode não precisar de tantos ramos como mostrado aqui, especialmente quando apenas uma ou duas pessoas trabalham em um projeto, ou apenas uma pessoa trabalha em todas as tarefas de uma história de usuário. Mas separar o ramo de desenvolvimento do ramo mestre é sempre uma boa prática, e pode ajudar a evitar que o ramo de liberação seja interrompido por atividades de desenvolvimento. Para obter uma descrição completa do modelo de ramificação Git, consulte [Um modelo de ramificação git bem sucedido](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Você também pode vincular um item de trabalho para uma branch existente. Na página **Detalhes** de um item de trabalho, selecione **Adicionar link**. Em seguida, selecione uma filial existente para vincular o item de trabalho e selecione **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Trabalhe no ramo e comprometa mudanças 

Depois de fazer uma alteração no item de trabalho, como adicionar `script` um arquivo de script R à filial da sua máquina local, você pode cometer a mudança de sua filial local para o ramo de trabalho upstream usando os seguintes comandos de bash do Git:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Criar uma solicitação de pull

Depois de um ou mais compromissos e empurrões, quando você estiver pronto para fundir seu ramo de trabalho atual em seu ramo base, você pode criar e enviar uma *solicitação de atração* no Azure Repos. 

Na página principal do projeto Azure DevOps, aponte para **solicitações de Reis** > **Puxar** na navegação à esquerda. Em seguida, selecione qualquer um dos botões **de solicitação de novo** ou o link Criar uma solicitação de **puxar.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Na tela **Nova solicitação de puxar,** se necessário, navegue até o repositório git e ramo em que deseja mesclar suas alterações. Adicione ou altere qualquer outra informação desejada. Em **Revisores,** adicione os nomes dos revisores e selecione **Criar**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Examinar e mesclar

Depois de criar a solicitação de puxar, seus revisores receberão uma notificação por e-mail para revisar a solicitação de retirada. Os revisores testam se as mudanças funcionam e verificam as alterações com o solicitante, se possível. Os revisores podem fazer comentários, solicitar alterações e aprovar ou rejeitar o pedido de retirada com base em sua avaliação. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Depois que os revisores aprovarem as alterações, você ou outra pessoa com permissões de mesclagem podem fundir o ramo de trabalho ao seu ramo base. Selecione **Concluir**e, em seguida, selecione **Concluir a fusão** na caixa **de diálogo 'Solicitação de puxar** completa'. Você pode optar por excluir o ramo de trabalho depois de fundido. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confirme se a solicitação está marcada como **CONCLUÍDA**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando você volta para **Repos** na navegação esquerda, você pode ver que você `script` foi trocado para o ramo principal desde que o ramo foi excluído.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Você também pode usar os seguintes comandos de bash do Git para mesclar o `script` ramo de trabalho ao seu ramo base e excluir o ramo de trabalho após a fusão:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Próximas etapas

[Executar tarefas de ciência de dados](execute-data-science-tasks.md) mostra como usar utilitários para completar várias tarefas comuns de ciência de dados, como exploração interativa de dados, análise de dados, emissão de relatórios e criação de modelos.

[Exemplos de passo a passo](walkthroughs.md) lista passo a passo de cenários específicos, com links e descrições de miniaturas. Os cenários vinculados ilustram como combinar ferramentas e serviços em nuvem e on-premises em fluxos de trabalho ou pipelines para criar aplicativos inteligentes. 

