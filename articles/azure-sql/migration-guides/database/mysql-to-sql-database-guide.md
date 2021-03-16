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
ms.openlocfilehash: d59a4fc2cbf52d95e6b5a5100b5ffe06c5dbed7e
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564781"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Guia de migração: MySQL para banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina a migrar seu banco de dados MySQL para o banco de dados SQL do Azure usando o Assistente de Migração do SQL Server para MySQL (SSMA para MySQL). 

Para obter outros guias de migração, confira [Migração de banco de dados](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar seu banco de dados MySQL para o banco de dados SQL do Azure, você precisa de:

- verificar se seu ambiente de origem é compatível. Atualmente, há suporte para MySQL 5,6 e 5,7. 
- [SQL Server Migration Assistant for MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>Pré-migração 

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da migração.

### <a name="assess"></a>Avaliar 

Usando [Assistente de migração do SQL Server para MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257), você pode revisar os objetos e dados do banco e avaliar os bancos de dado para a migração.

Para criar uma avaliação, execute as etapas a seguir.

1. Abra Assistente de Migração do SQL Server para MySQL. 
1. Selecione **arquivo** no menu e, em seguida, escolha **novo projeto**. Forneça o nome do projeto, um local para salvar seu projeto.
1. Escolha **banco de dados SQL do Azure** como o destino de migração. 
1. Escolha **conectar-se ao MySQL** e forneça os detalhes de conexão para conectar o servidor MySQL. 
1. Clique com o botão direito do mouse no esquema MySQL no **Gerenciador de metadados MySQL** e escolha **criar relatório**. Como alternativa, você pode selecionar **criar relatório** na barra de navegação de linha superior. 
1. Examine o relatório HTML para obter as estatísticas de conversão, bem como erros e avisos. Analise-o para entender problemas e resoluções de conversão. 

   Esse relatório também pode ser acessado na pasta projetos do SSMA, conforme selecionado na primeira tela. No exemplo acima, localize o arquivo de report.xml de:
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   e abra-o no Excel para obter um inventário de objetos MySQL e o esforço necessário para executar conversões de esquema.

### <a name="validate-data-types"></a>Validar tipos de dados

Antes de executar a conversão de esquema, valide os mapeamentos de tipo de dados padrão ou altere-os com base nos requisitos. Você pode fazer isso navegando até o menu "ferramentas" e escolhendo "configurações do projeto" ou pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no "Gerenciador de metadados do MySQL".

### <a name="convert-schema"></a>Converter esquema 

Para converter o esquema, siga estas etapas: 

1. Adicional Para converter consultas dinâmicas ou ad hoc, clique com o botão direito do mouse no nó e escolha **Adicionar instrução**. 
1. Escolha **conectar-se ao banco de dados SQL do Azure** na barra de navegação de linha superior e forneça detalhes de conexão. Você pode optar por se conectar a um banco de dados existente ou fornecer um novo nome, caso em que um banco de dados será criado no servidor de destino.
1. Clique com o botão direito do mouse no esquema e escolha **converter esquema**. 
1. Depois que o esquema terminar a conversão, compare o código convertido com o código original para identificar possíveis problemas. 



## <a name="migrate"></a>Migrar 

Depois de concluir a avaliação de seus bancos de dados e resolver quaisquer discrepâncias, a próxima etapa é executar o processo de migração. A migração envolve duas etapas – publicação do esquema e migração dos dados. 

Para publicar o esquema e migrar os dados, siga estas etapas: 

1. Clique com o botão direito do mouse no banco de dados do **Gerenciador de metadados do banco de dados SQL do Azure** e escolha **sincronizar com o banco** Essa ação publica o esquema MySQL no banco de dados SQL do Azure.
1. Clique com o botão direito do mouse no esquema do MySQL no **Gerenciador de metadados do MySQL** e escolha **migrar dados**. Como alternativa, você pode selecionar **migrar dados** da navegação de linha superior. 
1. Após a conclusão da migração, exiba o relatório de **migração de dados** : 
1.  Valide a migração examinando os dados e o esquema no banco de dados SQL do Azure usando o SQL Server Management Studio (SSMS).


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

| Título/link     | Descrição    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ele oferece um cálculo simples, com um clique e geração de relatórios que ajuda muito a acelerar as avaliações de grandes imóveis, fornecendo um processo de decisão de plataforma de destino automatizado e uniforme. |

Esses recursos foram desenvolvidos como parte do programa Data SQL Ninja, que é patrocinado pela equipe de engenharia do Grupo de Dados do Azure. A principal responsabilidade do programa Data SQL Ninja é desbloquear e acelerar as oportunidades complexas e diversas de migração da plataforma de dados para a plataforma de Dados do Azure da Microsoft. Se você acredita que sua organização tem interesse em participar do programa Data SQL Ninja, entre em contato com sua equipe de contas e peça que eles enviem uma indicação.

## <a name="next-steps"></a>Próximas etapas 

- Certifique-se de conferir a [calculadora de TCO (custo total de propriedade) do Azure](https://aka.ms/azure-tco) para ajudar a estimar a economia de custos que você pode obter ao migrar suas cargas de trabalho para o Azure.

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte o artigo [serviço e ferramentas para a migração de dados](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- Para obter outros guias de migração, confira [Migração de banco de dados](https://datamigration.microsoft.com/). 

Para vídeos, consulte: 
- [Visão geral da jornada de migração e das ferramentas/serviços recomendados para executar a avaliação e a migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
