---
title: Introdução ao Azure Data Factory | Microsoft Docs
description: Saiba mais sobre o Azure Data Factory, um serviço de integração de dados de nuvem que orquestra e automatiza a movimentação e a transformação dos dados.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 7bc03e80fc49756d19677edbef6bd8d372849732
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937247"
---
# <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure?

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-introduction.md)
> * [Versão atual](introduction.md)

No mundo de Big Data, os dados brutos e não organizados são, muitas vezes, armazenados em sistemas relacionais, não relacionais e outros sistemas de armazenamento. No entanto, os dados brutos em si não possuem o contexto ou significado apropriados para fornecer uma visão adequada para os analistas, cientistas de dados ou responsáveis por decisões de negócio. 

O Big Data requer um serviço que possa orquestrar e operacionalizar processos para refinar esses enormes repositórios de dados brutos em visões de negócio acionáveis. O Azure Data Factory é um serviço de nuvem gerenciado que foi criado para esses projetos híbridos complexos para extrair, transformar e carregar (ETL), extrair, carregar e transformar (ELT) e de integração de dados.

Por exemplo, imagine uma empresa de jogos que coleta petabytes de logs de jogos que são gerados por jogos na nuvem. A empresa deseja analisar esses logs para saber as preferências dos clientes, a faixa demográfica e o comportamento de uso. Ela também deseja identificar as oportunidades de venda adicional e venda cruzada, desenvolver recursos novos e cativantes para estimular o crescimento do negócio, e fornecer uma melhor experiência para os clientes.

Para analisar esses logs, a empresa precisa usar os dados de referência, como as informações sobre o cliente, sobre o jogo e sobre a campanha de marketing, que estão em um armazenamento de dados local. A empresa deseja usar esses dados provenientes do repositório de dados local, combinando-os com os dados de log adicionais que ela possui no repositório de dados na nuvem. 

Para extrair informações, ela espera processar os dados associados usando um cluster Spark na nuvem (Azure HDInsight) e publicar os dados transformados em um data warehouse de nuvem como o SQL Data Warehouse do Azure para gerar um relatório de forma fácil. A empresa deseja automatizar esse fluxo de trabalho, e monitorá-lo e gerenciá-lo diariamente. Ela também deseja executá-lo quando entram arquivos no contêiner de armazenamento de blobs.

O Azure Data Factory é a plataforma que resolve esses cenários de dados. É um *serviço de integração de dados com baseado em nuvem que permite que você crie fluxos de trabalho orientados a dados na nuvem para orquestrar e automatizar a movimentação de dados e a transformação de dados*. Usando o Azure Data Factory, é possível criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de diferentes repositórios de dados. Ele pode processar e transformar dados usando serviços de computação como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning. 

Além disso, é possível publicar dados de saída para repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). Por fim, por meio do Azure Data Factory, os dados brutos podem ser organizados em armazenamentos de dados e em data lakes importantes para possibilitar melhores decisões corporativas.

![Visão de nível superior do Data Factory](media/introduction/big-picture.png)

## <a name="how-does-it-work"></a>Como ele funciona?
Os pipelines (fluxos de trabalho controlados por dados) no Azure Data Factory normalmente executam as quatro etapas a seguir:

![Quatro etapas de um fluxo de trabalho controlado por dados](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Conectar e coletar

As empresas possuem dados de vários tipos que estão localizados em diferentes fontes locais, na nuvem, estruturadas, não estruturadas e semi-estruturadas, todos chegando em diferentes intervalos e velocidades. 

A primeira etapa ao criar um sistema de geração de informações é conectar todas as diferentes fontes de dados e processamento necessárias, como os serviços de software como serviço (SaaS), bancos de dados, compartilhamentos de arquivos e serviços Web FTP. A etapa seguinte é mover os dados conforme necessário para um local central para que estes sejam processados posteriormente.

Sem o Data Factory, as empresas devem criar componentes de movimentação de dados personalizados ou gravar serviços personalizados para integrar essas fontes de dados e processamento. É caro e difícil integrar e manter esses sistemas. Além disso, eles também, muitas vezes, não possuem o monitoramento, os alertas e os controles de nível empresarial oferecidos por um serviço totalmente gerenciado.

Com o Data Factory, você pode usar a [Atividade de Cópia](copy-activity-overview.md) em um pipeline de dados para mover os dados que estão em armazenamentos de dados de origem locais e na nuvem para um armazenamento de dados centralizado na nuvem para análise posterior. Por exemplo, é possível coletar dados no Azure Data Lake Storage e transformá-los posteriormente usando um serviço de computação do Azure Data Lake Analytics. Também é possível coletar dados no armazenamento de blobs do Azure e transformá-los posteriormente usando um cluster do Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Após os dados estarem presentes num repositório de dados central na nuvem, processe ou transforme os dados coletados usando serviços de computação como o HDInsight Hadoop, Spark, Data Lake Analytics e Machine Learning. Você quer produzir confiavelmente os dados transformados em uma agenda controlada e passível de manutenção para alimentar os ambientes de produção com dados confiáveis.

### <a name="publish"></a>Publicar
Após os dados brutos terem sido refinados para uma forma consumível pronta para negócios, carregue-os no Data Warehouse do Azure, Banco de Dados SQL do Azure, Azure CosmosDB ou qualquer mecanismo analítico para o qual os seus usuários empresariais possam apontar a partir de suas respectivas ferramentas de business intelligence.

### <a name="monitor"></a>Monitoramento
Após ter criado e implantado com sucesso o pipeline de integração de dados, fornecendo valor empresarial a partir de dados refinados, monitore as atividades e pipelines agendados para saber as taxas de sucesso e falha. O Azure Data Factory tem suporte interno para monitoramento de pipelines por meio do Azure Monitor, da API, do PowerShell, dos logs do Azure Monitor e dos painéis de integridade no portal do Azure.

## <a name="top-level-concepts"></a>Conceitos de nível superior
Uma assinatura do Azure pode ter uma ou mais instâncias do Azure Data Factory (ou data factories). O Azure Data Factory é composto de quatro componentes principais. Esses componentes trabalham juntos para oferecer a plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para mover e transformar dados.

### <a name="pipeline"></a>Pipeline
Um data factory pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades que realiza uma unidade de trabalho. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados provenientes de um blob do Azure e, em seguida, executa uma consulta Hive em um cluster HDInsight para particionar os dados. 

A vantagem disso é que o pipeline permite que você gerencie atividades como um conjunto, em vez de gerenciar cada uma individualmente. As atividades em um pipeline podem ser encadeadas para operarem de modo sequencial ou elas podem operar de forma independente em paralelo.

### <a name="activity"></a>Atividade
As atividades representam uma etapa de processamento em um pipeline. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um repositório de dados para outro. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a três tipos de atividades: atividades de movimentação de dados, atividades de transformação de dados e atividades de controle.

### <a name="datasets"></a>Conjunto de dados
Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam para ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. 

### <a name="linked-services"></a>Serviços vinculados
Os serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para que o Data Factory se conecte aos recursos externos. Pense dessa maneira: um serviço vinculado define a conexão à fonte de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço vinculado de Armazenamento do Azure especifica a cadeia de conexão para conectar-se à conta de Armazenamento do Azure. Além disso, um conjunto de dados de blob do Azure especifica o contêiner de blob e a pasta que contém os dados.

Serviços vinculados são usados para duas finalidades no Data Factory:

- Para representar uma **fonte de dados** que inclui, mas não está limitada a, um banco de dados do SQL Server local, um banco de dados Oracle, um compartilhamento de arquivos ou uma conta de armazenamento de blobs do Azure. Para obter uma lista dos armazenamentos de dados com suporte, consulte o artigo [Copy activity](copy-activity-overview.md) (Atividade de cópia).

- Para representar um **recurso de computação** que pode hospedar a execução de uma atividade. Por exemplo, a atividade HDInsightHive é executada em um cluster Hadoop do HDInsight. Para obter uma lista das atividades de transformação e dos ambientes de computação com suporte, confira o artigo [transformar dados](transform-data.md).

### <a name="triggers"></a>Gatilhos
Os gatilhos representam a unidade de processamento que determina quando uma execução de pipeline precisa ser inicializada. Existem diferentes tipos de gatilhos para diferentes tipos de eventos.

### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância da execução do pipeline. As execuções de pipeline normalmente são instanciadas por meio da transmissão de argumentos para os parâmetros que são definidos em pipelines. Os argumentos podem ser passados manualmente ou na definição do gatilho.

### <a name="parameters"></a>parâmetros
Os parâmetros são pares chave-valor da configuração somente leitura.  Os parâmetros são definidos no pipeline. Os argumentos para os parâmetros definidos são passados durante a execução por um contexto de execução criado por um gatilho ou por um pipeline executado manualmente. As atividades no pipeline consomem os valores de parâmetro.

Um conjunto de dados é um parâmetro fortemente tipado e uma entidade reutilizável/referenciável. Uma atividade pode referenciar conjuntos de dados e consumir as propriedades que são estabelecidas na definição do conjunto de dados.

Um serviço vinculado também é um parâmetro fortemente tipado que contém as informações de conexão para um armazenamento de dados ou para um ambiente de computação. Ele também é uma entidade reutilizável/referenciável.

### <a name="control-flow"></a>Controlar fluxo
O fluxo de controle é uma orquestração de atividades do pipeline que inclui o encadeamento de atividades em uma sequência, ramificação, definindo parâmetros no nível do pipeline, e passando argumentos durante a invocação do pipeline sob demanda ou a partir de um gatilho. Também inclui transmissão de estado personalizada e contêineres de looping, ou seja, iteradores for-each.


Para obter mais informações sobre os conceitos do Data Factory, confira os seguintes artigos:

- [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Tempo de execução de integração](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Regiões com suporte

Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). No entanto, uma fábrica de dados pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre repositórios de dados ou processar dados usando serviços de computação.

O Azure Data Factory em si não armazena dados. Ele permite que você crie fluxos de trabalho controlados por dados para orquestrar a movimentação de dados entre os armazenamentos de dados com suporte e o processamento de dados usando serviços de computação em outras regiões ou em um ambiente local. Também permite monitorar e gerenciar fluxos de trabalho usando mecanismos programáticos e de IU.

Embora o Data Factory esteja disponível somente em determinadas regiões, o serviço que capacita a movimentação de dados no Data Factory está disponível globalmente em várias regiões. Se um repositório de dados estiver atrás de um firewall, então um Integration Runtime auto-hospedado instalado em seu ambiente local moverá os dados.

Por exemplo, digamos que seus ambientes de computação, como o cluster Azure HDInsight e o Azure Machine Learning, estejam ficando sem a região Europa Ocidental. Você pode criar e usar uma instância do Azure Data Factory no Leste dos EUA e no Leste dos EUA 2 e usá-la para agendar trabalhos em seus ambientes de computação na Europa Ocidental. Demora alguns milissegundos para o Data Factory disparar o trabalho em seu ambiente de computação, mas o tempo de execução do trabalho em seu ambiente de computação não é alterado.

## <a name="accessibility"></a>Acessibilidade

A experiência do usuário do Data Factory no portal do Azure está acessível.

## <a name="compare-with-version-1"></a>Comparar com a versão 1
Para obter uma lista das diferenças entre a versão 1 e a versão atual do serviço Data Factory, veja [Comparar com a versão 1](compare-versions.md). 

## <a name="next-steps"></a>Próximas etapas
Introdução à criação de um pipeline do Data Factory usando um dos seguintes SDK/ferramentas: 

- [Interface do usuário do Data Factory no portal do Azure](quickstart-create-data-factory-portal.md)
- [Ferramenta Copiar Dados no portal do Azure](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Modelo do Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md)
 
