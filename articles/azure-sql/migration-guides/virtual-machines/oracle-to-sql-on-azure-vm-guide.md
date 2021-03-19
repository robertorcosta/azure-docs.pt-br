---
title: 'Oracle para SQL Server na VM do Azure: guia de migração'
description: Este guia ensina a migrar seus esquemas Oracle para SQL Server em VMs do Azure usando Assistente de Migração do SQL Server para Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 1767f1f990326e513393b8ce47e1ed8485f73849
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656321"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Guia de migração: Oracle para SQL Server na VM do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina a migrar seus esquemas Oracle para SQL Server na VM do Azure usando Assistente de Migração do SQL Server para Oracle. 

Para outros cenários, consulte o [Guia de migração de banco de dados](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar seu esquema Oracle para SQL Server na VM do Azure, você precisa de:

- Para verificar se há suporte para o ambiente de origem.
- Para baixar o [Assistente de migração do SQL Server (SSMA) para Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Uma [VM de SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md)de destino.
- As [permissões necessárias para o SSMA para Oracle e para](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) o [provedor](/sql/ssma/oracle/connect-to-oracle-oracletosql).

## <a name="pre-migration"></a>Pré-migração

Ao se preparar para migrar para a nuvem, verifique se o seu ambiente de origem tem suporte e se você resolveu todos os pré-requisitos. Isso ajudará a garantir uma migração eficiente e bem-sucedida.

Essa parte do processo envolve a condução de um inventário dos bancos de dados que você precisa migrar, avaliar esses bancos de dados quanto a possíveis problemas de migração ou bloqueadores e, em seguida, resolver quaisquer itens que você possa ter descoberto. 

### <a name="discover"></a>Descobrir

Use o [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) para identificar as fontes de dados existentes e os detalhes sobre os recursos que estão sendo usados pela sua empresa para obter uma compreensão melhor e planejar a migração. Esse processo envolve a verificação da rede para identificar todas as instâncias do Oracle da sua organização junto com a versão e os recursos em uso.

Para usar o MAP Toolkit para executar uma verificação de inventário, siga estas etapas: 

1. Abra o [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Selecione **criar/selecionar banco de dados**.

   ![Selecionar banco de dados](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Selecione **criar um banco de dados de inventário**, insira um nome para o novo banco de dados de inventário que você está criando, forneça uma breve descrição e, em seguida, selecione **OK**. 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Criar um banco de dados de inventário":::

1. Selecione **coletar dados de inventário** para abrir o **Assistente de inventário e avaliação**. 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Coletar dados de inventário":::

1. No **Assistente de inventário e avaliação**, escolha **Oracle** e, em seguida, selecione **Avançar**. 

   ![Escolha o Oracle](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Escolha a opção de pesquisa de computador mais adequada às suas necessidades de negócios e ao seu ambiente e, em seguida, selecione **Avançar**: 

   ![Escolha a opção de pesquisa de computador mais adequada às suas necessidades de negócios](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Insira as credenciais ou crie novas credenciais para os sistemas que você deseja explorar e, em seguida, selecione **Avançar**.

    ![Inserir as credenciais](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)

1. Defina a ordem das credenciais e, em seguida, selecione **Avançar**. 

   ![Definir ordem de credencial](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  

1. Especifique as credenciais para cada computador que você deseja descobrir. Você pode usar credenciais exclusivas para todos os computadores/máquinas, ou pode optar por usar a lista **todas as credenciais do computador** .  


   ![Especifique as credenciais para cada computador que você deseja descobrir](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Verifique seu resumo de seleção e, em seguida, selecione **concluir**.

   ![Resumo da revisão](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)

1. Depois que a verificação for concluída, exiba o relatório Resumo da **coleta de dados** . A verificação pode levar alguns minutos e depende do número de bancos de dados. Selecione **fechar** quando terminar. 

   ![Relatório de resumo da coleção](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Selecione **Opções** para gerar um relatório sobre os detalhes da avaliação e do banco de dados Oracle. Selecione ambas as opções (uma a uma) para gerar o relatório.


### <a name="assess"></a>Avaliar

Depois de identificar as fontes de dados, use o [Assistente de migração do SQL Server (SSMA) para Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) para avaliar as instâncias do Oracle migrando para a VM SQL Server para que você compreenda as lacunas entre as duas. Usando o assistente de migração, você pode examinar os objetos e os dados do banco de dados, avaliar os bancos de dado para migração, migrar objetos de banco para SQL Server e, em seguida, migrar dados para SQL Server.

Para criar uma avaliação, siga estas etapas: 

1. Abra o  [Assistente de migração do SQL Server (SSMA) para Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecione **Arquivo** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar o projeto e, em seguida, selecione um destino de migração do SQL Server na lista suspensa. Selecione **OK**. 

   ![Novo Projeto](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)

1. Selecione **conectar-se ao Oracle**. Insira valores nos detalhes da conexão Oracle na caixa de diálogo **conectar ao Oracle** .

   ![Conectar-se ao Oracle](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Selecione os esquemas do Oracle que você deseja migrar: 

   ![Selecionar esquema Oracle](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)

1. Clique com o botão direito do mouse no esquema Oracle que você deseja migrar no **Gerenciador de metadados Oracle** e escolha **criar relatório**. Isso vai gerar um relatório HTML. Como alternativa, você pode escolher **criar relatório** na barra de navegação depois de selecionar o banco de dados.

   ![Criar Relatório](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. No **Gerenciador de metadados Oracle**, selecione o esquema Oracle e, em seguida, selecione **criar relatório** para gerar um relatório HTML com estatísticas de conversão e erros/avisos, se houver.
1. Examine o relatório HTML para obter as estatísticas de conversão, bem como erros e avisos. Analise-o para entender problemas e resoluções de conversão.

   Esse relatório também pode ser acessado na pasta projetos do SSMA, conforme selecionado na primeira tela. No exemplo acima, localize o arquivo de report.xml de: 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

    e, em seguida, abri-lo no Excel para obter um inventário dos objetos Oracle e o esforço necessário para executar conversões de esquema.

   ![Relatório de conversão](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)



### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso, siga estas etapas: 

1. Selecione **Ferramentas** no menu. 
1. Selecione **Configurações do Projeto**. 
1. Selecione a guia **Mapeamentos de tipo**. 

   ![Mapeamentos de tipo](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Você pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no **Gerenciador de metadados Oracle**. 



### <a name="convert-schema"></a>Converter esquema

Para converter o esquema, siga estas etapas: 

1. Adicional Para converter consultas dinâmicas ou ad hoc, clique com o botão direito do mouse no nó e escolha **Adicionar instrução**.
1. Escolha **conectar-se a SQL Server** na barra de navegação de linha superior e forneça detalhes de conexão para seu SQL Server na VM do Azure. Você pode optar por se conectar a um banco de dados existente ou fornecer um novo nome, caso em que um banco de dados será criado no servidor de destino.

   ![Conectar-se ao SQL](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Clique com o botão direito do mouse no esquema Oracle no **Gerenciador de metadados Oracle** e escolha **converter esquema**.

   ![Converter esquema](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)

1. Após a conclusão da conversão do esquema, compare e examine a estrutura do esquema para identificar possíveis problemas.

   ![Revisar recomendações](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Compare o texto Transact-SQL convertido com os procedimentos armazenados originais e examine as recomendações: 

   ![Examinar o código de recomendações](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Você pode salvar o projeto localmente para um exercício de correção de esquema offline. Você pode fazer isso selecionando **salvar projeto** no menu **arquivo** . Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema para SQL Server.


## <a name="migrate"></a>Migrações

Depois de ter os pré-requisitos necessários em vigor e concluir as tarefas associadas **ao estágio de pré-migração,** você estará pronto para executar a migração de dados e de esquema. A migração envolve duas etapas – publicação do esquema e migração dos dados. 


Para publicar o esquema e migrar os dados, siga estas etapas: 

1. Clique com o botão direito do mouse no banco de dados do **SQL Server Gerenciador de metadados**  e escolha **sincronizar com Banco de dados**. Esta ação publica o esquema Oracle para SQL Server na VM do Azure. 

   ![Sincronizar com Banco de dados](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Examine o status da sincronização: 

   ![Examinar o status da sincronização](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)


1. Clique com o botão direito do mouse no esquema Oracle no **Gerenciador de metadados Oracle** e escolha **migrar dados**. Como alternativa, você pode selecionar migrar dados da navegação de linha superior.

   ![Migrar dados](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Forneça detalhes de conexão para Oracle e SQL Server na VM do Azure na caixa de diálogo.
1. Após a conclusão da migração, exiba o relatório de migração de dados:

    ![Relatório de migração de dados](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Conecte-se ao seu SQL Server na VM do Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) para examinar dados e esquema em sua instância do SQL Server. 

   ![Validar no SSMA](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)




Além de usar o SSMA, você também pode usar SQL Server Integration Services (SSIS) para migrar os dados. Para obter mais informações, consulte: 
- O artigo [introdução com SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- O white paper [SQL Server Integration Services: SSIS para o Azure e a movimentação de dados híbridos](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>Pós-migração 

Depois de concluir com êxito o estágio de **migração** , você precisa passar por uma série de tarefas de pós-implantação para garantir que tudo esteja funcionando da maneira mais tranqüila e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.

O [Kit de ferramentas de migração de acesso a dados](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) é uma extensão para Visual Studio Code que permite que você analise seu código-fonte Java e detecte chamadas e consultas à API de acesso a dados, fornecendo a você uma exibição de painel único do que precisa ser resolvido para dar suporte ao novo back-end. Para saber mais, confira o blog [migrar nosso aplicativo Java do Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) . 

### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste em executar as seguintes atividades:

1. **Desenvolver testes de validação**. Para testar a migração de banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.

2. **Configure o ambiente de teste**. O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.

3. **Executar testes de validação**. Execute os testes de validação em relação à origem e ao destino e, em seguida, analise os resultados.

4. **Executar testes de desempenho**. Execute o teste de desempenho em relação à origem e ao destino e, em seguida, analise e compare os resultados.

### <a name="optimize"></a>Otimizar

A fase de pós-migração é crucial para reconciliar quaisquer problemas de precisão de dados e verificar a integridade, bem como resolver problemas de desempenho com a carga de trabalho.

> [!Note]
> Para obter detalhes adicionais sobre esses problemas e etapas específicas para atenuá-los, consulte o [Guia de validação e otimização após a migração](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional com a conclusão deste cenário de migração, consulte os recursos a seguir, que foram desenvolvidos para dar suporte a um compromisso de projeto de migração real.

| **Título/link**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta fornece as melhores plataformas de destino "melhor ajuste", preparação para a nuvem e nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ele oferece um cálculo simples, com um clique e geração de relatórios que ajuda muito a acelerar as avaliações de grandes imóveis, fornecendo um processo de decisão de plataforma de destino automatizado e uniforme.                                                          |
| [Artefatos de script de inventário do Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Esse ativo inclui uma consulta PL/SQL que atinge as tabelas do sistema Oracle e fornece uma contagem de objetos por tipo de esquema, tipo de objeto e status. Ele também fornece uma estimativa aproximada de "dados brutos" em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados em um formato CSV.                                                                                                               |
| [Automatize a coleta de avaliação do Oracle SSMA & consolidação](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Esse conjunto de recursos usa um arquivo. csv como entrada (sources.csv nas pastas do projeto) para produzir os arquivos XML necessários para executar a avaliação do SSMA no modo de console. O source.csv é fornecido pelo cliente com base em um inventário de instâncias existentes do Oracle. Os arquivos de saída são AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [Os erros comuns do SSMA para Oracle e como corrigi-los](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Com o Oracle, você pode atribuir uma condição não escalar na cláusula WHERE. No entanto, SQL Server não dá suporte a esse tipo de condição. Como resultado, o Assistente de Migração do SQL Server (SSMA) para Oracle não converte consultas com uma condição não escalar na cláusula WHERE, em vez disso, gera um erro O2SS0001. Este white paper fornece mais detalhes sobre o problema e as maneiras de resolvê-lo.          |
| [Manual de migração do Oracle para SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Este documento se concentra nas tarefas associadas à migração de um esquema Oracle para a versão mais recente do SQL Server. Se a migração exigir alterações nos recursos/funcionalidades, o possível impacto de cada alteração nos aplicativos que usam o banco de dados deve ser considerado com cuidado.                                                     |

Esses recursos foram desenvolvidos como parte do programa Data SQL Ninja, que é patrocinado pela equipe de engenharia do Grupo de Dados do Azure. A principal responsabilidade do programa Data SQL Ninja é desbloquear e acelerar as oportunidades complexas e diversas de migração da plataforma de dados para a plataforma de Dados do Azure da Microsoft. Se você acredita que sua organização tem interesse em participar do programa Data SQL Ninja, entre em contato com sua equipe de contas e peça que eles enviem uma indicação.

## <a name="next-steps"></a>Próximas etapas

- Para verificar a disponibilidade dos serviços aplicáveis ao SQL Server consulte o [centro de infraestrutura global do Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte o artigo [serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre o SQL Azure, consulte:
   - [Opções de implantação](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server em VMs do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para obter informações sobre licenciamento, consulte
   - [Traga sua própria licença com o Benefício Híbrido do Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Obtenha suporte estendido gratuito para SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).

