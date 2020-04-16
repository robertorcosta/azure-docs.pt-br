---
title: 'Fábrica de dados do Azure: perguntas frequentes '
description: Obtenha resposta a perguntas frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 8d0b49b73ef6b67653fbf32db1174880a51d432d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412954"
---
# <a name="azure-data-factory-faq"></a>Perguntas frequentes sobre o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece respostas a perguntas frequentes sobre o Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory? 
A Data Factory é um serviço ETL totalmente gerenciado, baseado em nuvem, que automatiza a movimentação e a transformação de dados. Assim como uma fábrica que usa equipamentos para transformar matérias-primas em produtos, o Azure Data Factory orquestra serviços existentes que coletam dados brutos e os transformam em informações para uso imediato. 

Usando o Azure Data Factory, você pode criar fluxos de trabalho conduzidos por dados para mover dados entre armazenamentos de dados locais e na nuvem. E você pode processar e transformar dados com Fluxos de Dados. O ADF também suporta mecanismos de computação externos para transformações codificadas à mão usando serviços de computação como O Azure HDInsight, Azure Databricks e o tempo de execução dos Serviços de Integração de Servidores SQL (SSIS). 

Com o Data Factory, você pode executar o processamento de dados em um serviço de nuvem baseado no Azure ou em seu próprio ambiente de computação auto-hospedado, como SSIS, SQL Server ou Oracle. Depois de criar um pipeline que execute a ação necessária, você pode programá-la para ser executada periodicamente (por hora, diária ou semanal, por exemplo), agendar a janela de tempo ou acionar o pipeline de uma ocorrência de evento. Para obter mais informações, consulte [Introdução ao Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Fluxos de controle e escala 
Para suportar os diversos fluxos e padrões de integração no moderno data warehouse, a Data Factory permite uma modelagem flexível de pipeline de dados. Isso implica paradigmas de programação de fluxo de controle total, que incluem execução condicional, ramificação em pipelines de dados e a capacidade de passar explicitamente parâmetros dentro e através desses fluxos. O fluxo de controle também abrange a transformação de dados por meio de despacho de atividade para mecanismos de execução externa e recursos de fluxo de dados, incluindo movimentação de dados em escala, através da atividade Copiar.

O Data Factory fornece liberdade para modelar qualquer estilo de fluxo que seja necessário para integração de dados e que possa ser enviado sob demanda ou repetidamente em um agendamento. Alguns fluxos comuns que esse modelo permite são:   

- Fluxos de controle:
    - As atividades podem ser acorrentadas em uma seqüência dentro de um pipeline.
    - As atividades podem ser ramificadas dentro de um gasoduto.
    - Parâmetros:
        - Os parâmetros podem ser definidos no nível do pipeline e os argumentos podem ser aprovados enquanto você invoca o pipeline sob demanda ou a partir de um gatilho.
        - As atividades podem consumir os argumentos passados para o pipeline.
    - Passagem de estado personalizado:
        - As saídas de atividade, incluindo o estado, podem ser consumidas por uma atividade subseqüente no gasoduto.
    - Recipientes de looping:
        - A atividade forcada irá iterar sobre uma coleção especificada de atividades em um loop. 
- Fluxos baseados em gatilho:
    - Os pipelines podem ser acionados sob demanda ou por hora.
- Fluxos delta:
    - Os parâmetros podem ser usados para definir sua marca de água alta para cópia delta enquanto movem a dimensão ou tabelas de referência de uma loja relacional, seja no local ou na nuvem, para carregar os dados no lago. 

Para obter mais informações, consulte [Tutorial: fluxo de controle](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dados transformados em escala com pipelines livres de código
A nova experiência de ferramentas baseada em navegador fornece criação e implantação de pipeline sem código, com uma experiência moderna e interativa baseada na Web.

Para desenvolvedores de dados visuais e engenheiros de dados, a UI web da Fábrica de Dados é o ambiente de design sem código que você usará para construir pipelines. É totalmente integrado com o Visual Studio Online Git e oferece integração para CI/CD e desenvolvimento iterativo com opções de depuração.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDKs multiplataforma séricos para usuários avançados
A Fábrica de Dados V2 fornece um rico conjunto de SDKs que podem ser usados para criar, gerenciar e monitorar pipelines usando seu IDE favorito, incluindo:
* SDK do Python
* PowerShell CLI
* SDK do C#

Os usuários também podem usar as APIs REST documentadas para interagir com o Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Desenvolvimento iterativo e depuração usando ferramentas visuais
As ferramentas visuais da Fábrica de Dados Do Azure permitem o desenvolvimento iterativo e a depuração. Você pode criar seus pipelines e fazer testes usando o recurso **Debug** na tela do pipeline sem escrever uma única linha de código. Você pode ver os resultados de suas corridas de teste na janela **de saída** da tela do pipeline. Depois que sua execução de teste for bem sucedida, você pode adicionar mais atividades ao seu pipeline e continuar depurando de forma iterativa. Você também pode cancelar suas corridas de teste depois que eles estiverem em andamento. 

Você não é obrigado a publicar suas alterações no serviço de fábrica de dados antes de selecionar **Debug**. Isso é útil em cenários onde você deseja ter certeza de que as novas adições ou alterações funcionarão como esperado antes de atualizar seus fluxos de trabalho de fábrica de dados em ambientes de desenvolvimento, teste ou produção. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Capacidade de implantar pacotes SSIS no Azure 
Se você quiser mover as cargas de trabalho do SSIS, poderá criar um Data Factory e provisionar um runtime de integração do Azure-SSIS. Um tempo de execução de integração Azure-SSIS é um cluster totalmente gerenciado de VMs (nós) do Azure que são dedicados a executar seus pacotes SSIS na nuvem. Para obter instruções passo a passo, consulte o tutorial [Implantar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>SDKs
Se você é um usuário avançado e procura uma interface programática, a Data Factory fornece um rico conjunto de SDKs que você pode usar para criar, gerenciar ou monitorar pipelines usando seu IDE favorito. O suporte à linguagem inclui .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitoramento
É possível monitorar os Data Factories por meio do PowerShell, SDK ou das Ferramentas de Monitoramento Visual na interface do usuário do navegador. Você pode monitorar e gerenciar fluxos personalizados sob demanda, baseados em gatilho e relógio de forma eficiente e eficaz. Cancele tarefas existentes, veja falhas rapidamente, aprofunde para baixo para obter mensagens de erro detalhadas e depuraos os problemas, tudo a partir de um único painel de vidro sem alternação de contexto ou navegar para frente e para trás entre as telas. 

### <a name="new-features-for-ssis-in-data-factory"></a>Novos recursos para SSIS na fábrica de dados
Desde a pré-visualização pública inicial em 2017, a Data Factory adicionou os seguintes recursos para o SSIS:

-    Suporte para mais três configurações/variantes do Banco de Dados SQL do Azure para hospedar o banco de dados SSIS (SSISDB) de projetos/pacotes:
-    Banco de dados SQL com pontos finais de serviço de rede virtual
-    Instância gerenciada
-    Pool elástico
-    Suporte para uma rede virtual Do Azure Resource Manager em cima de uma rede virtual clássica a ser depreciada no futuro, o que permite injetar/juntar o tempo de execução da integração Azure-SSIS a uma rede virtual configurada para Banco de Dados SQL com endpoints de serviço de rede virtual/MI/acesso a dados no local. Para obter mais informações, consulte também [participar de um tempo de execução de integração Azure-SSIS em uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).
-    Suporte à autenticação do Azure Active Directory (Azure AD) e autenticação SQL para se conectar ao SSISDB, permitindo a autenticação do Azure AD com a identidade gerenciada da Fábrica de Dados para os recursos do Azure
-    Suporte para trazer sua própria licença sql server no local para ganhar uma economia substancial de custos com a opção Azure Hybrid Benefit
-    Suporte para a Enterprise Edition do tempo de execução de integração Azure-SSIS que permite usar recursos avançados/premium, uma interface de configuração personalizada para instalar componentes/extensões adicionais e um ecossistema parceiro. Para obter mais informações, consulte também [Enterprise Edition, Custom Setup e 3rd Party Extensibility for SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Uma integração mais profunda do SSIS na Fábrica de Dados que permite invocar/acionar as atividades do pacote SSIS de primeira classe em pipelines de fábrica de dados e programá-las via SSMS. Para obter mais informações, consulte também [Modernizar e estender seus fluxos de trabalho ETL/ELT com atividades SSIS em pipelines ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Qual é o tempo de execução da integração?
O tempo de execução de integração é a infra-estrutura de computação que o Azure Data Factory usa para fornecer os seguintes recursos de integração de dados em vários ambientes de rede:

- **Movimentação de dados**: Para a movimentação de dados, o tempo de execução de integração move os dados entre os armazenamentos de dados de origem e destino, ao mesmo tempo em que fornece suporte para conectores incorporados, conversão de formato, mapeamento de colunas e transferência de dados executor escorável e escalável.
- **Atividades de despacho**: Para a transformação, o tempo de execução de integração fornece capacidade para executar nativamente pacotes SSIS.
- **Execute pacotes SSIS**: O tempo de execução de integração executa nativamente pacotes SSIS em um ambiente de computação Azure gerenciado. O tempo de execução de integração também suporta o envio e monitoramento de atividades de transformação em execução em uma variedade de serviços de computação, como Azure HDInsight, Azure Machine Learning, SQL Database e SQL Server.

Você pode implantar uma ou muitas instâncias do tempo de execução de integração conforme necessário para mover e transformar dados. O tempo de execução da integração pode ser executado em uma rede pública Azure ou em uma rede privada (no local, Azure Virtual Network ou Amazon Web Services virtual private cloud [VPC]). 

Para obter mais informações, consulte [O tempo de execução da Integração na Fábrica de Dados do Azure](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>O que é o limite do número de runtimes de integração?
Não há nenhum limite rígido quanto ao número de instâncias de runtime de integração que você pode ter em um data factory. Entretanto, há um limite de número de núcleos de VM que o runtime de integração pode usar por assinatura para a execução de pacotes do SSIS. Para saber mais, confira [Limites do Data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quais são os conceitos de nível superior do Azure Data Factory?
Uma assinatura do Azure pode ter uma ou mais instâncias (ou data factories) do Azure Data Factory. O Azure Data Factory contém quatro componentes principais que trabalham juntos como uma plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para movimentação e transformação dos dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades para realizar uma unidade de trabalho. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que ingerem dados de um blob do Azure e, em seguida, executar uma consulta de Hive em um cluster de HDInsight para particionar os dados. O benefício é que você pode usar um pipeline para gerenciar as atividades como um conjunto, em vez de gerenciar cada atividade individualmente. Você pode encadear as atividades em um pipeline para operá-las em sequência ou pode operá-las de modo independente, em paralelo.

### <a name="data-flows"></a>Fluxos de dados
Fluxos de dados são objetos que você constrói visualmente na Fábrica de Dados que transformam dados em escala nos serviços de Backend Spark. Você não precisa entender programação ou faísca interna. Basta projetar sua intenção de transformação de dados usando gráficos (Mapeamento) ou planilhas (Wrangling).

### <a name="activities"></a>Atividades
As atividades representam uma etapa de processamento em um pipeline. Por exemplo, você pode usar uma atividade copiar para copiar dados de um armazenamento de dados para outro armazenamento de dados. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a três tipos de atividades: atividades de movimentação de dados, atividades de transformação de dados e atividades de controle.

### <a name="datasets"></a>Conjunto de dados
Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam para ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. 

### <a name="linked-services"></a>Serviços vinculados
Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense desta forma: um serviço vinculado define a conexão com a fonte de dados, e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão para conectar-se à conta de Armazenamento do Azure. E um conjunto de dados blob do Azure especifica o recipiente blob e a pasta que contém os dados.

Serviços vinculados têm duas finalidades no Data Factory:

- Para representar um *armazenamento de dados* que inclui, mas não é limitado a, uma instância do SQL Server local, uma instância de banco de dados Oracle, um compartilhamento de arquivos ou uma conta de armazenamento de Blobs do Azure. Para obter uma lista de repositórios de dados com suporte, consulte [Atividade de cópia no Azure Data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode hospedar a execução de uma atividade. Por exemplo, a atividade do Hive do HDInsight é executada em um cluster Hadoop do HDInsight. Para obter uma lista das atividades de transformação e dos ambientes de computação com suporte, consulte [Transformar dados no Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Gatilhos
Gatilhos representam as unidades de processamento que determinam quando a execução de um pipeline é iniciada. Existem diferentes tipos de gatilhos para diferentes tipos de eventos. 

### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância da uma execução do pipeline. Normalmente, você instancia uma execução do pipeline passando argumentos para os parâmetros definidos no pipeline. Você pode passar os argumentos manualmente ou dentro da definição do gatilho.

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares chave-valor em uma configuração somente leitura.Você define os parâmetros em um pipeline e passa os argumentos para os parâmetros definidos durante a execução de um contexto de execução. O contexto de execução é criado por um gatilho ou de um pipeline que você executa manualmente. As atividades no pipeline consomem os valores de parâmetro.

Um conjunto de dados é um parâmetro fortemente digitado e uma entidade que você pode reutilizar ou referenciar. Uma atividade pode referenciar conjuntos de dados, e pode consumir as propriedades definidas na definição do conjunto de dados.

Um serviço vinculado também é um parâmetro fortemente tipado que contém as informações de conexão para um armazenamento de dados ou para um ambiente de computação. É também uma entidade que você pode reutilizar ou referenciar.

### <a name="control-flows"></a>Fluxos de controle
Fluxos de controle coordenam as atividades de pipeline, incluindo encadeamento de atividades em uma sequência, ramificação, parâmetros que você define no nível do pipeline e argumentos passados conforme você invoca o pipeline sob demanda ou de um gatilho. Os fluxos de controle também incluem recipientes de passagem e looping de estado personalizados (ou seja, foreach iterators).


Para obter mais informações sobre os conceitos do Data Factory, confira os seguintes artigos:

- [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [runtime de integração](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>O que é o modelo de preços Data Factory?
Para os detalhes de preço do Azure Data Factory, consulte [Detalhes de preço do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso me manter informado sobre o Data Factory?
Para obter as informações mais atualizadas sobre o Azure Data Factory, vá para os seguintes sites:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page da documentação](/azure/data-factory)
- [Home page do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Análise técnica aprofundada 

### <a name="how-can-i-schedule-a-pipeline"></a>Como fazer para agendar um pipeline? 
Você pode usar o gatilho do agendador ou gatilho de janela de tempo para agendar um pipeline. O gatilho usa um cronograma de relógiode parede, que pode agendar os gasodutos periodicamente ou em padrões recorrentes baseados em calendário (por exemplo, às segundas-feiras às 18:00 e quintas-feiras às 21:00). Para obter mais informações, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Posso passar parâmetros para uma execução de pipeline?
Sim, os parâmetros são um conceito de primeira classe e de alto nível na Fábrica de Dados. Você pode definir parâmetros no nível do pipeline e passar argumentos durante a execução do pipeline sob demanda ou usando um gatilho.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>É possível definir valores padrão para os parâmetros do pipeline? 
Sim. Você pode definir valores padrão para os parâmetros nos pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Uma atividade em um pipeline pode consumir argumentos passados para uma execução de pipeline? 
Sim. Cada atividade no pipeline pode consumir o valor do parâmetro passado para o pipeline e ser executada com o constructo `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de saída de uma atividade pode ser consumida em outra atividade? 
Sim. Uma saída de atividade pode ser consumida em uma atividade subsequente com o constructo `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como tratar normalmente o valores nulos em uma saída de atividade? 
Você pode usar o constructo `@coalesce` nas expressões para manipular valores nulos normalmente. 

## <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Preciso de ajuda para solucionar minha lógica de fluxo de dados. Que informações eu preciso fornecer para obter ajuda?

Quando a Microsoft fornecer ajuda ou solução de problemas com fluxos de dados, forneça o Script de fluxo de dados. Este é o script por trás do seu gráfico de fluxo de dados. Na UI da ADF, abra seu fluxo de dados e clique no botão "Script" no canto superior direito. Copie e cole este script ou salve-o em um arquivo de texto.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Como acessar dados usando os outros 90 tipos de conjunto de dados na Fábrica de Dados?

O recurso de fluxo de dados de mapeamento atualmente permite o Azure SQL Database, o Azure SQL Data Warehouse, arquivos de texto delimitados do armazenamento Azure Blob ou do Azure Data Lake Storage Gen2 e arquivos Parquet do armazenamento Blob ou data lake storage Gen2 nativamente para fonte e sink. 

Use a atividade Copiar para encenar dados de qualquer um dos outros conectores e, em seguida, execute uma atividade de Fluxo de Dados para transformar dados depois de serem encenadas. Por exemplo, seu pipeline primeiro será copiado para o armazenamento Blob e, em seguida, uma atividade de fluxo de dados usará um conjunto de dados na fonte para transformar esses dados.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>O tempo de execução de integração auto-hospedado está disponível para fluxos de dados?

O IR auto-hospedado é um gasoduto adf que você pode usar com a Atividade de Cópia para adquirir ou mover dados para e a partir de fontes e sinks baseados em VM. Stagere os dados primeiro com uma cópia, depois fluxo de dados para transformação e, em seguida, uma cópia subseqüente se você precisar mover esses dados transformados de volta para o armazenamento on-prem.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>O mecanismo de computação de fluxo de dados serve a vários inquilinos?
Clusters nunca são compartilhados. Garantimos o isolamento para cada trabalho executado em corridas de produção. No caso de cenário de depuração, uma pessoa recebe um cluster, e todos os depuradores irão para aquele cluster que são iniciados por esse usuário.

## <a name="wrangling-data-flows"></a>Disputando fluxos de dados

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Quais são as regiões suportadas para a disputa do fluxo de dados?

Atualmente, a disputa do fluxo de dados é suportada em fábricas de dados criadas nas seguintes regiões:

* Leste da Austrália
* Canadá Central
* Índia Central
* Leste dos EUA
* Leste dos EUA 2
* Leste do Japão
* Norte da Europa
* Sudeste Asiático
* Centro-Sul dos Estados Unidos
* Sul do Reino Unido
* Centro-Oeste dos EUA
* Europa Ocidental
* Oeste dos EUA
* Oeste dos EUA 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Quais são as limitações e restrições com a disputa do fluxo de dados?

Os nomes do conjunto de dados só podem conter caracteres alfa-numéricos. Os seguintes armazenamentos de dados são suportados:

* Conjunto de dados delimitadotexto no Azure Blob Storage usando autenticação de chave da conta
* Conjunto de dados delimitadotexto no Azure Data Lake Storage gen2 usando a chave da conta ou a autenticação principal do serviço
* Conjunto de dados delimitadotexto na gen1 do Azure Data Lake Storage usando autenticação principal do serviço
* Banco de dados Azure SQL e Data Warehouse usando autenticação sql. Veja os tipos SQL suportados abaixo. Não há PolyBase ou suporte para armazenamento de dados.

Neste momento, a integração do Key Vault de serviço vinculado não é suportada na disputa de fluxos de dados.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Qual é a diferença entre mapear e disputar fluxos de dados?

O mapeamento dos fluxos de dados fornece uma maneira de transformar dados em escala sem qualquer codificação necessária. Você pode projetar um trabalho de transformação de dados na tela de fluxo de dados, construindo uma série de transformações. Comece com qualquer número de transformações de código-fonte, seguidas pelas etapas de transformação de dados. Complete seu fluxo de dados com uma pia para aterrissar seus resultados em um destino. O mapeamento do fluxo de dados é ótimo no mapeamento e transformação de dados com esquemas conhecidos e desconhecidos nos dissipadores e fontes.

A disputa de fluxos de dados permite que você faça uma preparação e exploração ágil de dados usando o editor de mashup Power Query Online em escala via execução de faíscas. Com o surgimento de lagos de dados, às vezes você só precisa explorar um conjunto de dados ou criar um conjunto de dados no lago. Você não está mapeando um alvo conhecido. A disputa de fluxos de dados é usada para cenários de análise menos formais e baseados em modelos.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Qual é a diferença entre fluxos de dados da plataforma de energia e a disputa de fluxos de dados?

Os Fluxos de dados da Plataforma de Energia permitem que os usuários importem e transformem dados de uma ampla gama de fontes de dados no Common Data Service e no Azure Data Lake para criar aplicativos powerapps, relatórios de Power BI ou automação de fluxo. Os Fluxos de dados da plataforma de energia usam as experiências de preparação de dados estabelecidas do Power Query, semelhantes ao Power BI e ao Excel. Os Fluxos de dados da plataforma de energia também permitem uma reutilização fácil dentro de uma organização e lidam automaticamente com a orquestração (por exemplo, atualizando automaticamente fluxos de dados que dependem de outro fluxo de dados quando o primeiro é atualizado).

O Azure Data Factory (ADF) é um serviço gerenciado de integração de dados que permite aos engenheiros de dados e ao integrador de dados cidadãos criar fluxos de trabalho complexos de e-transformada-de-carga de extrato híbrido (ETL) e de extrato-load-transform (ELT). A disputa do fluxo de dados no ADF capacita os usuários com um ambiente sem código e sem servidor que simplifica a preparação de dados na nuvem e dimensiona para qualquer tamanho de dados sem necessidade de gerenciamento de infra-estrutura. Ele usa a tecnologia de preparação de dados do Power Query (também usada em fluxos de dados da Power Platform, Excel, Power BI) para preparar e moldar os dados. Criado para lidar com todas as complexidades e desafios de escala da integração de big data, a disputa de fluxos de dados permite que os usuários preparem rapidamente dados em escala através da execução de faíscas. Os usuários podem construir pipelines de dados resilientes em um ambiente visual acessível com nossa interface baseada no navegador e permitir que o ADF lide com as complexidades da execução do Spark. Crie agendas para seus pipelines e monitore suas execuções de fluxo de dados a partir do portal de monitoramento do ADF. Gerencie facilmente os SLAs de disponibilidade de dados com o rico monitoramento e alertas de disponibilidade da ADF e aproveite os recursos de integração e implantação contínuos incorporados para salvar e gerenciar seus fluxos em um ambiente gerenciado. Estabeleça alertas e visualize planos de execução para validar que sua lógica está funcionando como planejado à medida que você sintoniza seus fluxos de dados.

### <a name="supported-sql-types"></a>Tipos SQL suportados

A disputa do fluxo de dados suporta os seguintes tipos de dados no SQL. Você terá um erro de validação para usar um tipo de dados que não é suportado.

* short
* double
* real
* FLOAT
* char
* NCHAR
* varchar
* NVARCHAR
* inteiro
* INT
* bit
* booleano
* SMALLINT
* TINYINT
* BIGINT
* long
* text
* date
* DATETIME
* datetime2
* smalldatetime
* timestamp
* UNIQUEIDENTIFIER
* Xml

Outros tipos de dados serão suportados no futuro.

## <a name="next-steps"></a>Próximas etapas
Para obter instruções passo a passo para criar um data factory, consulte os tutoriais a seguir:

- [Quickstart: Crie uma fábrica de dados](quickstart-create-data-factory-dot-net.md)
- [Tutorial: copiar os dados na nuvem](tutorial-copy-data-dot-net.md)
