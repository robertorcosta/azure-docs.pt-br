---
title: Tarefas para o líder de equipe do Processo de Ciência de Dados da Equipe
description: Um passo a passo detalhado das tarefas para uma equipe líder em uma equipe de Processo de Ciência de Dados da equipe
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864274"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Tarefas para a equipe liderar em uma equipe de Processo de Ciência de Dados da equipe

Este artigo descreve as tarefas que uma *equipe lidera* completa para sua equipe de ciência de dados. O objetivo da equipe é estabelecer um ambiente de equipe colaborativa que se normalize no [Processo de Ciência](overview.md) de Dados da Equipe (TDSP). O TDSP foi projetado para ajudar a melhorar a colaboração e o aprendizado em equipe. 

O TDSP é uma metodologia ágil e iterativa de ciência de dados para fornecer com eficiência soluções de análise preditiva e aplicações inteligentes. O processo destila as melhores práticas e estruturas da Microsoft e da indústria.  O objetivo é a implementação bem-sucedida de iniciativas de ciência de dados e a realização completa dos benefícios de seus programas de análise. Para um esboço das funções de pessoal e tarefas associadas para uma equipe de ciência de dados padronizada no TDSP, consulte [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipe .

Um líder de equipe gerencia uma equipe composta por vários cientistas de dados na unidade de ciência de dados de uma empresa. Dependendo do tamanho e estrutura da unidade de ciência de dados, o gerente do [grupo](group-manager-tasks.md) e o líder da equipe podem ser a mesma pessoa, ou podem delegar suas tarefas aos substitutos. Mas as tarefas em si não são alteradas. 

O diagrama a seguir mostra o fluxo de trabalho para as tarefas que a equipe lidera completa para configurar um ambiente de equipe:

![Fluxo de trabalho de tarefa sulidera equipe](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Crie um **projeto de equipe** na organização do grupo no Azure DevOps. 
  
1. Renomeie o repositório de equipe padrão para **TeamUtilities**.
  
1. Crie um novo repositório **TeamTemplate** no projeto da equipe. 
  
1. Importe o conteúdo dos repositórios **GroupUtilities** e **GroupProjectTemplate** do grupo nos repositórios **TeamUtilities** e **TeamTemplate.** 
  
1. Configure o **controle de segurança** adicionando membros da equipe e configurando suas permissões.
  
1. Se necessário, crie dados de equipe e recursos de análise:
   - Adicione utilitários específicos da equipe ao repositório **TeamUtilities.** 
   - Crie **o armazenamento de arquivos Do Zure** para armazenar ativos de dados que podem ser úteis para toda a equipe. 
   - Monte o armazenamento de arquivos Azure na Máquina Virtual de **Data Science** (DSVM) do líder da equipe e adicione ativos de dados a ele.

O tutorial a seguir percorre as etapas em detalhes.

> [!NOTE] 
> Este artigo usa o Azure DevOps e um DSVM para configurar um ambiente de equipe TDSP, porque é assim que se implementa o TDSP na Microsoft. Se sua equipe usa outras plataformas de hospedagem ou desenvolvimento de código, as tarefas de liderança da equipe são as mesmas, mas a maneira de completá-las pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que os seguintes recursos e permissões foram configurados pelo seu [gerente de grupo](group-manager-tasks.md):

- A **organização** Azure DevOps para sua unidade de dados
- Repositórios **GroupProjectTemplate** e **GroupUtilities, preenchidos** com o conteúdo dos repositórios **ProjectTemplate** e **Utilities** da equipe do Microsoft TDSP
- Permissões na conta da sua organização para você criar projetos e repositórios para sua equipe

Para poder clonar repositórios e modificar seu conteúdo em sua máquina local ou DSVM, ou configurar o armazenamento de arquivos Do Zure e montá-lo no seu DSVM, você precisa do seguinte:

- Uma assinatura do Azure.
- Git instalado em sua máquina. Se você estiver usando um DSVM, o Git está pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux criado e configurado no Azure. Para obter mais informações e instruções, consulte a [Documentação](/azure/machine-learning/data-science-virtual-machine/)da Máquina Virtual de Ciência de Dados .
- Para um Windows DSVM, [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em sua máquina. No arquivo *README.md,* desça até a seção **Baixar e Instalar** e selecione o **instalador mais recente**. Baixe o instalador *.exe* na página do instalador e execute-o. 
- Para um DSVM Linux, uma chave pública SSH configurada em seu DSVM e adicionada no Azure DevOps. Para obter mais informações e instruções, consulte a seção **Criar chave pública SSH** nas plataformas e no [apêndice de ferramentas](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Criar um projeto de equipe e repositórios

Nesta seção, você cria os seguintes recursos na organização Azure DevOps do seu grupo:

- O projeto **MyTeam** no Azure DevOps
- O repositório **TeamTemplate**
- O repositório **TeamUtilities**

Os nomes especificados para os repositórios e diretórios neste tutorial assumem que você deseja estabelecer um projeto separado para sua própria equipe dentro de sua maior organização de ciência de dados. No entanto, todo o grupo pode optar por trabalhar sob um único projeto criado pelo gerente do grupo ou administrador da organização. Então, todas as equipes de ciência de dados criam repositórios este único projeto. Este cenário pode ser válido para:
- Um pequeno grupo de ciência de dados que não tem várias equipes de ciência de dados. 
- Um grupo maior de ciência de dados com várias equipes de ciência de dados que, no entanto, quer otimizar a colaboração entre equipes com atividades como planejamento de sprint em nível de grupo. 

Se as equipes optarem por ter seus repositórios específicos da equipe em um único projeto de grupo, os leads da equipe devem criar os repositórios com nomes como * \<TeamName>Template* e * \<TeamName>Utilities*. Por exemplo: *TeamATemplate* e *TeamAUtilities*. 

De qualquer forma, os líderes da equipe precisam informar aos membros da equipe quais reósios de modelos e utilitários configurar e clonar. Os leads do projeto devem seguir as [tarefas de liderança](project-lead-tasks.md) do projeto para que uma equipe de ciência de dados crie repositórios de projetos, seja em projetos separados ou em um único projeto. 

### <a name="create-the-myteam-project"></a>Criar o projeto MyTeam

Para criar um projeto separado para sua equipe:

1. No seu navegador da Web, vá para a página inicial da organização Azure DevOps do seu grupo na URL *https:\//\<nome do servidor\<>/ nome da organização>* e selecione Novo **projeto**. 
   
   ![Selecione Novo projeto](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. Na caixa de diálogo **Criar projeto,** digite o nome da sua equipe, como *MyTeam*, em **nome do Projeto,** e selecione **Avançado**. 
   
1. Em **Controle de Versão,** selecione **Git**e no **processo de trabalho,** selecione **Ágil**. Em seguida, **selecione Criar**. 
   
   ![Criar projeto](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
A página **resumo do** projeto da equipe é aberta, com a página URL *https:\//\<nome do servidor>/\<nome da organização>/\<nome da equipe>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Renomeie o repositório padrão MyTeam para TeamUtilities

1. Na página **Resumo do** projeto **MyTeam,** em **qual serviço você gostaria de começar?**, selecione **Repos**. 
   
   ![Selecione Repos](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Na página de repo **do MyTeam,** selecione o repositório **MyTeam** na parte superior da página e selecione **Gerenciar repositórios** a partir do dropdown. 
   
   ![Selecione Gerenciar repositórios](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Na página **Configurações do projeto,** selecione o **...** ao lado do repositório **MyTeam** e, em seguida, selecione **Renomerepository**. 
   
   ![Selecione o repositório Rename](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. No **renomeo do popup do repositório MyTeam,** digite *TeamUtilities*e selecione **Renomear**. 

### <a name="create-the-teamtemplate-repository"></a>Crie o repositório TeamTemplate

1. Na página **Configurações do projeto,** selecione **Novo repositório.** 
   
   ![Selecione Novo repositório](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Ou selecione **Repos** na navegação à esquerda da página **Resumo do** projeto **MyTeam,** selecione um repositório na parte superior da página e selecione **Novo repositório** a partir da parada.
   
1. Na criação de uma nova caixa de diálogo **de repositório,** certifique-se de que **o Git** está selecionado em **Tipo**. Digite *TeamTemplate* em **nome de Repositório**e, em seguida, selecione **Criar**.
   
   ![Criar repositório](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Confirme se você pode ver os dois repositórios **TeamUtilities** e **TeamTemplate** na página de configurações do projeto. 
   
   ![Dois repositórios de equipe](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importar o conteúdo dos repositórios comuns do grupo

Para preencher os repositórios da sua equipe com o conteúdo dos repositórios comuns do grupo configurados pelo gerenciador de grupo:

1. Na página inicial do projeto **MyTeam,** selecione **Repos** na navegação à esquerda. Se você receber uma mensagem de que o modelo **MyTeam** não foi encontrado, selecione o link em **Caso Contrário, navegue até o repositório TeamTemplate padrão.** 
   
   O repositório **TeamTemplate** padrão é aberto. 
   
1. Na **página TeamTemplate está vazia,** selecione **Importar**. 
   
   ![Selecione Importar](./media/team-lead-tasks/import-repo.png)
   
1. Na **caixa de diálogo Importar um repositório Git,** selecione **Git** como **o tipo De Origem**e digite a URL para o repositório de modelo comum do grupo em Clone **URL**. A URL é *\//\<https:\<nome do servidor\<>/ nome da organização>/_git/ nome do repositório>*. Por exemplo: *\/https: /dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Selecione **Importar**. O conteúdo do repositório do modelo de grupo é importado para o repositório de modelo sérico. 
   
   ![Repositório de modelos comuns do grupo de importação](./media/team-lead-tasks/import-repo-2.png)
   
1. Na parte superior da página **Repos** do seu projeto, baixe e selecione o repositório **TeamUtilities.**
   
1. Repita o processo de importação para importar o conteúdo do repositório de utilitários comuns do seu grupo, por exemplo *GroupUtilities*, em seu repositório **TeamUtilities.** 
   
Cada um de seus dois repositórios de equipe agora contém os arquivos do repositório comum do grupo correspondente. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Personalize o conteúdo dos repositórios da equipe

Se você quiser personalizar o conteúdo dos repositórios da sua equipe para atender às necessidades específicas da sua equipe, você pode fazer isso agora. Você pode modificar arquivos, alterar a estrutura do diretório ou adicionar arquivos e pastas.

Para modificar, carregar ou criar arquivos ou pastas diretamente no Azure DevOps:

1. Na página **Resumo do** projeto **MyTeam,** selecione **Repos**. 
   
1. No topo da página, selecione o repositório que deseja personalizar.

1. Na estrutura do diretório de repo, navegue até a pasta ou arquivo que deseja alterar. 
   
   - Para criar novas pastas ou arquivos, selecione a seta ao lado **de Novo**. 
     
     ![Criar novo arquivo](./media/team-lead-tasks/new-file.png)
     
   - Para carregar arquivos, selecione **Upload file(s)**. 
     
     ![Carregar arquivos](./media/team-lead-tasks/upload-files.png)
     
   - Para editar os arquivos existentes, navegue até o arquivo e selecione **Editar**. 
     
     ![Editar um arquivo](./media/team-lead-tasks/edit-file.png)
     
1. Depois de adicionar ou editar arquivos, selecione **'Comprometer**'
   
   ![Confirmar alterações](./media/team-lead-tasks/commit.png)

Para trabalhar com repositórios em sua máquina local ou DSVM, primeiro copie ou *clone* os repositórios para sua máquina local e, em seguida, se compromete e empurra suas alterações para os repositórios da equipe compartilhada, 

Para clonar repositórios:

1. Na página **Resumo do** projeto **MyTeam,** selecione **Repos**e, no topo da página, selecione o repositório que deseja clonar.
   
1. Na página de repo, selecione **Clone** no canto superior direito.
   
1. Na caixa de diálogo **do repositório Clone,** em **linha de comando,** selecione **HTTPS** para uma conexão HTTP ou **SSH** para uma conexão SSH e copie a URL clone para sua área de transferência.
   
   ![Copiar URL clone](./media/team-lead-tasks/clone.png)
   
1. Em sua máquina local, crie os seguintes diretórios:
   
   - Para Windows: **C:\GitRepos\MyTeam**
   - Para Linux, **$home/GitRepos/MyTeam** 
   
1. Mude para o diretório que você criou.
   
1. No Git Bash, `git clone <clone URL>`execute \<o comando , onde o clone de URL> é a URL que você copiou da caixa de diálogo **Clone.**
   
   Por exemplo, use um dos seguintes comandos para clonar o repositório **TeamUtilities** para o diretório *MyTeam* em sua máquina local. 
   
   **Conexão HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Conexão SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Depois de fazer as alterações desejadas no clone local do seu repositório, comprometa e empurre as alterações para os repositórios da equipe compartilhada. 

Execute os seguintes comandos Git Bash do diretório **GitRepos\MyTeam\TeamTemplate** ou **GitRepos\MyTeam\TeamUtilities.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se esta for a primeira vez que você se compromete com um repositório do Git, `git commit` talvez seja necessário configurar parâmetros globais *user.name* e *usuário.email* antes de executar o comando. Execute os dois comandos a seguir:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se você estiver se comprometendo com vários repositórios do Git, use o mesmo nome e endereço de e-mail para todos eles. Usar o mesmo nome e endereço de e-mail é conveniente ao criar painéis power bi para rastrear suas atividades do Git em vários repositórios.

## <a name="add-team-members-and-configure-permissions"></a>Adicionar membros da equipe e configurar permissões

Para adicionar membros à equipe:

1. No Azure DevOps, da página inicial do projeto **MyTeam,** selecione **configurações** do Projeto a partir da navegação à esquerda. 
   
1. A partir da navegação esquerda **configurações do projeto,** selecione **Equipes,** em seguida, na página **Equipes,** selecione a **Equipe MyTeam**. 
   
   ![Configurar equipes](./media/team-lead-tasks/teams.png)
   
1. Na página Perfil da **equipe,** selecione **Adicionar**.
   
   ![Adicionar ao MyTeam Team](./media/team-lead-tasks/add-to-team.png)
   
1. Na caixa de diálogo **Adicionar usuários e grupos,** procure e selecione membros para adicionar ao grupo e, em seguida, **selecione Salvar alterações**. 
   
   ![Adicionar usuários e grupos](./media/team-lead-tasks/add-users.png)
   

Para configurar permissões para membros da equipe:

1. A partir da navegação esquerda **configurações do projeto,** selecione **Permissões**. 
   
1. Na página **Permissões,** selecione o grupo ao que deseja adicionar membros. 
   
1. Na página para esse grupo, selecione **Membros**e selecione **Adicionar**. 
   
1. No **popup De convidar membros,** procure e selecione membros para adicionar ao grupo e, em seguida, **selecione Salvar**. 
   
   ![Conceder permissões aos membros](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Crie dados de equipe e recursos de análise

Essa etapa é opcional, mas compartilhar dados e recursos de análise com toda a sua equipe tem benefícios de desempenho e custo. Os membros da equipe podem executar seus projetos com recursos compartilhados, economizar em orçamentos e colaborar de forma mais eficiente. Você pode criar o armazenamento de arquivos Azure e montá-lo em seu DSVM para compartilhar com os membros da equipe. 

Para obter informações sobre como compartilhar outros recursos com sua equipe, como clusters Azure HDInsight Spark, consulte [Plataformas e ferramentas](platforms-and-tools.md). Esse tópico fornece orientação a partir de uma perspectiva de ciência de dados sobre a seleção de recursos apropriados para suas necessidades e links para páginas de produtos e outros tutoriais relevantes e úteis.

>[!NOTE]
> Para evitar a transmissão de dados através de data centers, o que pode ser lento e caro, certifique-se de que seu grupo de recursos do Azure, conta de armazenamento e DSVM estejam todos hospedados na mesma região do Azure. 

### <a name="create-azure-file-storage"></a>Criar armazenamento de arquivos Azure

1. Execute o script a seguir para criar o armazenamento de arquivos Azure para ativos de dados que são úteis para toda a sua equipe. O script solicita informações de assinatura do Azure, então tenha isso pronto para entrar. 

   - Em uma máquina Windows, execute o script do prompt de comando PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Em uma máquina Linux, execute o script da shell Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Faça login na sua conta do Microsoft Azure quando solicitado e selecione a assinatura que deseja usar.
   
1. Selecione a conta de armazenamento a ser usada ou crie uma nova sua assinatura selecionada. Você pode usar caracteres minúsculos, números e hífens para o nome de armazenamento de arquivos Azure.
   
1. Para facilitar a montagem e o compartilhamento do armazenamento, pressione Enter ou enter *Y* para salvar as informações de armazenamento de arquivos do Azure em um arquivo de texto em seu diretório atual. Você pode verificar neste arquivo de texto o seu repositório **TeamTemplate,** idealmente em **Docs\DataDictionaries,** para que todos os projetos da sua equipe possam acessá-lo. Você também precisa das informações do arquivo para montar o armazenamento de arquivos do Azure no seu DSVM do Azure na próxima seção. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Monte o armazenamento de arquivos Do Azure em sua máquina local ou DSVM

1. Para montar o armazenamento de arquivos Do Azure na máquina local ou no DSVM, use o script a seguir.
   
   - Em uma máquina Windows, execute o script do prompt de comando PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Em uma máquina Linux, execute o script da shell Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Pressione Enter ou enter *Y* para continuar, se você salvou um arquivo de informações de armazenamento de arquivos Do Zure na etapa anterior. Digite o caminho completo e o nome do arquivo que você criou. 
   
   Se você não tiver um arquivo de informações de armazenamento de arquivos Azure, digite *n*e siga as instruções para inserir sua assinatura, conta de armazenamento Azure e informações de armazenamento de arquivos do Azure.
   
1. Digite o nome de uma unidade local ou TDSP para montar o compartilhamento de arquivos. A tela exibe uma lista de nomes de unidade existentes. Forneça um nome de unidade que ainda não existe.
   
1. Confirme se a nova unidade e armazenamento foi montado com sucesso na sua máquina.

## <a name="next-steps"></a>Próximas etapas

Aqui estão links para descrições detalhadas das outras funções e tarefas definidas pelo Processo de Ciência de Dados da Equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Projeto Individual Contribuinte tarefas para uma equipe de ciência de dados](project-ic-tasks.md)
