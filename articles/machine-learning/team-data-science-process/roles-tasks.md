---
title: Tarefas e funções do Processo de Ciência de Dados de Equipe
description: Um esboço dos principais componentes, funções de pessoal e tarefas associadas para um grupo de ciência de dados.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720003"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tarefas e funções do Processo de Ciência de Dados de Equipe

O Team Data Science Process (TDSP) é uma estrutura desenvolvida pela Microsoft que fornece uma metodologia estruturada para construir eficientemente soluções de análise preditiva e aplicações inteligentes. Este artigo descreve as principais funções de pessoal e tarefas associadas para uma equipe de ciência de dados padronizando esse processo.

Este artigo introdutório vincula-se a tutoriais sobre como configurar o ambiente TDSP. Os tutoriais fornecem orientações detalhadas para o uso de Projetos Azure DevOps, Repositórios Azure e Boards Azure.  O objetivo motivador é passar do conceito para a modelagem e para a implantação.

Os tutoriais usam DevOps do Azure porque é assim que se implementa o TDSP na Microsoft. O Azure DevOps facilita a colaboração integrando segurança baseada em função, gerenciamento e rastreamento de itens de trabalho e hospedagem de códigos, compartilhamento e controle de origem. Os tutoriais também usam uma Máquina Virtual de Data Science (DSVM) do Azure [Data Science](https://aka.ms/dsvm) como desktop de análise, que tem várias ferramentas populares de ciência de dados pré-configuradas e integradas com os serviços microsoft software e Azure. 

Você pode usar os tutoriais para implementar o TDSP usando outras ferramentas e ambientes de hospedagem de código, planejamento ágil e desenvolvimento, mas alguns recursos podem não estar disponíveis.

## <a name="structure-of-data-science-groups-and-teams"></a>Estrutura de grupos e equipes de ciência de dados

As funções de ciência de dados nas empresas são frequentemente organizadas na seguinte hierarquia:

- Grupo de ciência de dados
  - Equipe/s de ciência de dados dentro do grupo

Nessa estrutura, há líderes de grupo e líderes de equipe. Normalmente, um projeto de ciência de dados é feito por uma equipe de ciência de dados. As equipes de ciência de dados têm pistas de projetos para tarefas de gerenciamento e governança de projetos, e cientistas e engenheiros de dados individuais para executar as partes de ciência de dados e engenharia de dados do projeto. A configuração e a governança do projeto inicial são feitas pelo grupo, equipe ou líderes do projeto.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definição e tarefas para as quatro funções do TDSP
Com o pressuposto de que a unidade de ciência de dados é composta por equipes dentro de um grupo, existem quatro funções distintas para o pessoal do TDSP:

1. **Gerente de Grupo**: Gerencia toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada uma trabalhando em vários projetos de ciência de dados em áreas comerciais distintas. Um gerente de grupo pode delegar suas tarefas a um substituto, mas as tarefas associadas à função não são alteradas.
   
2. **Team Lead**: Gerencia uma equipe na unidade de ciência de dados de uma empresa. Uma equipe consiste em vários cientistas de dados. Para uma pequena unidade de ciência de dados, o Gerente de Grupo e o Líder da Equipe podem ser a mesma pessoa.
   
3. **Project Lead**: Gerencia as atividades diárias de cientistas de dados individuais em um projeto específico de ciência de dados.
   
4. **Colaboradores individuais do Projeto**: Cientistas de dados, analistas de negócios, engenheiros de dados, arquitetos e outros que executam um projeto de ciência de dados.

> [!NOTE]
> Dependendo da estrutura e tamanho de uma empresa, uma única pessoa pode desempenhar mais de um papel, ou mais de uma pessoa pode preencher um papel.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Tarefas a serem concluídas pelas quatro funções

O diagrama a seguir mostra as tarefas de alto nível para cada função do Processo de Ciência de Dados da Equipe. Este esquema e o seguinte esboço mais detalhado de tarefas para cada função TDSP podem ajudá-lo a escolher o tutorial que você precisa com base em suas responsabilidades.

![Visão geral das funções e tarefas](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Tarefas do Gerente de Grupo

O Gerenciador de grupo ou um administrador de sistema TDSP designado completa as seguintes tarefas para adotar o TDSP:

- Cria uma **organização** Azure DevOps e um projeto de grupo dentro da organização. 
- Cria um **repositório de modelo de projeto** no projeto do grupo Azure DevOps e o semente do repositório de modelo de projeto desenvolvido pela equipe do Microsoft TDSP. O repositório de modelo de projeto do Microsoft TDSP fornece:
  - Uma **estrutura de diretório padronizada,** incluindo diretórios para dados, códigos e documentos.
  - Um conjunto de **modelos de documentos padronizados** para orientar um processo eficiente de ciência de dados.
- Cria um **repositório de utilitários**e o separa do repositório de utilitários desenvolvido pela equipe do Microsoft TDSP. O repositório de utilitários TDSP da Microsoft fornece um conjunto de utilitários úteis para tornar o trabalho de um cientista de dados mais eficiente. O repositório de utilitários da Microsoft inclui utilitários para exploração interativa de dados, análise, emissão de relatórios e modelagem e relatórios de linha de base.
- Configura a **política de controle de segurança** para a conta da organização.

Para obter instruções detalhadas, consulte [as tarefas do Gerente de Grupo para uma equipe de ciência de dados](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Tarefas do líder da equipe

O Líder de Equipe ou um administrador de projeto designado completa as seguintes tarefas para adotar o TDSP:

- Cria um **projeto** de equipe na organização Azure DevOps do grupo.
- Cria o **repositório de modelo de projeto** no projeto e o separa do repositório de modelo de projeto de grupo criado pelo Gerente de Grupo ou pelo delegado.
- Cria o **repositório de utilitários**da equipe, separa-o do repositório de utilitários do grupo e adiciona utilitários específicos da equipe ao repositório.
- Opcionalmente, cria [o armazenamento de arquivos Do Azure](https://azure.microsoft.com/services/storage/files/) para armazenar ativos de dados úteis para a equipe. Outros membros da equipe podem montar esse armazenamento de arquivo de nuvem compartilhado em suas áreas de trabalho de análise.
- Opcionalmente, monta o armazenamento de arquivos Azure no **DSVM** da equipe e adiciona ativos de dados da equipe a ele.
- Configure o controle de **segurança** adicionando membros da equipe e configurando suas permissões.

Para obter instruções detalhadas, consulte [as tarefas do Team Lead para uma equipe de ciência de dados](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Tarefas do líder de projetos

O Project Lead completa as seguintes tarefas para adotar o TDSP:

- Cria um **repositório** de projeto no projeto da equipe e o semente do repositório de modelo do projeto.
- Opcionalmente, cria **o armazenamento de arquivos Do Azure** para armazenar os ativos de dados do projeto.
- Opcionalmente, monta o armazenamento de arquivos Azure no **DSVM** e adiciona ativos de dados do projeto a ele.
- Configure o controle de **segurança** adicionando membros do projeto e configurando suas permissões.

Para obter instruções detalhadas, consulte [as tarefas do Project Lead para uma equipe de ciência de dados](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Tarefas do colaborador individual do projeto

O Projeto Contribuinte Individual, geralmente um Cientista de Dados, realiza as seguintes tarefas usando o TDSP:

- Clona o **repositório** do projeto criado pelo líder do projeto.
- Opcionalmente, monta a equipe compartilhada e projeta **o armazenamento de arquivos Azure** em sua Máquina Virtual de Data **Science** (DSVM).
- Executa o projeto.

Para obter instruções detalhadas para o embarque em um projeto, consulte [as tarefas do Project Individual Contributor para uma equipe de ciência de dados](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Fluxo de trabalho de execução de projetos de ciência de dados

Seguindo os tutoriais relevantes, cientistas de dados, líderes de projetos e líderes de equipe podem criar itens de trabalho para rastrear todas as tarefas e etapas do projeto do início ao fim. O uso do Azure Repos promove a colaboração entre os cientistas de dados e garante que os artefatos gerados durante a execução do projeto sejam controlados e compartilhados por todos os membros do projeto. O Azure DevOps permite que você conecte seus itens de trabalho do Azure Boards com seus ramos de repositório do Azure Repos e rastreie facilmente o que foi feito para um item de trabalho.

A figura a seguir descreve o fluxo de trabalho TDSP para execução do projeto:

![Fluxo de trabalho típico do projeto de ciência de dados](./media/roles-tasks/overview-project-execute.png)

As etapas do fluxo de trabalho podem ser agrupadas em três atividades:

- Project Leads conduz planejamento de sprint
- Cientistas de dados `git` desenvolvem artefatos em ramos para abordar itens de trabalho
- Os Líderes do Projeto ou outros membros da equipe fazem revisões de código e mesclam ramos de trabalho ao ramo mestre

Para obter instruções detalhadas sobre o fluxo de trabalho de execução de projetos, consulte [O desenvolvimento ágil de projetos de ciência de dados](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Repositório de modelo de projeto TDSP

Use o [repositório](https://github.com/Azure/Azure-TDSP-ProjectTemplate) de modelo de projeto da equipe do Microsoft TDSP para suportar a execução e colaboração eficientes do projeto. O repositório oferece uma estrutura de diretório padronizada e modelos de documentos que você pode usar para seus próprios projetos de TDSP.

## <a name="next-steps"></a>Próximas etapas

Explore as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Projeto Individual Contribuinte tarefas para uma equipe de ciência de dados](project-ic-tasks.md)
