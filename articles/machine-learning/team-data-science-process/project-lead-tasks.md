---
title: Tarefas para o líder de projetos no Processo de Ciência de Dados da Equipe
description: Um passo a passo detalhado das tarefas para um líder de projeto em uma equipe do Processo de Ciência de Dados da Equipe
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714420"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Tarefas de liderança de projeto no Processo de Ciência de Dados da Equipe

Este artigo descreve tarefas que um *projeto de chumbo* completa para configurar um repositório para sua equipe de projeto no Processo de Ciência de Dados da [Equipe](overview.md) (TDSP). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma seqüência estruturada de atividades para executar eficientemente soluções de análise preditiva baseadas em nuvem. O TDSP foi projetado para ajudar a melhorar a colaboração e o aprendizado em equipe. Para um esboço das funções de pessoal e tarefas associadas para uma equipe de ciência de dados padronizada no TDSP, consulte [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipe .

Um líder de projeto gerencia as atividades diárias de cientistas de dados individuais em um projeto específico de ciência de dados no TDSP. O diagrama a seguir mostra o fluxo de trabalho para tarefas de liderança de projeto:

![Fluxo de trabalho de tarefa sulidera projeto](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Este tutorial abrange o Passo 1: Crie o repositório do projeto e o repositório do projeto Deed 2 do repositório ProjectTemplate da sua equipe. 

Para o passo 3: Crie o item de trabalho de recursos para o projeto, e passo 4: adicione histórias para fases do projeto, veja [desenvolvimento ágil de projetos de ciência de dados](agile-development.md).

Para o Passo 5: Crie e personalize os ativos de armazenamento/análise e compartilhe, se necessário, [criar dados da equipe e recursos de análise](team-lead-tasks.md#create-team-data-and-analytics-resources).

Para a Etapa 6: Configure o controle de segurança do repositório do projeto, consulte [Adicionar membros da equipe e configure permissões](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Este artigo usa o Azure Repos para configurar um projeto TDSP, porque é assim que se implementa o TDSP na Microsoft. Se sua equipe usa outra plataforma de hospedagem de código, as tarefas de liderança do projeto são as mesmas, mas a maneira de completá-las pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que o [gerente de grupo](group-manager-tasks.md) e o líder da [equipe](team-lead-tasks.md) configuraram os seguintes recursos e permissões:

- A **organização** Azure DevOps para sua unidade de dados
- Um **projeto** de equipe para sua equipe de ciência de dados
- Modelo de equipe e **repositórios** de utilidades
- **Permissões** na conta da sua organização para você criar e editar repositórios para o seu projeto

Para clonar repositórios e modificar conteúdo em sua máquina local ou Na Máquina Virtual de Data Science (DSVM), ou configurar o armazenamento de arquivos Do Zure e montá-lo no seu DSVM, você também precisa considerar esta lista de verificação:

- Uma assinatura do Azure.
- Git instalado em sua máquina. Se você estiver usando um DSVM, o Git está pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux criado e configurado no Azure. Para obter mais informações e instruções, consulte a [Documentação](/azure/machine-learning/data-science-virtual-machine/)da Máquina Virtual de Ciência de Dados .
- Para um Windows DSVM, [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em sua máquina. No arquivo *README.md,* desça até a seção **Baixar e Instalar** e selecione o **instalador mais recente**. Baixe o instalador *.exe* na página do instalador e execute-o. 
- Para um DSVM Linux, uma chave pública SSH configurada em seu DSVM e adicionada no Azure DevOps. Para obter mais informações e instruções, consulte a seção **Criar chave pública SSH** nas plataformas e no [apêndice de ferramentas](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Crie um repositório de projetos em seu projeto de equipe

Para criar um repositório de projetos no projeto **MyTeam** da sua equipe:

1. Acesse a página de **resumo** do projeto da sua equipe em https: *\//\<nome do servidor>/\<nome da organização>/\<nome da equipe>, *por exemplo, **https:\//dev.azure.com/DataScienceUnit/MyTeam**, e selecione **Repos** na navegação esquerda. 
   
1. Selecione o nome do repositório na parte superior da página e, em seguida, selecione **Novo repositório** a partir do dropdown.
   
   ![Selecione Novo repositório](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. Na criação de uma nova caixa de diálogo **de repositório,** certifique-se de que **o Git** está selecionado em **Tipo**. Digite *DSProject1* em **nome do Repositório**e selecione **Criar**.
   
   ![Criar repositório](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Confirme se você pode ver o novo repositório **DSProject1** na página de configurações do projeto. 
   
   ![Repositório de projetos em Configurações de Projeto](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importe o modelo de equipe para o repositório do projeto

Para preencher o repositório do projeto com o conteúdo do repositório do modelo da sua equipe:

1. Na página **Resumo** do projeto da sua equipe, selecione **Repos** na navegação à esquerda. 
   
1. Selecione o nome do repositório na parte superior da página e selecione **DSProject1** na ativa.
   
1. Na página **DSProject1 está vazia,** selecione **Importar**. 
   
   ![Selecione Importar](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. Na **caixa de diálogo Importar um repositório Git,** selecione **Git** como **o tipo De Origem**e digite a URL do repositório **TeamTemplate** em **Clone URL**. O URL é *\//\<https:\<nome do\<servidor>/ nome\<da organização>/ nome da equipe>/_git/ nome do modelo de equipe>*. Por exemplo: **\/https: /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Selecione **Importar**. O conteúdo do repositório de modelo de sua equipe é importado para o repositório do projeto. 
   
   ![Repositório de modelo de equipe de importação](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Se você precisar personalizar o conteúdo do repositório do projeto para atender às necessidades específicas do seu projeto, você pode adicionar, excluir ou modificar arquivos e pastas de repositório. Você pode trabalhar diretamente nos Azure Repos, ou clonar o repositório para sua máquina local ou DSVM, fazer alterações e comprometer e empurrar suas atualizações para o repositório de projeto compartilhado. Siga as instruções em [Personalizar o conteúdo dos repositórios da equipe](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Próximas etapas

Aqui estão links para descrições detalhadas das outras funções e tarefas definidas pelo Processo de Ciência de Dados da Equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Tarefas individuais de contribuinte para uma equipe de ciência de dados](project-ic-tasks.md)
