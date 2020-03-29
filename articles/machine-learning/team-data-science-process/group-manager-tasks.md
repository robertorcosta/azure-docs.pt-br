---
title: Tarefas do gerenciador de grupos de Processo de Ciência de Dados de Equipe
description: Acompanhe este passo a passo detalhado das tarefas que um gerente de grupo completa em um projeto de equipe de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721346"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Tarefas do gerenciador de grupos de Processo de Ciência de Dados de Equipe

Este artigo descreve as tarefas que um *gerente de grupo* completa para uma organização de ciência de dados. O gerente do grupo gerencia toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada uma delas trabalhando em muitos projetos de ciência de dados em distintas verticais de negócios. O objetivo do gerente do grupo é estabelecer um ambiente de grupo colaborativo que se normalize no [Processo de Ciência](overview.md) de Dados da Equipe (TDSP). Para um esboço de todas as funções de pessoal e tarefas associadas tratadas por uma equipe de ciência de dados padronizada no TDSP, consulte [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipe .

O diagrama a seguir mostra as seis tarefas principais de configuração do gerenciador de grupo. Os gerentes de grupo podem delegar suas tarefas aos substitutos, mas as tarefas associadas à função não mudam.

![Tarefas de gerente de grupo](./media/group-manager-tasks/tdsp-group-manager.png)

1. Criar uma **organização Azure DevOps** para o grupo.
2. Crie o **projeto padrão GroupCommon** na organização Azure DevOps.
3. Crie o repositório **GroupProjectTemplate** no Azure Repos.
4. Crie o repositório **GroupUtilities** em Azure Repos.
5. Importe o conteúdo dos repositórios **ProjectTemplate** e **Utilities** da equipe do Microsoft TDSP para os repositórios comuns do grupo.
6. Configure **a adesão** e **as permissões** para os membros da equipe acessarem o grupo.

O tutorial a seguir percorre as etapas em detalhes. 

> [!NOTE] 
> Este artigo usa o Azure DevOps para configurar um ambiente de grupo TDSP, porque é assim que se implementa o TDSP na Microsoft. Se o seu grupo usa outras plataformas de hospedagem ou desenvolvimento de código, as tarefas do Gerente de Grupo são as mesmas, mas a maneira de completá-las pode ser diferente.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Crie uma organização e um projeto no Azure DevOps

1. Vá para [visualstudio.microsoft.com](https://visualstudio.microsoft.com), selecione **Entrar** no canto superior direito e faça login na sua conta microsoft. 
   
   ![Entre na sua conta da Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Se você não tiver uma conta microsoft, **selecione Cadastre-se agora,** crie uma conta microsoft e faça login usando essa conta. Se sua organização tiver uma assinatura do Visual Studio, faça login com as credenciais dessa assinatura.
   
1. Depois de fazer login, no canto superior direito na página Azure DevOps, selecione **Criar nova organização**.
   
   ![Criar nova organização](./media/group-manager-tasks/create-organization.png)
   
1. Se você for solicitado a concordar com os Termos de Serviço, Declaração de Privacidade e Código de Conduta, **selecione Continuar**.
   
1. Na caixa de diálogo de inscrição, nomeie sua organização Azure DevOps e aceite a atribuição da região de host ou baixe e selecione uma região diferente. Em seguida, **selecione Continuar**. 

1. Em **Criar um projeto para começar,** insira *GroupCommon*e selecione **Criar projeto**. 
   
   ![Criar projeto](./media/group-manager-tasks/create-project.png)

A página **Resumo do** projeto **GroupCommon** é aberta. A URL da página é *\//\<https: servername>/\<organization-name>/GroupCommon*.

![Página de resumo do projeto](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Configure os repositórios comuns do grupo

O Azure Repos hospeda os seguintes tipos de repositórios para o seu grupo:

- **Grupo repositórios comuns**: Repositórios de uso geral que várias equipes dentro de uma unidade de ciência de dados podem adotar para muitos projetos de ciência de dados. 
- **Repositórios de**equipe : Repositórios para equipes específicas dentro de uma unidade de ciência de dados. Esses repositórios são específicos para as necessidades de uma equipe, e podem ser usados para vários projetos dentro dessa equipe, mas não são gerais o suficiente para serem usados em várias equipes dentro de uma unidade de ciência de dados.
- **Repositórios de**projetos : Repositórios para projetos específicos. Esses repositórios podem não ser gerais o suficiente para vários projetos dentro de uma equipe ou para outras equipes em uma unidade de ciência de dados.

Para configurar os repositórios comuns do grupo em seu projeto, você: 
- Renomeie o repositório **padrão do GroupCommon** para **GroupProjectTemplate**
- Crie um novo repositório **GroupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Renomeie o repositório de projeto padrão para GroupProjectTemplate

Para renomear o repositório de projeto **GroupCommon** padrão para **GroupProjectTemplate**:

1. Na página **Resumo do** projeto **GroupCommon,** selecione **Repos**. Essa ação leva você ao repositório **Padrão GroupCommon** do projeto GroupCommon, que está atualmente vazio.
   
1. No topo da página, baixe a seta ao lado do **GroupCommon** e selecione **Gerenciar repositórios**.
   
   ![Gerenciar repositórios](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na página **Configurações do projeto,** selecione o **...** ao lado do **GroupCommon**e selecione **Renomear repositório**. 
   
   ![Selecione... e, em seguida, selecione Renomear repositório](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. No **Renomeação do popup do repositório GroupCommon,** insira *GroupProjectTemplate*e selecione **Renomear**. 
   
   ![Renomear repositório](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Criar o repositório GroupUtilities

Para criar o repositório **GroupUtilities:**

1. Na página **Resumo do** projeto **GroupCommon,** selecione **Repos**. 
   
1. Na parte superior da página, baixe a seta ao lado de **GroupProjectTemplate** e selecione **Novo repositório**.
   
   ![Selecione Novo repositório](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. No Criar uma nova caixa de diálogo **de repositório,** selecione **Git** como **tipo,** insira *GroupUtilities* como nome do **Repositório**e selecione **Criar**.
   
   ![Criar o repositório GroupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na página **Configurações do projeto,** selecione **Repositórios** em **Repos** na navegação à esquerda para ver os dois repositórios de grupo: **GroupProjectTemplate** e **GroupUtilities**.
   
   ![Dois repositórios de grupo](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importe os repositórios da equipe do Microsoft TDSP

Nesta parte do tutorial, você importa o conteúdo dos repositórios **ProjectTemplate** e **Utilities gerenciados** pela equipe do Microsoft TDSP em seus repositórios **GroupProjectTemplate** e **GroupUtilities.** 

Para importar os repositórios da equipe do TDSP:

1. Na página inicial do projeto **GroupCommon,** selecione **Repos** na navegação à esquerda. O repo **GroupProjectTemplate** padrão é aberto. 
   
1. Na **página GroupProjectTemplate está vazia,** selecione **Importar**. 
   
   ![Selecione Importar](./media/group-manager-tasks/import-repo.png)
   
1. Na caixa de diálogo **Importar um repositório Git,** selecione **Git** como o **tipo Origem**e digite *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* para a **URL clone**. Em seguida, **selecione Importar**. O conteúdo do repositório ProjectTemplate da equipe do Microsoft TDSP é importado para o repositório GroupProjectTemplate. 
   
   ![Importar repositório de equipe do Microsoft TDSP](./media/group-manager-tasks/import-repo-2.png)
   
1. Na parte superior da página **Repos,** baixe e selecione o repositório **GroupUtilities.**
   
1. Repita o processo de importação para importar o conteúdo do repositório **utilities da** equipe do Microsoft TDSP, *\/https: /github.com/Azure/Azure-TDSP-Utilities.git*, no repositório **GroupUtilities.** 
   
Cada um de seus dois repositórios de grupo agora contém todos os arquivos, exceto aqueles no diretório *.git,* do repositório correspondente da equipe do Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Personalize o conteúdo dos repositórios do grupo

Se você quiser personalizar o conteúdo de seus repositórios de grupo para atender às necessidades específicas do seu grupo, você pode fazer isso agora. Você pode modificar os arquivos, alterar a estrutura do diretório ou adicionar arquivos que seu grupo desenvolveu ou que são úteis para o seu grupo.

### <a name="make-changes-in-azure-repos"></a>Faça alterações nos Repos do Azure

Para personalizar o conteúdo do repositório:

1. Na página **Resumo do** projeto **GroupCommon,** selecione **Repos**. 
   
1. No topo da página, selecione o repositório que deseja personalizar.

1. Na estrutura do diretório de repo, navegue até a pasta ou arquivo que deseja alterar. 
   
   - Para criar novas pastas ou arquivos, selecione a seta ao lado **de Novo**. 
     
     ![Criar novo arquivo](./media/group-manager-tasks/new-file.png)
     
   - Para carregar arquivos, selecione **Upload file(s)**. 
     
     ![Carregar arquivos](./media/group-manager-tasks/upload-files.png)
     
   - Para editar os arquivos existentes, navegue até o arquivo e selecione **Editar**. 
     
     ![Editar um arquivo](./media/group-manager-tasks/edit-file.png)
     
1. Depois de adicionar ou editar arquivos, selecione **'Comprometer**'
   
   ![Confirmar alterações](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Faça alterações usando sua máquina local ou DSVM

Se você quiser fazer alterações usando sua máquina local ou DSVM e empurrar as alterações para os repositórios de grupo, certifique-se de ter os pré-requisitos para trabalhar com Git e DSVMs:

- Uma assinatura do Azure, se você quiser criar um DSVM.
- Git instalado em sua máquina. Se você estiver usando um DSVM, o Git está pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux criado e configurado no Azure. Para obter mais informações e instruções, consulte a [Documentação](/azure/machine-learning/data-science-virtual-machine/)da Máquina Virtual de Ciência de Dados .
- Para um Windows DSVM, [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em sua máquina. No arquivo *README.md,* desça até a seção **Baixar e Instalar** e selecione o **instalador mais recente**. Baixe o instalador *.exe* na página do instalador e execute-o. 
- Para um DSVM Linux, uma chave pública SSH configurada em seu DSVM e adicionada no Azure DevOps. Para obter mais informações e instruções, consulte a seção **Criar chave pública SSH** nas plataformas e no [apêndice de ferramentas](platforms-and-tools.md#appendix). 

Primeiro, copie ou *clone* o repositório para sua máquina local. 
   
1. Na página **Resumo do** projeto **GroupCommon,** selecione **Repos**e, no topo da página, selecione o repositório que deseja clonar.
   
1. Na página de repo, selecione **Clone** no canto superior direito.
   
1. Na caixa de diálogo **do repositório Clone,** selecione **HTTPS** para uma conexão HTTP ou **SSH** para uma conexão SSH e copie a URL clone sob **a linha command** para sua área de transferência.
   
   ![Clonar repositório](./media/group-manager-tasks/clone.png)
   
1. Em sua máquina local, crie os seguintes diretórios:
   
   - Para Windows: **C:\GitRepos\GroupCommon**
   - Para Linux, **$/GitRepos/GroupCommon** em seu diretório inicial 
   
1. Mude para o diretório que você criou.
   
1. Em Git Bash, execute o comando`git clone <clone URL>.`
   
   Por exemplo, qualquer um dos comandos a seguir clona o repositório **GroupUtilities** para o diretório *GroupCommon* em sua máquina local. 
   
   **Conexão HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Conexão SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Depois de fazer as alterações desejadas no clone local do seu repositório, você pode empurrar as alterações para os repositórios comuns do grupo compartilhado. 

Execute os seguintes comandos Git Bash do diretório local **GroupProjectTemplate** ou **GroupUtilities.**

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

## <a name="add-group-members-and-configure-permissions"></a>Adicionar membros do grupo e configurar permissões

Para adicionar membros ao grupo:

1. No Azure DevOps, da página inicial do projeto **GroupCommon,** selecione **configurações** do Projeto a partir da navegação à esquerda. 
   
1. Nas **configurações** de projeto navegação esquerda, selecione **Equipes,** em seguida, na página **Equipes,** selecione a **Equipe do GrupoComum**. 
   
   ![Configurar equipes](./media/group-manager-tasks/teams.png)
   
1. Na página Perfil da **equipe,** selecione **Adicionar**.
   
   ![Adicionar ao GroupCommon Team](./media/group-manager-tasks/add-to-team.png)
   
1. Na caixa de diálogo **Adicionar usuários e grupos,** procure e selecione membros para adicionar ao grupo e, em seguida, **selecione Salvar alterações**. 
   
   ![Adicionar usuários e grupos](./media/group-manager-tasks/add-users.png)
   

Para configurar permissões para membros:

1. A partir da navegação esquerda **configurações do projeto,** selecione **Permissões**. 
   
1. Na página **Permissões,** selecione o grupo ao que deseja adicionar membros. 
   
1. Na página para esse grupo, selecione **Membros**e selecione **Adicionar**. 
   
1. No **popup De convidar membros,** procure e selecione membros para adicionar ao grupo e, em seguida, **selecione Salvar**. 
   
   ![Conceder permissões aos membros](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Próximas etapas

Aqui estão links para descrições detalhadas das outras funções e tarefas no Processo de Ciência de Dados da Equipe:

- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Projeto Individual Contribuinte tarefas para uma equipe de ciência de dados](project-ic-tasks.md)
