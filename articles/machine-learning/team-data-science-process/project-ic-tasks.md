---
title: Tarefas para um contribuidor individual no Processo de Ciência de Dados de Equipe
description: Um passo a passo detalhado das tarefas para um colaborador individual em um projeto de equipe de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721244"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Tarefas para um contribuidor individual no Processo de Ciência de Dados de Equipe

Este tópico descreve as tarefas que um *colaborador individual* completa para configurar um projeto no Processo de Ciência de Dados da [Equipe](overview.md) (TDSP). O objetivo é trabalhar em um ambiente de equipe colaborativa que se normalize no TDSP. O TDSP foi projetado para ajudar a melhorar a colaboração e o aprendizado em equipe. Para um esboço das funções de pessoal e suas tarefas associadas que são tratadas por uma equipe de ciência de dados padronizada no TDSP, consulte [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipe .

O diagrama a seguir mostra as tarefas que projetam colaboradores individuais (cientistas de dados) completam para configurar seu ambiente de equipe. Para obter instruções sobre como executar um projeto de ciência de dados no âmbito do TDSP, consulte [Execução de projetos de ciência de dados](project-execution.md). 

![Tarefas individuais de contribuinte](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** é o repositório que sua equipe de projeto mantém para compartilhar modelos e ativos de projetos.
- **TeamUtilities** é o repositório de utilidades que sua equipe mantém especificamente para sua equipe. 
- **GroupUtilities** é o repositório que seu grupo mantém para compartilhar utilitários úteis em todo o grupo. 

> [!NOTE] 
> Este artigo usa o Azure Repos e uma Máquina Virtual de Data Science (DSVM) para configurar um ambiente TDSP, porque é assim que se implementa o TDSP na Microsoft. Se sua equipe usa outras plataformas de hospedagem ou desenvolvimento de código, as tarefas individuais de contribuinte são as mesmas, mas a maneira de completá-las pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que os seguintes recursos e permissões foram configurados pelo seu [gerente de grupo,](group-manager-tasks.md) [líder da equipe](team-lead-tasks.md)e líder do [projeto](project-lead-tasks.md):

- A **organização** Azure DevOps para sua unidade de ciência de dados
- Um **repositório de projetos** criado pelo seu projeto leva a compartilhar modelos e ativos do projeto
- **Repositórios GroupUtilities** e **TeamUtilities criados** pelo gerente do grupo e líder da equipe, se aplicável
- Armazenamento **de arquivos** Azure configurado para ativos compartilhados para sua equipe ou projeto, se aplicável
- **Permissões** para você clonar e empurrar de volta para o seu repositório de projeto 

Para clonar repositórios e modificar o conteúdo em sua máquina local ou DSVM, ou montar o armazenamento de arquivos Azure para o seu DSVM, você precisa considerar esta lista de verificação:

- Uma assinatura do Azure.
- Git instalado em sua máquina. Se você estiver usando um DSVM, o Git está pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux criado e configurado no Azure. Para obter mais informações e instruções, consulte a [Documentação](/azure/machine-learning/data-science-virtual-machine/)da Máquina Virtual de Ciência de Dados .
- Para um Windows DSVM, [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em sua máquina. No arquivo *README.md,* desça até a seção **Baixar e Instalar** e selecione o **instalador mais recente**. Baixe o instalador *.exe* na página do instalador e execute-o. 
- Para um DSVM Linux, uma chave pública SSH configurada em seu DSVM e adicionada no Azure DevOps. Para obter mais informações e instruções, consulte a seção **Criar chave pública SSH** nas plataformas e no [apêndice de ferramentas](platforms-and-tools.md#appendix). 
- As informações de armazenamento de arquivos do Azure para qualquer armazenamento de arquivos Azure que você precisa montar no seu DSVM. 

## <a name="clone-repositories"></a>Repositórios de clones

Para trabalhar com repositórios localmente e empurrar suas alterações para a equipe compartilhada e repositórios de projeto, primeiro copie ou *clone* os repositórios para sua máquina local. 

1. No Azure DevOps, acesse a página de resumo do projeto da sua equipe em *https:\//\<nome do servidor>/\<nome da organização>/\<nome da equipe>, *por exemplo, **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Selecione **Repos** na navegação à esquerda e, no topo da página, selecione o repositório que deseja clonar.
   
1. Na página de repo, selecione **Clone** no canto superior direito.
   
1. Na caixa de diálogo **do repositório Clone,** selecione **HTTPS** para uma conexão HTTP ou **SSH** para uma conexão SSH e copie a URL clone **a linha command** para sua área de transferência.
   
   ![Clonar repositório](./media/project-ic-tasks/clone.png)
   
1. Em sua máquina local ou DSVM, crie os seguintes diretórios:
   
   - Para Windows: **C:\GitRepos**
   - Para Linux: **$home/GitRepos**
   
1. Mude para o diretório que você criou.
   
1. Em Git Bash, `git clone <clone URL>` execute o comando de cada repositório que você deseja clonar. 
   
   Por exemplo, o comando a seguir clona o repositório **TeamUtilities** para o diretório *MyTeam* em sua máquina local. 
   
   **Conexão HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Conexão SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Confirme se você pode ver as pastas dos repositórios clonados em seu diretório de projeto local.
   
   ![Três pastas de repositório local](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Armazenamento de arquivos Mount Azure para seu DSVM

Se sua equipe ou projeto tiver ativos compartilhados no armazenamento de arquivos Do Azure, monte o armazenamento de arquivos na máquina local ou no DSVM. Siga as instruções no [armazenamento de arquivos Mount Azure em sua máquina local ou DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Próximas etapas

Aqui estão links para descrições detalhadas das outras funções e tarefas definidas pelo Processo de Ciência de Dados da Equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)

