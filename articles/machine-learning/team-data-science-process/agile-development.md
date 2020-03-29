---
title: Desenvolvimento ágil de projetos de ciência de dados – Processo de Ciência de Dados da Equipe
description: Execute um projeto de ciência de dados de forma sistemática, controlada por versão e colaborativa dentro de uma equipe de projeto usando o Processo de Ciência de Dados da Equipe.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722094"
---
# <a name="agile-development-of-data-science-projects"></a>Desenvolvimento do Agile de projetos de ciência de dados

Este documento descreve como os desenvolvedores podem executar um projeto de ciência de dados de modo sistemático, com controle de versão e colaborativo em uma equipe de projeto usando o TDSP [(Processo de Ciência de Dados da Equipe)](overview.md). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma seqüência estruturada de atividades para executar eficientemente soluções de análise preditiva baseadas em nuvem. Para um esboço das funções e tarefas que são tratadas por uma equipe de ciência de dados padronizada no TDSP, consulte [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipe . 

Este artigo inclui instruções sobre como: 

- Faça *o planejamento de sprint* para itens de trabalho envolvidos em um projeto.
- Adicione *itens de trabalho* aos sprints.
- Crie e use um *modelo de item de trabalho derivado de ágil* que se alinhe especificamente com as etapas do ciclo de vida do TDSP.

As instruções a seguir descrevem as etapas necessárias para configurar um ambiente de equipe TDSP usando placas azure e repositórios Azure no Azure DevOps. As instruções usam DevOps do Azure porque é assim que se implementa o TDSP na Microsoft. Se o seu grupo usa uma plataforma de hospedagem de código diferente, as tarefas de liderança da equipe geralmente não mudam, mas a maneira de concluir as tarefas é diferente. Por exemplo, vincular um item de trabalho a uma filial do Git pode não ser o mesmo com o GitHub como é com o Azure Repos.

A figura a seguir ilustra um típico fluxo de trabalho de planejamento, codificação e controle de origem para um projeto de ciência de dados:

![Processo de ciência de dados de equipe](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Tipos de item de trabalho

Na estrutura de planejamento de sprint do TDSP, existem quatro tipos de *itens de trabalho* frequentemente *usados: Recursos,* *Histórias do Usuário,* *Tarefas*e *Bugs.* O backlog para todos os itens de trabalho está no nível do projeto, não no nível de repositório do Git. 

Aqui estão as definições para os tipos de itens de trabalho:

- **Recurso**: Um recurso corresponde a um engajamento do projeto. Diferentes compromissos com um cliente são diferentes Recursos, e é melhor considerar diferentes fases de um projeto como diferentes Recursos. Se você escolher um esquema como * \<ClientName>-\<EngagementName>* para nomear seus Recursos, você poderá reconhecer facilmente o contexto do projeto e o engajamento dos próprios nomes.
  
- **História do usuário**: As histórias do usuário são itens de trabalho necessários para concluir um recurso de ponta a ponta. Exemplos de histórias de usuários incluem:
  - Obter dados 
  - Explorar dados 
  - Gerar recursos
  - Compilar modelos
  - Operacionalizar modelos 
  - Treinar modelos novamente
  
- **Tarefa**: As tarefas são itens de trabalho atribuíveis que precisam ser feitos para concluir uma história de usuário específica. Por exemplo, tarefas na história do usuário *obter dados* podem ser:
  - Obtenha credenciais do SQL Server
  - Carregar dados para o SQL Data Warehouse
  
- **Bug**: Bugs são problemas no código ou documentos existentes que devem ser corrigidos para concluir uma Tarefa. Se os bugs forem causados por itens de trabalho perdidos, eles podem se intensificar para serem Histórias de Usuário ou Tarefas. 

Os cientistas de dados podem se sentir mais confortáveis usando um modelo ágil que substitui recursos, histórias de usuário e tarefas por estágios e subestágios do ciclo de vida do TDSP. Para criar um modelo de derivação ágil que esteja especificamente alinhado com as etapas do ciclo de vida do TDSP, consulte [Usar um modelo de trabalho TDSP ágil](#set-up-agile-dsp-6).

> [!NOTE]
> O TDSP empresta os conceitos de Recursos, Histórias de Usuário, Tarefas e Bugs do Gerenciamento de Código de Software (SCM). Os conceitos de TDSP podem diferir ligeiramente de suas definições convencionais de SCM.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Planejar sprints

Muitos cientistas de dados estão envolvidos com vários projetos, que podem levar meses para serem concluídos e prosseguirem em diferentes ritmos. O planejamento de sprint é útil para priorização de projeto e planejamento e alocação de recursos. No Azure Boards, você pode facilmente criar, gerenciar e rastrear itens de trabalho para seus projetos e realizar o planejamento de sprint para garantir que os projetos estejam avançando conforme o esperado.

Para obter mais informações sobre o planejamento de sprint, consulte [Scrum sprints](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Para obter mais informações sobre o planejamento de sprint no Azure Boards, consulte [Atribuir itens de atraso a um sprint](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Adicionar um recurso ao backlog 

Depois que seu repositório de código de projeto e projeto for criado, você pode adicionar um recurso ao backlog para representar o trabalho para o seu projeto.

1. Na página do projeto, selecione **Backlogs** > **de placas** na navegação à esquerda. 
   
1. Na guia **Backlog,** se o tipo de item de trabalho na barra superior for **Stories,** baixe e selecione **Recursos**. Em seguida, selecione **Novo item de trabalho.**
   
   ![Selecione novo item de trabalho](./media/agile-development/2-sprint-team-overview.png)
   
1. Digite um título para o Recurso, geralmente o nome do projeto e, em seguida, **selecione Adicionar ao topo**. 
   
   ![Digite um título e selecione Adicionar ao topo](./media/agile-development/3-sprint-team-add-work.png)
   
1. Na lista **Backlog,** selecione e abra o novo Recurso. Preencha a descrição, designe um membro da equipe e defina parâmetros de planejamento. 
   
   Você também pode vincular o Recurso ao repositório de código Azure Repos do projeto selecionando **adicionar link** na seção **Desenvolvimento.** 
   
   Depois de editar o Recurso, **selecione Salvar & Fechar**.
   
   ![Editar recurso e selecionar Salvar & Fechar](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Adicione uma história do usuário ao recurso 

No Recurso, você pode adicionar Histórias de Usuário para descrever os principais passos necessários para concluir o projeto. 

Para adicionar uma nova história de usuário a um recurso:

1. Na guia **Backlog,** **+** selecione o à esquerda do Recurso. 
   
   ![Adicione uma nova história de usuário o recurso](./media/agile-development/4-sprint-add-story.png)
   
1. Dê um título ao User Story e edite detalhes como atribuição, status, descrição, comentários, planejamento e prioridade. 
   
   Você também pode vincular o User Story a uma filial do repositório de código Azure Repos do projeto selecionando **adicionar link** na seção **Desenvolvimento.** Selecione o repositório e a filial a que deseja vincular o item de trabalho e, em seguida, selecione **OK**.
   
   ![Adicionar Link](./media/agile-development/5-sprint-edit-story.png)
   
1. Quando terminar de editar a história do usuário, **selecione Salvar & Fechar**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Adicionar uma tarefa a uma história de usuário 

As tarefas são etapas específicas e detalhadas que são necessárias para concluir cada História do Usuário. Depois que todas as tarefas de uma história de usuário forem concluídas, a História do Usuário também deve ser concluída. 

Para adicionar uma tarefa a uma **+** história de usuário, selecione o próximo ao item 'História do usuário' e selecione **Tarefa**. Preencha o título e outras informações na Tarefa.

![Adicionar uma tarefa a uma história de usuário](./media/agile-development/7-sprint-add-task.png)

Depois de criar Recursos, Histórias de Usuário e Tarefas, você pode visualizá-los nas **exibições de backlogs** ou **boards** para rastrear seu status.

![Exibição de atrasos](./media/agile-development/8-sprint-backlog-view.png)

![Visualização de placas](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Use um modelo de trabalho TDSP ágil

Os cientistas de dados podem se sentir mais confortáveis usando um modelo ágil que substitui recursos, histórias de usuário e tarefas por estágios e subestágios do ciclo de vida do TDSP. No Azure Boards, você pode criar um modelo derivado de ágil que usa estágios do ciclo de vida do TDSP para criar e rastrear itens de trabalho. As etapas a seguir caminham através da criação de um modelo de processo ágil específico para ciência de dados e criação de itens de trabalho de ciência de dados com base no modelo.

### <a name="set-up-an-agile-data-science-process-template"></a>Configure um modelo de processo ágil de ciência de dados

1. Na página principal da organização Azure DevOps, selecione **configurações** de Organização a partir da navegação à esquerda. 
   
1. Nas **Configurações de Organização** navegação esquerda, em **Placas,** selecione **Processo**. 
   
1. No **painel Todos os processos,** selecione o **...** ao lado do **Ágil**, e, em seguida, selecione **Criar processo herdado**.
   
   ![Criar processo herdado do Agile](./media/agile-development/10-settings.png) 
   
1. No **processo Criar herdado da** caixa de diálogo Ágil, digite o nome *AgileDataScienceProcess*e selecione **Criar processo**.
   
   ![Crie o processo AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. Em **Todos os processos,** selecione o novo **AgileDataScienceProcess**. 
   
1. Na guia **Tipos de itens de trabalho,** desabilite **Epic**, **Feature,** **User Story**e **Task** selecionando o **...** ao lado de cada item e, em seguida, selecionando **Desativar**. 
   
   ![Desativar tipos de itens de trabalho](./media/agile-development/12-disable.png)
   
1. Em **Todos os processos,** selecione a guia **Níveis de atraso.** Em **Pastas,** selecione o **...** ao lado de **Epic (desativado)** e, em seguida, **selecione Editar/Renomear**. 
   
1. Na caixa de diálogo **editar nível de atraso:**
   1. Em **Name,** substitua **a Epic por** Projetos *TDSP*. 
   1. Em **'Trabalhar tipos de itens' neste nível de atraso,** selecione **Novo tipo de item de trabalho,** insira projeto *TDSP*e selecione **Adicionar**. 
   1. No **tipo de item de trabalho Padrão,** baixe e selecione **Projeto TDSP**. 
   1. Selecione **Salvar**.
   
   ![Definir o nível de backlog do Portfolio](./media/agile-development/13-rename.png)  
   
1. Siga as mesmas etapas para renomear **recursos** para *Estágios TDSP*e adicione os seguintes novos tipos de itens de trabalho:
   
   - *Compreensão dos negócios*
   - *Aquisição de Dados*
   - *Modelagem*
   - *Implantação*
   
1. Em **Backlog de requisitos,** renomear **Stories** para *Subestágios TDSP,* adicionar o novo tipo de item de trabalho *TDSP Subestágio*e definir o tipo de item de trabalho padrão para **Subestágio TDSP**.
   
1. Em **backlog iteração,** adicione um novo item de trabalho tipo *TDSP Task*e defina-o como o tipo de item de trabalho padrão. 
   
Depois de concluir as etapas, os níveis de backlog devem ficar assim:
   
 ![Níveis de backlog do modelo TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Crie itens de trabalho do Processo de Ciência de Dados Ágil

Você pode usar o modelo de processo de ciência de dados para criar projetos TDSP e rastrear itens de trabalho que correspondem às etapas do ciclo de vida do TDSP.

1. Na página principal da organização Azure DevOps, selecione **Novo projeto**. 
   
1. No **Criar novo diálogo de projeto,** dê um nome ao seu projeto e, em seguida, selecione **Avançado**. 
   
1. Em **processo de item de trabalho,** baixe e selecione **AgileDataScienceProcess**e selecione **Criar**.
   
   ![Criar um projeto TDSP](./media/agile-development/15-newproject.png)
   
1. No projeto recém-criado, selecione **Backlogs** > **de** placas na navegação à esquerda.
   
1. Para tornar visíveis os projetos TDSP, selecione o ícone **Configurar configurações da equipe.** Na tela **Configurações,** selecione a caixa de seleção **Projetos TDSP** e, em seguida, **selecione Salvar e fechar**.
   
   ![Selecione a caixa de seleção de projetos TDSP](./media/agile-development/16-enabledsprojects1.png)
   
1. Para criar um Projeto TDSP específico para ciência de dados, selecione **Projetos TDSP** na barra superior e selecione **Novo item de trabalho**. 
   
1. No popup, dê um nome ao item de trabalho do Projeto TDSP e selecione **Adicionar ao topo**.
   
   ![Criar item de trabalho de projeto de ciência de dados](./media/agile-development/17-dsworkitems0.png)
   
1. Para adicionar um item de trabalho no **+** projeto TDSP, selecione o próximo ao projeto e selecione o tipo de item de trabalho a ser criado. 
   
   ![Selecione o tipo de item de trabalho de ciência de dados](./media/agile-development/17-dsworkitems1.png)
   
1. Preencha os detalhes do novo item de trabalho e **selecione Salvar & Fechar**.
   
1. Continue a **+** selecionar os símbolos ao lado dos itens de trabalho para adicionar novos Estágios, Subestágios e Tarefas do TDSP. 
   
Aqui está um exemplo de como os itens de trabalho do projeto de ciência de dados devem aparecer na exibição **de Backlogs:**

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Próximas etapas

[A codificação colaborativa com o Git](collaborative-coding-with-git.md) descreve como fazer o desenvolvimento de código colaborativo para projetos de ciência de dados usando o Git como estrutura de desenvolvimento de código compartilhado e como vincular essas atividades de codificação ao trabalho planejado com o processo ágil.

[Exemplos de passo a passo](walkthroughs.md) lista passo a passo de cenários específicos, com links e descrições de miniaturas. Os cenários vinculados ilustram como combinar ferramentas e serviços em nuvem e on-premises em fluxos de trabalho ou pipelines para criar aplicativos inteligentes.
  
Recursos adicionais em processos ágeis:

- [Processo ágil](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Tipos de itens de trabalho de processo ágeis e fluxo de trabalho](/azure/devops/boards/work-items/guidance/agile-process-workflow)

