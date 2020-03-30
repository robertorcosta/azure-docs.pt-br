---
title: runtime de integração
description: Saiba mais sobre o Integration Runtime no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: 4077e1e00b606480ec93feacbad3c841c0de1ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336170"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime no Azure Data Factory
O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer as seguintes funcionalidades de integração de dados entre diferentes ambientes de rede:

- **Fluxo de dados**: Execute um [fluxo de dados](concepts-data-flow-overview.md) no ambiente de computação Azure gerenciado.  
- **Movimentação de dados**: Copiar dados em armazenamentos de dados em rede pública e data stores em rede privada (on-premises ou rede privada virtual). Ele fornece suporte para conectores internos, conversão de formato, mapeamento de coluna e transferência de dados de alto desempenho e escalonáveis.
- **Despacho de atividades**: Despachar e monitorar atividades de transformação em execução em uma variedade de serviços de computação, como Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server e muito mais.
- **Execução de pacote SSIS**: execute nativamente pacotes do SSIS (SQL Server Integration Services) em um ambiente de computação gerenciada do Azure.

No Data Factory, uma atividade define a ação a ser realizada. Um serviço vinculado define um armazenamento de dados de destino ou um serviço de computação. Um Integration Runtime fornece a ponte entre a atividade e os serviços vinculados.  Ele é referenciado pelo serviço ou atividade vinculado, e fornece o ambiente de computação de onde a atividade é executada ou é despachada. Desse modo, a atividade pode ser executada na região mais próxima possível do serviço de computação ou armazenamento de dados de destino, da maneira que proporciona o mais alto desempenho e atendendo às necessidades de segurança e de conformidade.

## <a name="integration-runtime-types"></a>Tipos de Integration Runtime
O Data Factory oferece três tipos de Integration Runtime e você deve escolher o tipo que melhor atende as funcionalidades de integração de dados e as necessidades de ambiente de rede pelas quais você está procurando.  Esses três tipos são:

- Azure
- Auto-hospedado
- Azure-SSIS

A tabela a seguir descreve as funcionalidades e o suporte de rede para cada um dos tipos de Integration Runtime:

Tipo de IR | Rede pública | Rede privada
------- | -------------- | ---------------
Azure | Fluxo de Dados<br/>Movimentação de dados<br/>Expedição de atividade | &nbsp;
Auto-hospedado | Movimentação de dados<br/>Expedição de atividade | Movimentação de dados<br/>Expedição de atividade
Azure-SSIS | Execução do pacote SSIS | Execução do pacote SSIS

O diagrama a seguir mostra como os diferentes runtimes de integração podem ser usados em combinação para oferecer funcionalidades de integração de dados e suporte de rede avançados:

![Tipos diferentes de runtimes de integração](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure Integration runtime
Um Integration Runtime do Azure é capaz de:

- Executando fluxos de dados no Azure 
- Executar a atividade de cópia entre armazenamentos de dados de nuvem
- Despachando as seguintes atividades de transformação na rede pública: Atividade seletiva de Notebook/ Jar/ Python, atividade do HDInsight Hive, atividade do HDInsight Pig, atividade do HDInsight Spark, atividade de streaming hdinsight, atividade de execução em lote de aprendizado de máquina, atividades de atualização de aprendizado de máquina, atividade de procedimento armazenado, atividade U-SQL do Lago de Dados, atividade personalizada .NET, atividade da Web, atividade de pesquisa e atividade Get Metadata.

### <a name="azure-ir-network-environment"></a>Ambiente de rede do IR do Azure
O Azure Integration Runtime suporta a conexão a armazenamentos de dados e serviços de computação com pontos finais acessíveis ao público. Use um Integration Runtime auto-hospedado para o ambiente de Rede Virtual do Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
O Integration Runtime do Azure fornece uma computação totalmente gerenciada e sem servidor no Azure.  Você não precisa se preocupar com provisão de infra-estrutura, instalação de software, patches ou dimensionamento de capacidade.  Além disso, você só paga pela duração da utilização real.

O Integration Runtime do Azure fornece a computação nativa de para mover dados entre armazenamentos de dados de nuvem de maneira segura, confiável e de alto desempenho.  Você pode definir quantas unidades de integração de dados usar na atividade de cópia e o tamanho da computação do IR do Azure é expandido elasticamente de modo adequado sem que você precise ajustar explicitamente o tamanho do Integration Runtime do Azure. 

O despacho de atividades é uma operação leve para direcionar a atividade para o serviço de computação de destino, portanto não há necessidade de aumentar o tamanho do cálculo para este cenário.

Para obter informações sobre como criar e configurar um IR do Azure, consulte Como criar e configurar o IR do Azure nos guias de instruções. 

> [!NOTE] 
> O tempo de execução do Azure Integration tem propriedades relacionadas ao tempo de execução do Fluxo de Dados, que define a infra-estrutura de computação subjacente que seria usada para executar os fluxos de dados. 

## <a name="self-hosted-integration-runtime"></a>runtime de integração auto-hospedada
Um IR auto-hospedado é capaz de:

- Executar a atividade de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados na rede privada.
- Despachando as seguintes atividades de transformação contra recursos de computação em locais ou Rede Virtual Azure: atividade hdinsight hive (BYOC-Bring Your Own Cluster), atividade HDInsight Pig (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark atividade (BYOC), atividade de streaming HDInsight (BYOC), atividade de execução em lote de aprendizado de máquina, atividades de atualização de aprendizado de máquina, atividade de procedimento armazenado, atividade U-SQL do Data Lake Analytics, atividade personalizada (é executada no lote do Azure), pesquisa atividade e atividade Get Metadata.

> [!NOTE] 
> Use o tempo de execução de integração auto-hospedado para suportar armazenamentos de dados que exijam trazer seu próprio driver, como SAP Hana, MySQL, etc.  Para obter mais informações, consulte [armazenamentos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) é uma dependência do IR auto-hospedado. Certifique-se de que o JRE está instalado no mesmo host.

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede do IR auto-hospedado
Se você deseja realizar a integração de dados com segurança em um ambiente de rede privada, que não tem uma linha de visão direta do ambiente de nuvem pública, você pode instalar um IR auto-hospedado no ambiente local por trás de seu firewall corporativo ou então em uma rede virtual privada.  O Integration Runtime auto-hospedado só faz conexões de saída com base em HTTP com a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR auto-hospedado
O IR auto-hospedado precisa ser instalado em um computador local ou em uma máquina virtual dentro de uma rede privada. Atualmente, só há suporte para a execução do IR auto-hospedado em um sistema operacional Windows.  

Para alta disponibilidade e escalabilidade, você pode expandir o IR auto-hospedado associando a instância lógica a vários computadores locais no modo ativo-ativo.  Para obter mais informações, veja como criar e configurar artigo [de IR auto-hospedado](create-self-hosted-integration-runtime.md) em como orientar para obter detalhes.

## <a name="azure-ssis-integration-runtime"></a>Integration Runtime do Azure-SSIS
Para fazer lift-and-shift da carga de trabalho existente do SSIS, você pode criar um IR Azure-SSIS para executar pacotes do SSIS nativamente.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede do IR do Azure-SSIS
O IR Azure-SSIS pode ser provisionado na rede pública ou na rede privada.  Há suporte para o acesso a dados locais unindo o IR do Azure-SSIS a uma Rede Virtual conectada à sua rede local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS
O IR do SSIS do Azure é um cluster totalmente gerenciado das VMs do Azure dedicado para executar os pacotes de SSIS. É possível colocar seu próprio servidor do Banco de Dados SQL do Azure ou Instância Gerenciada para hospedar o catálogo de projetos/pacotes do SSIS (SSISDB) que será anexado a ele. Você pode aumentar a potência de computação especificando o tamanho do nó e escalá-la horizontalmente especificando o número de nós no cluster. Você pode gerenciar o custo da execução do Integration Runtime do Azure SSIS, parando-o e iniciando-o como considerar adequado.

Para saber mais, veja o artigo sobre como criar e configurar IR do Azure-SSIS, encontrado nos guias de instruções.  Depois de criado, você pode implantar e gerenciar seus pacotes SSIS existentes com pouca ou nenhuma alteração usando ferramentas familiares, como o SSDT (SQL Server Data Tools) e o SSMS (SQL Server Management Studio), assim como usando o SSIS localmente.

Confira estes artigos para obter mais informações sobre o runtime do Azure-SSIS: 

- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo-a-passo para criar um IR Azure-SSIS e usa um Banco de Dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um runtime de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do Banco de Dados SQL do Azure e unir o IR a uma rede virtual. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
- [Unir um IR do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a rede virtual para que o IR do Azure-SSIS possa unir-se à rede virtual. 

## <a name="integration-runtime-location"></a>Localização do Integration Runtime
É na localização do Data Factory que os metadados do data factory são armazenados e é dela que o disparo do pipeline é iniciado. No entanto, um data factory pode acessar armazenamentos de dados e serviços de computação em outras regiões do Azure para mover dados entre armazenamentos de dados ou processar dados usando os serviços de computação. Esse comportamento é realizado por meio do [IR disponível globalmente](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade de dados, a eficiência e os custos de saída de rede reduzidos.

A localização do IR define a localização da respectiva computação de back-end e, essencialmente, a localização em que a movimentação de dados, a expedição de atividades e a execução de pacotes SSIS são executadas. A localização de IR pode ser diferente da localização do data factory ao qual ele pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure
Você pode definir um determinado local de um IR do Azure, onde a movimentação de dados ou a expedição de atividade ocorrerá nessa região específica. 

>[!TIP]
>Se você tiver requisitos de conformidade de dados restritos e precisar garantir que os dados não saiam de uma determinada geografia, você pode criar explicitamente um IR do Azure em uma determinada região e apontar o Serviço Vinculado a esse IR usando a propriedade ConnectVia. Por exemplo, se você quiser copiar dados de Blob no Sul do Reino Unido para SQL DW no Sul do Reino Unido e quiser garantir que os dados não saiam do Reino Unido, crie um IR do Azure no Sul do Reino Unido e vincule os dois Serviços Vinculados a este IR.

Se você optar por usar o **Azure IR automático**, que é o padrão, 

- Para a atividade de cópia, o ADF fará um esforço para detectar automaticamente a localização do seu armazenamento de dados do dissipador e, em seguida, usar o IR na mesma região, se disponível ou o mais próximo na mesma geografia; se a região do armazenamento de dados da pia não for detectável, o RI na região da fábrica de dados como alternativa é usado.

  Por exemplo, você tem sua fábrica criada no leste dos EUA, 
  
  - Ao copiar dados para o Azure Blob no Oeste dos EUA, se a ADF detectou com sucesso que o Blob está no Oeste dos EUA, a atividade de cópia é executada no IR nos EUA Ocidentais; se a detecção da região falhar, a atividade de cópia será executada no IR no Leste dos EUA.
  - Ao copiar dados para salesforce dos quais a região não é detectável, a atividade de cópia é executada no RI no Leste dos EUA.

- Para a execução de atividades de Pesquisa/GetMetadata/Exclusão (também conhecidas como atividades de Pipeline), despachamento de atividades de transformação (também conhecidas como atividades externas) e operações de autoria (conexão de teste, lista de pastas de navegação e lista de tabelas, dados de visualização), o ADF usará o RI na região da fábrica de dados.

- Para fluxo de dados, o ADF usará o RI na região da fábrica de dados. 

  > [!TIP] 
  > Uma boa prática seria garantir que o fluxo de dados seja executado na mesma região que seus correspondentes armazenamentos de dados (se possível). Você pode conseguir isso resolvendo automaticamente o Azure IR (se a localização do armazenamento de dados for a mesma da localização da Fábrica de Dados), ou criando uma nova instância de IR do Azure na mesma região que seus armazenamentos de dados e, em seguida, executar o fluxo de dados nele. 

Você pode monitorar quais locais do IR entram em vigor durante a execução da atividade na exibição do monitoramento de atividades de pipeline na interface do usuário ou no conteúdo de monitoramento de atividades.

### <a name="self-hosted-ir-location"></a>Local do IR auto-hospedado
O IR auto-hospedado está logicamente registrado para o Data Factory e a computação usada para dar suporte às funcionalidades dele é fornecido por você. Portanto, não há nenhuma propriedade de localização explícita para IR auto-hospedado. 

Quando usado para realizar a movimentação de dados, o IR auto-hospedado extrai dados da origem e grava-os no destino.

### <a name="azure-ssis-ir-location"></a>Local do Azure-SSIS IR
Selecionar a localização certa para o IR do Azure-SSIS é essencial para alcançar alto desempenho em seus fluxos de trabalho de ETL (extrair, transformar e carregar).

- A localização do seu IR Azure-SSIS não precisa ser o mesmo que a localização de sua fábrica de dados, mas deve ser o mesmo que a localização do seu próprio Banco de Dados SQL ou servidor de instância gerenciada do Azure onde o SSISDB deve ser hospedado. Desse modo, o Integration Runtime do Azure-SSIS pode acessar o SSISDB facilmente sem incorrer em tráfegos excessivos entre localizações diferentes.
- Se você não tiver um banco de dados SQL existente ou um servidor de instância gerenciada para hospedar o SSISDB, mas você tiver fontes/destinos de dados no local, você deve criar um novo servidor de banco de dados SQL do Azure ou instância gerenciada no mesmo local de uma rede virtual conectada à sua rede local.  Dessa forma, você pode criar seu IR Azure-SSIS usando o novo banco de dados SQL do Azure ou servidor de instância gerenciada e se juntando a essa rede virtual, tudo no mesmo local, minimizando efetivamente os movimentos de dados em diferentes locais.
- Se a localização do seu banco de dados SQL existente ou do servidor de instância gerenciada onde o SSISDB está hospedado não for o mesmo que a localização de uma rede virtual conectada à sua rede local, primeiro crie seu IR Azure-SSIS usando um Banco de Dados Azure SQL existente ou Servidor de instância gerenciada e se juntar a outra rede virtual no mesmo local e, em seguida, configurar uma rede virtual para conexão de rede virtual entre diferentes locais.

O diagrama a seguir mostra as configurações de localização de Data Factory e os respectivos tempos de execução de integração:

![Localização do Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Determinando qual IR usar

### <a name="copy-activity"></a>Atividade de cópia

Para a atividade de cópia, ela requer que serviços vinculados de origem e de coletor definam a direção do fluxo de dados. A lógica a seguir é usada para determinar qual instância do Integration Runtime é usada para realizar a cópia: 

- **Copiando entre duas fontes de dados de nuvem**: quando os serviços vinculados de origem e o de coletor estiverem usando o IR do Azure, o ADF usará o IR do Azure regional, se especificado, ou determinará automaticamente um local do IR do Azure se você escolher a resolução automática do IR (padrão), conforme descrito na seção [Local de runtime de integração](#integration-runtime-location).
- **Copiando entre uma fonte de dados de nuvem e uma fonte de dados na rede privada**: se o serviço vinculado de origem ou de coletor aponta para um IR auto-hospedado, a atividade de cópia é executada nesse Integration Runtime auto-hospedado.
- **Copiando entre duas fontes de dados em rede privada**: tanto a fonte quanto o sink Linked Service devem apontar para a mesma instância de tempo de execução de integração, e que o tempo de execução de integração é usado para executar a atividade de cópia.

### <a name="lookup-and-getmetadata-activity"></a>Atividade de pesquisa e GetMetadata

A atividade de pesquisa e GetMetadata é executada no runtime de integração associado ao serviço vinculado de armazenamento de dados.

### <a name="external-transformation-activity"></a>Atividade de transformação externa

Cada atividade de transformação externa que utiliza um mecanismo de computação externa tem um serviço vinculado de computação de destino, que aponta para um tempo de execução de integração. Essa instância de tempo de execução de integração determina o local de onde essa atividade de transformação codificada à mão externa é enviada.

### <a name="data-flow-activity"></a>Atividade de Fluxo de Dados

As atividades de Fluxo de Dados são executadas no tempo de execução de integração do Azure associado a ele. A computação Spark utilizada pelos Fluxos de Dados é determinada pelas propriedades de fluxo de dados em seu Tempo de Execução de Integração do Azure e é totalmente gerenciada pelo ADF.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Crie o tempo de execução da integração do Azure](create-azure-integration-runtime.md)
- [Crie tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md)
- [Crie um tempo de execução de integração Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do Banco de Dados SQL do Azure e unir o IR a uma rede virtual. 
