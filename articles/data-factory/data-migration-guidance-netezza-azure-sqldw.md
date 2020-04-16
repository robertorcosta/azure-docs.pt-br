---
title: Migrar dados de um servidor Netezza no local para o Azure
description: Use a Fábrica de Dados Do Azure para migrar dados de um servidor Netezza no local para o Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: a0263880262da95f4d26ee8388da464e9a59efca
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416449"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Use a Fábrica de Dados do Azure para migrar dados de um servidor Netezza no local para o Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo de desempenho, robusto e econômico para migrar dados em escala de um servidor Netezza no local para sua conta de armazenamento Azure ou banco de dados Azure SQL Data Warehouse. 

Este artigo fornece as seguintes informações para engenheiros de dados e desenvolvedores:

> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança de rede
> * Arquitetura de soluções de alto nível 
> * Práticas recomendadas de implementação  

## <a name="performance"></a>Desempenho

A Azure Data Factory oferece uma arquitetura sem servidor que permite o paralelismo em vários níveis. Se você é um desenvolvedor, isso significa que você pode construir pipelines para usar totalmente a largura de banda da rede e do banco de dados para maximizar o throughput de movimentação de dados para o seu ambiente.

![Diagrama de desempenho](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Uma única atividade de cópia pode tirar proveito de recursos de computação escaláveis. Quando você usa o Azure Integration Runtime, você pode especificar [até 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) para cada atividade de cópia de forma sem servidor. Com um tempo de execução de integração auto-hospedado (IR auto-hospedado), você pode escalar manualmente a máquina ou dimensionar para várias[máquinas (até quatro nós)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)e uma única atividade de cópia distribui sua partição em todos os nós. 

- Uma única atividade de cópia é lida e grava no armazenamento de dados usando vários segmentos. 

- O fluxo de controle da Fábrica de Dados do Azure pode iniciar várias atividades de cópia em paralelo. Por exemplo, ele pode iniciá-los usando um [loop For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Para obter mais informações, consulte [O desempenho da atividade de copiar e o guia de escalabilidade](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Resiliência

Dentro de uma única execução de atividade de cópia, a Azure Data Factory possui um mecanismo de repetição incorporado, que permite lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente.

Com a atividade de cópia do Azure Data Factory, quando você copia dados entre os armazenamentos de dados de origem e de sumidouro, você tem duas maneiras de lidar com linhas incompatíveis. Você pode abortar e falhar a atividade de cópia ou continuar a copiar o resto dos dados pulando as linhas de dados incompatíveis. Além disso, para saber a causa da falha, você pode registrar as linhas incompatíveis no armazenamento Azure Blob ou no Azure Data Lake Store, corrigir os dados na fonte de dados e tentar novamente a atividade de cópia.

## <a name="network-security"></a>Segurança de rede 

Por padrão, a Fábrica de Dados do Azure transfere dados do servidor Netezza no local para uma conta de armazenamento Azure ou banco de dados Azure SQL Data Warehouse usando uma conexão criptografada através do Hypertext Transfer Protocol Secure (HTTPS). O HTTPS fornece criptografia de dados em trânsito e evita escutas e ataques man-in-the-middle.

Alternativamente, se você não quiser que os dados sejam transferidos pela internet pública, você pode ajudar a obter maior segurança, transferindo dados através de um link de peering privado via Azure Express Route. 

A próxima seção discute como alcançar maior segurança.

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção discute duas maneiras de migrar seus dados.

### <a name="migrate-data-over-the-public-internet"></a>Migrar dados pela internet pública

![Migrar dados pela internet pública](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Nesta arquitetura, você transfere dados com segurança usando HTTPS na internet pública.

- Para obter essa arquitetura, você precisa instalar o tempo de execução de integração da Fábrica de Dados Do Azure (auto-hospedado) em uma máquina Windows atrás de um firewall corporativo. Certifique-se de que esse tempo de execução de integração possa acessar diretamente o servidor Netezza. Para usar totalmente sua rede e data stores largura de banda para copiar dados, você pode escalar manualmente sua máquina ou dimensionar para várias máquinas.

- Usando essa arquitetura, você pode migrar tanto dados iniciais de snapshot quanto dados delta.

### <a name="migrate-data-over-a-private-network"></a>Migrar dados sobre uma rede privada 

![Migrar dados sobre uma rede privada](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Nesta arquitetura, você migra dados através de um link de peering privado via Azure Express Route, e os dados nunca atravessam a internet pública. 

- Para obter essa arquitetura, você precisa instalar o tempo de execução de integração da Fábrica de Dados Do Azure (auto-hospedado) em uma máquina virtual do Windows (VM) dentro de sua rede virtual Azure. Para usar totalmente sua rede e data stores largura de banda para copiar dados, você pode escalar manualmente sua VM ou dimensionar para várias VMs.

- Usando essa arquitetura, você pode migrar tanto dados iniciais de snapshot quanto dados delta.

## <a name="implement-best-practices"></a>Implementar práticas recomendadas 

### <a name="manage-authentication-and-credentials"></a>Gerenciar autenticação e credenciais 

- Para autenticar o Netezza, você pode usar a [autenticação ODBC via string de conexão](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 

- Para autenticar o armazenamento Do Azure Blob: 

   - Recomendamos o uso de [identidades gerenciadas para recursos do Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) Construída supérvisual da Fábrica de Dados Azure gerenciada automaticamente no Azure Active Directory (Azure AD), as identidades gerenciadas permitem configurar pipelines sem ter que fornecer credenciais na definição do Serviço Vinculado.  

   - Alternativamente, você pode autenticar o armazenamento do Azure Blob usando [o principal do serviço,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)uma [assinatura de acesso compartilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou uma chave de conta [de armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 

- Para autenticar o Azure Data Lake Storage Gen2: 

   - Recomendamos o uso de [identidades gerenciadas para recursos do Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)
   
   - Você também pode usar [o diretor de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou uma chave de conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 

- Para autenticar no Azure SQL Data Warehouse:

   - Recomendamos o uso de [identidades gerenciadas para recursos do Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)
   
   - Você também pode usar [o serviço principal](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) ou [autenticação SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication).

- Quando você não está usando identidades gerenciadas para recursos do Azure, recomendamos armazenar [as credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para facilitar o gerenciamento central e a rotação de chaves sem ter que modificar os serviços vinculados à Fábrica de Dados do Azure. Esta também é uma das [melhores práticas para CI/CD.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd) 

### <a name="migrate-initial-snapshot-data"></a>Migrar dados iniciais de instantâneo 

Para tabelas pequenas (ou seja, tabelas com um volume inferior a 100 GB ou que podem ser migradas para o Azure dentro de duas horas), você pode fazer cada cópia de dados de carga de trabalho por tabela. Para maior throughput, você pode executar vários trabalhos de cópia da Fábrica de Dados do Azure para carregar tabelas separadas simultaneamente. 

Dentro de cada trabalho de cópia, para executar consultas paralelas e copiar dados por partições, você também pode alcançar algum nível de paralelismo usando a [ `parallelCopies` configuração](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) de propriedade com qualquer uma das seguintes opções de partição de dados:

- Para obter maior eficiência, encorajamos você a começar a partir de uma fatia de dados.  Certifique-se de que `parallelCopies` o valor na configuração é menor do que o número total de partições fatiadas de dados em sua tabela no servidor Netezza.  

- Se o volume de cada partição de fatia de dados ainda for grande (por exemplo, 10 GB ou mais), encorajamos você a mudar para uma partição de intervalo dinâmico. Esta opção lhe dá maior flexibilidade para definir o número de partições e o volume de cada partição por coluna de partição, limite superior e limite inferior.

Para tabelas maiores (ou seja, tabelas com um volume de 100 GB ou superior ou que *não podem* ser migradas para o Azure dentro de duas horas), recomendamos que você particione os dados por consulta personalizada e, em seguida, faça cada cópia de trabalho de cópia de uma partição por vez. Para obter um melhor throughput, você pode executar vários trabalhos de cópia da Fábrica de Dados do Azure simultaneamente. Para cada objetivo de trabalho de cópia de carregar uma partição por consulta personalizada, você pode aumentar o throughput ativando o paralelismo através de fatia de dados ou alcance dinâmico. 

Se qualquer trabalho de cópia falhar por causa de um problema transitório de rede ou armazenamento de dados, você pode executar o trabalho de cópia com falha para recarregar essa partição específica da tabela. Outros trabalhos de cópia que carregam outras partições não são afetados.

Quando você carrega dados em um banco de dados do Azure SQL Data Warehouse, sugerimos que você habilite o PolyBase no trabalho de cópia com o armazenamento Do Azure Blob como estágio.

### <a name="migrate-delta-data"></a>Migrar dados delta 

Para identificar as linhas novas ou atualizadas da sua tabela, use uma coluna de carimbo de ponto-e-hora ou uma chave de incremento dentro do esquema. Em seguida, você pode armazenar o valor mais recente como uma marca d'água alta em uma tabela externa e, em seguida, usá-lo para filtrar os dados delta na próxima vez que você carregar dados. 

Cada tabela pode usar uma coluna de marca d'água diferente para identificar suas linhas novas ou atualizadas. Sugerimos que crie uma mesa de controle externa. Na tabela, cada linha representa uma tabela no servidor Netezza com seu nome de coluna de marca d'água específico e alto valor de marca d'água. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Configure um tempo de execução de integração auto-hospedado

Se você está migrando dados do servidor Netezza para o Azure, se o servidor está no local por trás do firewall da sua corporação ou dentro de um ambiente de rede virtual, você precisa instalar um IR auto-hospedado em uma máquina Windows ou VM, que é o mecanismo que é usado para mover dados. Como você está instalando o IR auto-hospedado, recomendamos a seguinte abordagem:

- Para cada máquina Windows ou VM, comece com uma configuração de 32 vCPU e memória de 128 GB. Você pode continuar monitorando o uso da CPU e da memória da máquina de IR durante a migração de dados para ver se você precisa escalar ainda mais a máquina para obter um melhor desempenho ou reduzir a máquina para economizar custos.

- Você também pode dimensionar associando até quatro nós com um único IR auto-hospedado. Um único trabalho de cópia que está sendo executado contra um IR auto-hospedado aplica automaticamente todos os nós vm para copiar os dados em paralelo. Para alta disponibilidade, comece com quatro nós VM para evitar um único ponto de falha durante a migração de dados.

### <a name="limit-your-partitions"></a>Limite suas partições

Como uma prática recomendada, conduza uma prova de desempenho de conceito (POC) com um conjunto de dados de amostra representativa, para que você possa determinar um tamanho de partição apropriado para cada atividade de cópia. Sugerimos que você carregue cada partição para o Azure dentro de duas horas.  

Para copiar uma tabela, comece com uma única atividade de cópia com uma única máquina de RI auto-hospedada. Aumente gradualmente `parallelCopies` a configuração com base no número de partições de fatias de dados em sua tabela. Veja se a tabela inteira pode ser carregada no Azure dentro de duas horas, de acordo com o throughput que resulta do trabalho de cópia. 

Se ele não puder ser carregado no Azure dentro de duas horas, e a capacidade do nó IR auto-hospedado e do armazenamento de dados não for totalmente usado, aumente gradualmente o número de atividades de cópia simultâneas até atingir o limite da sua rede ou o limite de largura de banda dos armazenamentos de dados. 

Continue monitorando o uso da CPU e da memória na máquina de IR auto-hospedada e esteja pronto para escalar a máquina ou dimensionar para várias máquinas quando você ver que a CPU e a memória estão totalmente usadas. 

Quando você encontrar erros de estrangulamento, conforme relatado pela atividade de cópia `parallelCopies` da Fábrica de Dados do Azure, reduza a concorrência ou a configuração na Fábrica de Dados do Azure ou considere aumentar os limites de largura de banda ou I/O por segundo (IOPS) da rede e dos armazenamentos de dados. 


### <a name="estimate-your-pricing"></a>Estime seus preços 

Considere o seguinte pipeline, que é construído para migrar dados do servidor Netezza no local para um banco de dados Azure SQL Data Warehouse:

![O pipeline de preços](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Vamos supor que as seguintes afirmações são verdadeiras: 

- O volume total de dados é de 50 terabytes (TB). 

- Estamos migrando dados usando a arquitetura de primeira solução (o servidor Netezza está no local, atrás do firewall).

- O volume de 50 TB é dividido em 500 partições, e cada atividade de cópia move uma partição.

- Cada atividade de cópia é configurada com um IR auto-hospedado contra quatro máquinas e alcança um throughput de 20 megabytes por segundo (MBps). (Dentro da `parallelCopies` atividade de cópia, é definido como 4, e cada segmento para carregar dados da tabela atinge um throughput de 5 MBps.)

- A suporia ForEach é definida como 3, e o throughput agregado é de 60 MBps.

- No total, leva 243 horas para concluir a migração.

Com base nas suposições anteriores, aqui está o preço estimado: 

![A tabela de preços](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> O preço mostrado na tabela anterior é hipotético. Seu preço real depende do produto real em seu ambiente. O preço da máquina Windows (com o IR auto-hospedado instalado) não está incluído. 

### <a name="additional-references"></a>Referências adicionais

Para obter mais informações, consulte os seguintes artigos e guias:

- [Migre os dados de um banco de dados relacional no local para o Azure usando a Fábrica de Dados do Azure](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Conector Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Conector do ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Conector de armazenamento Azure Blob](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Conector do Azure Data Lake Store Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Conector do armazém de dados Azure SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Guia de ajuste de desempenho de atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Criar e configurar um runtime de integração auto-hospedada](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [HA de tempo de execução de integração auto-hospedado e escalabilidade](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerações de segurança da movimentação de dados](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Armazenar credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiar dados incrementalmente de uma tabela](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Copiar dados incrementalmente de várias tabelas](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Página de preços da Fábrica de Dados Do Azure](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos de vários contêineres usando a Fábrica de Dados do Azure](solution-template-copy-files-multiple-containers.md)
