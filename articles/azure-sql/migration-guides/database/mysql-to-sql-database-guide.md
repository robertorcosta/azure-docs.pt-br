---
title: 'MySQL para banco de dados SQL do Azure: guia de migração'
description: Este guia ensina você a migrar seus bancos de dados MySQL para o Azure SQL usando o Assistente de Migração do SQL Server para MySQL (SSMA para MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 6b8d3afc214f6b78fcc11b3592cd51dadf37bf96
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564136"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Guia de migração: MySQL para banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina a migrar seu banco de dados MySQL para o banco de dados SQL do Azure usando o Assistente de Migração do SQL Server para MySQL (SSMA para MySQL). 

Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar seu banco de dados MySQL para o banco de dados SQL do Azure, você precisa de:

- Verificar se seu ambiente de origem é compatível. Atualmente, há suporte para MySQL 5,6 e 5,7. 
- [Assistente de Migração do SQL Server para MySQL](https://www.microsoft.com/download/details.aspx?id=54257)
- Conectividade e permissões suficientes para acessar a origem e o destino. 


## <a name="pre-migration"></a>Pré-migração 

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da migração.

### <a name="assess"></a>Avaliar 

Use o Assistente de Migração do SQL Server (SSMA) para MySQL para revisar objetos de banco de dados e data e avaliar os bancos de dado para migração. 

Para criar uma avaliação, execute as seguintes etapas: 

1. Abra [Assistente de migração do SQL Server para MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Selecione **arquivo** no menu e, em seguida, escolha **novo projeto**. 
1. Forneça o nome do projeto, um local para salvar seu projeto. Escolha **banco de dados SQL do Azure** como o destino de migração. Selecione **OK**:

   ![Novo Projeto](./media/mysql-to-sql-database-guide/new-project.png)

1. Escolha **conectar-se ao MySQL** e forneça os detalhes de conexão para conectar seu servidor MySQL:

   ![Conectar-se ao MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Clique com o botão direito do mouse no esquema MySQL no **Gerenciador de metadados MySQL** e escolha **criar relatório**. Como alternativa, você pode selecionar **criar relatório** na barra de navegação de linha superior:

   ![Criar Relatório](./media/mysql-to-sql-database-guide/create-report.png)

1. Examine o relatório HTML para entender as estatísticas de conversão e outros erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário de objetos MySQL e o esforço necessário para executar conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects.
 
   Por exemplo: `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Relatório de conversão](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso, siga estas etapas: 

1. Selecione **Ferramentas** no menu. 
1. Selecione **Configurações do Projeto**. 
1. Selecione a guia **mapeamentos de tipo** :

   ![Mapeamentos de tipo](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Você pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no **Gerenciador de metadados do MySQL**. 

### <a name="convert-schema"></a>Converter esquema 

Para converter o esquema, siga estas etapas: 

1. (Opcional) Para converter consultas dinâmicas ou ad hoc, clique com o botão direito do mouse no nó e escolha **Adicionar instrução**. 
1. Selecione **conectar ao banco de dados SQL do Azure**. 
    1. Insira os detalhes de conexão para conectar seu banco de dados no banco de dados SQL do Azure.
    1. Escolha o banco de dados SQL de destino na lista suspensa ou forneça um novo nome, caso em que um banco de dados será criado no servidor de destino. 
    1. Forneça detalhes de autenticação. 
    1. Selecione **conectar**:

   ![Conectar-se ao SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Clique com o botão direito do mouse no esquema e escolha **converter esquema**. Como alternativa, você pode escolher **converter esquema** na barra de navegação de linha superior depois de escolher o banco de dados:

   ![Converter esquema](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Após a conclusão da conversão, compare e revise os objetos convertidos para os objetos originais para identificar possíveis problemas e solucioná-los com base nas recomendações:

   ![Os objetos convertidos podem ser comparados com a origem](./media/mysql-to-sql-database-guide/table-comparison.png)

   Compare o texto Transact-SQL convertido com o código original e examine as recomendações:

   ![As consultas convertidas podem ser comparadas com o código-fonte](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Selecione **Examinar resultados** no painel Saída e examine os erros no painel **Lista de erros**. 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Selecione **Salvar Projeto** no menu **Arquivo**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no banco de dados SQL.



## <a name="migrate"></a>Migrar 

Depois de concluir a avaliação de seus bancos de dados e resolver quaisquer discrepâncias, a próxima etapa é executar o processo de migração. A migração envolve duas etapas: publicação do esquema e migração dos dados. 

Para publicar o esquema e migrar os dados, siga estas etapas: 

1. Publicar o esquema: clique com o botão direito do mouse no banco de dados do **Gerenciador de metadados do banco de dados SQL do Azure** e escolha **sincronizar com Banco de dados**. Esta ação publica o esquema MySQL no banco de dados SQL do Azure:

   ![Sincronizar com Banco de dados](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Examine o mapeamento entre seu projeto de origem e seu destino:

   ![Sincronizar com a revisão do banco de dados](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrar os dados: clique com o botão direito do mouse no objeto ou banco de dados que você deseja migrar no **Gerenciador de metadados MySQL** e escolha **migrar dados**. Como alternativa, você pode selecionar **Migrar Dados** na barra de navegação de linha superior. Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dados, expanda Tabelas e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção:

   ![Migrar dados](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Após a conclusão da migração, exiba o relatório de **migração de dados** : 

   ![Relatório de migração de dados](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Conecte-se ao banco de dados SQL do Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração revisando os data e o esquema:

    ![Validar no SSMA](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



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

| Título/link     | Description    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Modelo e Ferramenta de Avaliação de Carga de Trabalho de Dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam muito a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado. |

Esses recursos foram desenvolvidos como parte do programa Data SQL Ninja, que é patrocinado pela equipe de engenharia do Grupo de Dados do Azure. A principal responsabilidade do programa Data SQL Ninja é desbloquear e acelerar as oportunidades complexas e diversas de migração da plataforma de dados para a plataforma de Dados do Azure da Microsoft. Se você acredita que sua organização tem interesse em participar do programa Data SQL Ninja, entre em contato com sua equipe de contas e peça que eles enviem uma indicação.

## <a name="next-steps"></a>Próximas etapas 

- Certifique-se de conferir a [calculadora de TCO (custo total de propriedade) do Azure](https://aka.ms/azure-tco) para ajudar a estimar a economia de custos que você pode obter ao migrar suas cargas de trabalho para o Azure.

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudar você com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, confira o artigo [Serviços e ferramentas para migração de dados](../../../dms/dms-tools-matrix.md).

- Para obter outros guias de migração, confira [Migração de banco de dados](https://datamigration.microsoft.com/). 

Para vídeos, confira: 
- [Visão geral da jornada de migração e das ferramentas/serviços recomendados para executar a avaliação e a migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)