---
title: 'Acesso ao banco de dados SQL do Azure: guia de migração'
description: Este guia ensina você a migrar seus bancos de dados do Microsoft Access para o Azure SQL usando o Assistente de Migração do SQL Server para acesso (SSMA para Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 9b64dc95c6ee00a834c2741b30026df7350780c0
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564783"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guia de migração: acesso ao banco de dados SQL do Azure

Este guia de migração ensina você a migrar seus bancos de dados do Microsoft Access para o Azure SQL usando o Assistente de Migração do SQL Server para acesso.

Para obter outros guias de migração, confira [Migração de banco de dados](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar seu banco de dados do Access para o banco de dados SQL do Azure, você precisa:

- verificar se seu ambiente de origem é compatível. 
- [Assistente de migração do SQL Server para acesso](https://www.microsoft.com/download/details.aspx?id=54255). 

## <a name="pre-migration"></a>Pré-migração

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da migração.


### <a name="assess"></a>Avaliar 

Crie uma avaliação usando o [Assistente de migração do SQL Server para acesso](https://www.microsoft.com/download/details.aspx?id=54255). 

Para criar uma avaliação, siga estas etapas: 

1. Abra Assistente de Migração do SQL Server para acesso. 
1. Selecione **Arquivo** e, em seguida, escolha **Novo Projeto**. Forneça um nome para o projeto de migração. 
1. Selecione **Adicionar bancos de dados** e escolha os bancos de dados a serem adicionados ao novo projeto
1. No **Gerenciador de metadados do Access**, clique com o botão direito do mouse no banco de dados e escolha **criar relatório**. 
1. Examine a avaliação de exemplo. Por exemplo: 

### <a name="convert-schema"></a>Converter esquema

Para converter objetos de banco de dados, siga estas etapas: 

1. Selecione **conectar-se ao banco de dados SQL do Azure** e fornecer detalhes de conexão.
1. Clique com o botão direito do mouse no banco de dados no **Gerenciador de metadados do Access** e escolha **converter esquema**.  
1. Adicional Para converter um objeto individual, clique com o botão direito do mouse no objeto e escolha **converter esquema**. Um objeto que foi convertido aparece em negrito no **Gerenciador de metadados do Access**: 
1. Selecione **examinar resultados** no painel saída e examine os erros no painel **lista de erros** . 


## <a name="migrate"></a>Migrar

Depois de concluir a avaliação de seus bancos de dados e resolver quaisquer discrepâncias, a próxima etapa é executar o processo de migração. A migração de dados é uma operação de carregamento em massa que move linhas de dados para o banco de dado SQL do Azure em transações. O número de linhas a serem carregadas no banco de dados SQL do Azure em cada transação é definido nas configurações do projeto.

Para migrar dados usando o SSMA para Access, siga estas etapas: 

1. Se você ainda não fez isso, selecione **conectar ao banco de dados SQL do Azure** e forneça detalhes de conexão. 
1. Clique com o botão direito do mouse no banco de dados do **Gerenciador de metadados do banco de dados SQL do Azure** e escolha **sincronizar com o banco** Essa ação publica o esquema MySQL no banco de dados SQL do Azure.
1. Use o **Gerenciador de metadados do Access** para marcar as caixas ao lado dos itens que você deseja migrar. Se você quiser migrar todo o banco de dados, marque a caixa ao lado do banco de dados. 
1. Clique com o botão direito do mouse no banco de dados ou objeto que você deseja migrar e escolha **migrar data**. 
   Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dado, expanda tabelas e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção.

## <a name="post-migration"></a>Pós-migração 

Depois de concluir com êxito o estágio de **migração** , você precisa passar por uma série de tarefas de pós-implantação para garantir que tudo esteja funcionando da maneira mais tranqüila e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.

### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste em executar as seguintes atividades:

  1. **Desenvolver testes de validação**. Para testar a migração de banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.

  2. **Configure o ambiente de teste**. O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.

  3. **Executar testes de validação**. Execute os testes de validação em relação à origem e ao destino e, em seguida, analise os resultados.

  4. **Executar testes de desempenho**. Execute o teste de desempenho em relação à origem e ao destino e, em seguida, analise e compare os resultados.

### <a name="optimize"></a>Otimizar

A fase de pós-migração é crucial para reconciliar quaisquer problemas de precisão de dados e verificar a integridade, bem como resolver problemas de desempenho com a carga de trabalho.

Para obter detalhes adicionais sobre esses problemas e etapas específicas para atenuá-los, consulte o [Guia de validação e otimização após a migração](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional com a conclusão deste cenário de migração, consulte os recursos a seguir, que foram desenvolvidos para dar suporte a um compromisso de projeto de migração real.

| **Título/link**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta fornece as melhores plataformas de destino "melhor ajuste", preparação para a nuvem e nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ele oferece um cálculo simples, com um clique e geração de relatórios que ajuda muito a acelerar as avaliações de grandes imóveis, fornecendo um processo de decisão de plataforma de destino automatizado e uniforme. |


Esses recursos foram desenvolvidos como parte do programa Data SQL Ninja, que é patrocinado pela equipe de engenharia do Grupo de Dados do Azure. A principal responsabilidade do programa Data SQL Ninja é desbloquear e acelerar as oportunidades complexas e diversas de migração da plataforma de dados para a plataforma de Dados do Azure da Microsoft. Se você acredita que sua organização tem interesse em participar do programa Data SQL Ninja, entre em contato com sua equipe de contas e peça que eles enviem uma indicação.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o Banco de Dados SQL do Azure, confira:
   - [Uma visão geral do banco de dados SQL](../../database/sql-database-paas-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).

