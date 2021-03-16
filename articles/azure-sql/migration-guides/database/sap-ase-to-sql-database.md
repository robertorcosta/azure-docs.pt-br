---
title: 'SAP ASE para banco de dados SQL do Azure: guia de migração'
description: Este guia ensina você a migrar seus bancos de dados do SAP ASE para o Azure SQL usando o Assistente de Migração do SQL Server para SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564780"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guia de migração: SAP ASE para banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina você a migrar seus bancos de dados do SAP ASE para o Azure SQL usando o Assistente de Migração do SQL Server para SAP Adapter Server Enterprise.

Para obter outros guias de migração, confira [Migração de banco de dados](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar seu banco de dados SAP SE para o banco de dados SQL do Azure, você precisa de:

- verificar se seu ambiente de origem é compatível. 
- [Assistente de migração do SQL Server para SAP Adaptive Server Enterprise (anteriormente conhecido como SAP Sybase ase)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>Pré-migração

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da migração.

### <a name="assess"></a>Avaliar

Use o [Assistente de migração do SQL Server (SSMA) para SAP Adaptive Server Enterprise (em formalmente, o SAP Sybase ase)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) para examinar objetos e dados do banco de dados, avaliar os bancos de dado para migração, migrar objetos de banco do Para saber mais, consulte [Assistente de migração do SQL Server para Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Para criar uma avaliação, siga estas etapas: 

1. Abra o **SSMA para Sybase**. 
1. Selecione **Arquivo** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar seu projeto e, em seguida, selecione banco de dados SQL do Azure como o destino de migração no menu suspenso. Selecione **OK**.
1. Insira os valores dos detalhes de conexão do SAP na caixa de diálogo **conectar ao Sybase** . 
1. Clique com o botão direito do mouse no banco de dados SAP que você deseja migrar e escolha **criar relatório**. Isso gera um relatório HTML.
1. Examine o relatório HTML para entender as estatísticas de conversão e outros erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário de objetos do DB2 e o esforço necessário para executar as conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Validar mapeamentos de tipo

Antes de executar a conversão de esquema, valide os mapeamentos de tipo de dados padrão ou altere-os com base nos requisitos. Você pode fazer isso navegando até o menu **ferramentas** e escolhendo **configurações do projeto** ou pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no **Gerenciador de metadados do SAP ase**.


### <a name="convert-schema"></a>Converter esquema

Para converter o esquema, siga estas etapas:

1. Adicional Para converter consultas dinâmicas ou ad hoc, clique com o botão direito do mouse no nó e escolha **Adicionar instrução**. 
1. Selecione **conectar-se ao banco de dados SQL do Azure** na barra de navegação de linha superior e forneça detalhes do banco de dados SQL do Azure. Você pode optar por se conectar a um banco de dados existente ou fornecer um novo nome, caso em que um banco de dados será criado no servidor de destino.
1. Clique com o botão direito do mouse no esquema do SAP ASE no **Gerenciador de metadados do Sybase** e escolha **converter esquema**. Como alternativa, você pode selecionar **converter esquema** na barra de navegação de linha superior. 
1. Compare e revise a estrutura do esquema para identificar possíveis problemas. 

   Após a conversão do esquema, você pode salvar este projeto localmente para um exercício de correção de esquema offline. Selecione **Salvar Projeto** no menu **Arquivo**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no banco de dados SQL do Azure.

Para saber mais, consulte [converter esquema](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql)


## <a name="migrate"></a>Migrar 

Depois de ter os pré-requisitos necessários em vigor e concluir as tarefas associadas **ao estágio de pré-migração,** você estará pronto para executar a migração de dados e de esquema.

Para publicar o esquema e migrar os dados, siga estas etapas: 

1. Clique com o botão direito no banco de dados no **Gerenciador de metadados do banco de dados SQL do Azure** e escolha **sincronizar com Banco de dados**  Essa ação publica o esquema do SAP ASE na instância do banco de dados SQL do Azure.
1. Clique com o botão direito do mouse no esquema do SAP ASE no **Gerenciador de metadados do SAP ase** e escolha **migrar dados**.  Como alternativa, você pode selecionar **migrar dados** da barra de navegação de linha superior.  
1. Após a conclusão da migração, exiba o **relatório de migração de dados**: 
1. Valide a migração examinando os dados e o esquema na instância do banco de dados SQL do Azure usando o SSMS (Azure SQL Database Management Studio).


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

> [!NOTE]
> Para obter detalhes adicionais sobre esses problemas e etapas específicas para atenuá-los, consulte o [Guia de validação e otimização após a migração](/sql/relational-databases/post-migration-validation-and-optimization-guide).


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
