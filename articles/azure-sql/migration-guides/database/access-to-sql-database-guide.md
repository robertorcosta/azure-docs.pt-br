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
ms.openlocfilehash: 99e0fd3665a0269710a9b0994a1340745f77236f
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027305"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guia de migração: acesso ao banco de dados SQL do Azure

Este guia de migração ensina você a migrar seus bancos de dados do Microsoft Access para o Azure SQL usando o Assistente de Migração do SQL Server para acesso.

Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar seu banco de dados do Access para o banco de dados SQL do Azure, você precisa:

- Verificar se seu ambiente de origem é compatível. 
- [Assistente de Migração do SQL Server para Access](https://www.microsoft.com/download/details.aspx?id=54255). 
- Conectividade e permissões suficientes para acessar a origem e o destino. 


## <a name="pre-migration"></a>Pré-migração

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da migração.


### <a name="assess"></a>Avaliar 

Use o Assistente de Migração do SQL Server (SSMA) para acesso a revisar objetos e dados do banco de dados e avaliar os bancos de dado para a migração. 

Para criar uma avaliação, siga estas etapas: 

1. Abra o [Assistente de Migração do SQL Server para Access](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Selecione **Arquivo** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar seu projeto e, em seguida, selecione banco de dados SQL do Azure como o destino de migração no menu suspenso. Selecione **OK**:

   ![Escolher novo projeto](./media/access-to-sql-database-guide/new-project.png)

1. Selecione **Adicionar bancos de dados** e escolha os bancos de dados a serem adicionados ao novo projeto:

   ![Escolha Adicionar bancos de dados](./media/access-to-sql-database-guide/add-databases.png)

1. No **Gerenciador de metadados do Access**, clique com o botão direito do mouse no banco de dados e escolha **criar relatório**. Como alternativa, você pode escolher **criar relatório** na barra de navegação depois de selecionar o esquema:

   ![Clique com o botão direito do mouse no banco de dados e escolha criar relatório](./media/access-to-sql-database-guide/create-report.png)

1. Examine o relatório HTML para entender as estatísticas de conversão e outros erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário de objetos do Access e o esforço necessário para executar conversões de esquema. O local padrão do relatório está na pasta de relatório dentro de SSMAProjects

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Examinar a avaliação do relatório de exemplo](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso, siga estas etapas:

1. Selecione **Ferramentas** no menu. 
1. Selecione **Configurações do Projeto**. 
1. Selecione a guia **mapeamentos de tipo** :

   ![Mapeamentos de tipo](./media/access-to-sql-database-guide/type-mappings.png)

1. Você pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no **Gerenciador de metadados do Access**.


### <a name="convert-schema"></a>Converter esquema

Para converter objetos de banco de dados, siga estas etapas: 

1. Selecione **conectar ao banco de dados SQL do Azure**. 
    1. Insira os detalhes de conexão para conectar seu banco de dados no banco de dados SQL do Azure.
    1. Escolha o banco de dados SQL de destino na lista suspensa ou forneça um novo nome, caso em que um banco de dados será criado no servidor de destino. 
    1. Forneça detalhes de autenticação. 
    1. Selecione **conectar**:

   ![Conectar-se ao Banco de Dados SQL do Azure](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Clique com o botão direito do mouse no banco de dados em **Gerenciador de Metadados do Access** e escolha **Converter esquema**. Como alternativa, você pode escolher **converter esquema** na barra de navegação superior depois de selecionar o banco de dados:

   ![Clique com o botão direito do mouse no banco de dados e escolha converter esquema](./media/access-to-sql-database-guide/convert-schema.png)
   

1. Após a conclusão da conversão, compare e revise os objetos convertidos para os objetos originais para identificar possíveis problemas e solucioná-los com base nas recomendações:

   ![Os objetos convertidos podem ser comparados com a origem](./media/access-to-sql-database-guide/table-comparison.png)

   Compare o texto Transact-SQL convertido com o código original e examine as recomendações:

   ![As consultas convertidas podem ser comparadas com o código-fonte](./media/access-to-sql-database-guide/query-comparison.png)

1. (Opcional) Para converter um objeto individual, clique com o botão direito do mouse no objeto e escolha **Converter esquema**. Os objetos convertidos aparecem em negrito no **Gerenciador de metadados do Access**: 

   ![Objetos em negrito no Gerenciador de metadados foram convertidos](./media/access-to-sql-database-guide/converted-items.png)
 
1. Selecione **Examinar resultados** no painel Saída e examine os erros no painel **Lista de erros**. 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Selecione **Salvar Projeto** no menu **Arquivo**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no banco de dados SQL.


## <a name="migrate"></a>Migrar

Depois de concluir a avaliação de seus bancos de dados e resolver quaisquer discrepâncias, a próxima etapa é executar o processo de migração. A migração de dados é uma operação de carregamento em massa que move linhas de dados para o banco de dado SQL do Azure em transações. O número de linhas a serem carregadas no banco de dados SQL do Azure em cada transação é definido nas configurações do projeto.

Para publicar o esquema e migrar os dados usando o SSMA para Access, siga estas etapas: 

1. Se você ainda não fez isso, selecione **conectar ao banco de dados SQL do Azure** e forneça detalhes de conexão. 
1. Publicar o esquema: clique com o botão direito do mouse no banco de dados do **Gerenciador de metadados do banco de dados SQL do Azure** e escolha **sincronizar com Banco de dados**. Esta ação publica o esquema MySQL no banco de dados SQL do Azure:

   ![Sincronizar com Banco de dados](./media/access-to-sql-database-guide/synchronize-with-database.png)

   Examine o mapeamento entre seu projeto de origem e seu destino:

   ![Examinar a sincronização com o banco de dados](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Migrar os dados: clique com o botão direito do mouse no banco ou no objeto que você deseja migrar no **Gerenciador de metadados do Access** e escolha **migrar dados**. Como alternativa, você pode selecionar **Migrar Dados** na barra de navegação de linha superior. Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dados, expanda Tabelas e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção:

    ![Migrar dados](./media/access-to-sql-database-guide/migrate-data.png)

1. Após a conclusão da migração, exiba o **Relatório da Migração de Dados**:  

    ![Migrar análise de dados](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Conecte-se ao banco de dados SQL do Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração revisando os data e o esquema:

   ![Validar no SSMA](./media/access-to-sql-database-guide/validate-data.png)



## <a name="post-migration"></a>Pós-migração 

Depois de concluir com êxito o estágio de **migração**, você precisará passar por uma série de tarefas de pós-migração para garantir que tudo esteja funcionando da maneira mais estável e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.

### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste na execução das seguintes atividades:

  1. **Desenvolver testes de validação**. Para testar a migração do banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.

  2. **Configurar ambiente de teste**. O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.

  3. **Executar testes de validação**. Execute os testes de validação na origem e no destino e, em seguida, analise os resultados.

  4. **Executar testes de desempenho**. Execute o teste de desempenho na origem e no destino e, em seguida, analise e compare os resultados.

### <a name="optimize"></a>Otimizar

A fase de pós-migração é crucial para reconciliar problemas de precisão de dados e verificar a integridade dos dados, além de resolver problemas de desempenho com a carga de trabalho.

Para obter detalhes adicionais sobre esses problemas e etapas específicas para atenuá-los, confira o [Guia de validação e otimização após a migração](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional com a conclusão desse cenário de migração, confira os recursos a seguir, que foram desenvolvidos para dar suporte a um projeto de migração do mundo real.

| **Título/link**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e Ferramenta de Avaliação de Carga de Trabalho de Dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam muito a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado. |


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

