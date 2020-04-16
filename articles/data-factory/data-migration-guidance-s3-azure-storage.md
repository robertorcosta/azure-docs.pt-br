---
title: Migrar dados do Amazon S3 para o Armazenamento do Azure
description: Use a Fábrica de Dados Do Azure para migrar dados do Amazon S3 para o Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 3f40ad7346219b48a38ade38b2a75ddf71940875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416410"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Use a fábrica de dados do Azure para migrar dados do Amazon S3 para o Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo de desempenho, robusto e econômico para migrar dados em escala do Amazon S3 para o Azure Blob Storage ou o Azure Data Lake Storage Gen2.  Este artigo fornece as seguintes informações para engenheiros de dados e desenvolvedores: 

> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança de rede
> * Arquitetura de soluções de alto nível 
> * Práticas recomendadas de implementação  

## <a name="performance"></a>Desempenho

O ADF oferece uma arquitetura sem servidor que permite o paralelismo em diferentes níveis, o que permite que os desenvolvedores construam pipelines para utilizar totalmente sua largura de banda de rede, bem como IOPS de armazenamento e largura de banda para maximizar o throughput de movimentação de dados para o seu ambiente. 

Os clientes migraram com sucesso petabytes de dados que consistem em centenas de milhões de arquivos do Amazon S3 para o Azure Blob Storage, com um throughput sustentado de 2 GBps e superior. 

![desempenho](media/data-migration-guidance-s3-to-azure-storage/performance.png)

A imagem acima ilustra como você pode alcançar grandes velocidades de movimento de dados através de diferentes níveis de paralelismo:
 
- Uma única atividade de cópia pode tirar proveito de recursos de computação escaláveis: ao usar o Azure Integration Runtime, você pode especificar [até 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) para cada atividade de cópia de forma sem servidor; ao usar o Executtime de Integração auto-hospedado, você pode escalar manualmente a máquina ou dimensionar para várias[máquinas (até 4 nós)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)e uma única atividade de cópia irá particionar seu conjunto de arquivos em todos os nós. 
- Uma única atividade de cópia é lida e grava no armazenamento de dados usando vários segmentos. 
- O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando [For Each loop](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Resiliência

Dentro de uma única atividade de cópia executada, o ADF possui um mecanismo de repetição incorporado para que possa lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente. 

Ao fazer cópias binárias de S3 para Blob e de S3 para ADLS Gen2, o ADF executa automaticamente o checkpoint.  Se uma execução de atividade de cópia tiver falhado ou cronometrado, em uma repetição subseqüente, a cópia será retomada a partir do último ponto de falha em vez de começar do início. 

## <a name="network-security"></a>Segurança de rede 

Por padrão, o ADF transfere dados do Amazon S3 para o Azure Blob Storage ou o Azure Data Lake Storage Gen2 usando conexão criptografada através do protocolo HTTPS.  O HTTPS fornece criptografia de dados em trânsito e evita escutas e ataques man-in-the-middle. 

Alternativamente, se você não quiser que os dados sejam transferidos pela Internet pública, você pode obter maior segurança transferindo dados por um link de peering privado entre o AWS Direct Connect e o Azure Express Route.  Consulte a arquitetura da solução abaixo sobre como isso pode ser alcançado. 

## <a name="solution-architecture"></a>Arquitetura da solução

Migrar dados pela Internet pública:

![arquitetura de soluções-rede pública](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Nesta arquitetura, os dados são transferidos com segurança usando HTTPS pela Internet pública. 
- Tanto a fonte Amazon S3 quanto o destino Azure Blob Storage ou Azure Data Lake Storage Gen2 estão configurados para permitir o tráfego de todos os endereços IP da rede.  Consulte a segunda arquitetura abaixo sobre como você pode restringir o acesso à rede a uma gama de IP específica. 
- Você pode facilmente aumentar a quantidade de potência de maneira sem servidor para utilizar totalmente sua rede e largura de banda de armazenamento para que você possa obter o melhor throughput para o seu ambiente. 
- Tanto a migração inicial de instantâneos quanto a migração de dados delta podem ser alcançadas usando essa arquitetura. 

Migrar dados através de link privado: 

![arquitetura de soluções-rede privada](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Nesta arquitetura, a migração de dados é feita por meio de um link de peering privado entre o AWS Direct Connect e o Azure Express Route, de tal forma que os dados nunca atravessam a Internet pública.  Requer o uso da rede AWS VPC e Azure Virtual. 
- Você precisa instalar o tempo de execução de integração auto-hospedado do ADF em uma VM do Windows dentro da sua rede virtual Azure para alcançar essa arquitetura.  Você pode escalar manualmente suas VMs IR auto-hospedadas ou dimensionar para várias VMs (até 4 latinos) para utilizar totalmente sua rede e armazenamento IOPS/largura de banda. 
- Se for aceitável transferir dados pelo HTTPS, mas você quiser bloquear o acesso de rede à fonte S3 para uma gama IP específica, você pode adotar uma variação dessa arquitetura removendo o AWS VPC e substituindo o link privado pelo HTTPS.  Você vai querer manter o Azure Virtual e o IR auto-hospedado no Azure VM para que você possa ter um IP estático publicamente routable para fins de listagem branca. 
- Tanto a migração inicial de dados de instantâneos quanto a migração de dados delta podem ser alcançadas usando essa arquitetura. 

## <a name="implementation-best-practices"></a>Práticas recomendadas de implementação 

### <a name="authentication-and-credential-management"></a>Autenticação e gerenciamento de credenciais 

- Para autenticar a conta Amazon S3, você deve usar [a chave de acesso para conta IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Vários tipos de autenticação são suportados para se conectar ao Azure Blob Storage.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) é altamente recomendado: construído em cima de uma identificação ADF gerenciada automaticamente no Azure AD, permite configurar pipelines sem fornecer credenciais na definição do Linked Service.  Alternativamente, você pode autenticar o Azure Blob Storage usando [o Service Principal,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [assinatura de acesso compartilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou chave da conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 
- Vários tipos de autenticação também são suportados para se conectar ao Azure Data Lake Storage Gen2.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) é altamente recomendado, embora [o principal de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou a chave da conta de [armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) também possam ser usados. 
- Quando você não está usando identidades gerenciadas para recursos do Azure, [armazenar as credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) é altamente recomendado para facilitar o gerenciamento central e a rotação de chaves sem modificar os serviços vinculados ao ADF.  Esta também é uma das [melhores práticas para CI/CD.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd) 

### <a name="initial-snapshot-data-migration"></a>Migração inicial de dados de instantâneo 

A partição de dados é recomendada especialmente ao migrar mais de 100 TB de dados.  Para particionar os dados, aproveite a configuração 'prefixo' para filtrar as pastas e arquivos no Amazon S3 pelo nome e, em seguida, cada trabalho de cópia do ADF pode copiar uma partição de cada vez.  Você pode executar vários trabalhos de cópia do ADF simultaneamente para obter um melhor throughput. 

Se algum dos trabalhos de cópia falhar devido a um problema transitório de rede ou armazenamento de dados, você pode executar novamente o trabalho de cópia com falha para recarregar essa partição específica novamente do AWS S3.  Todos os outros trabalhos de cópia carregando outras partições não serão impactados. 

### <a name="delta-data-migration"></a>Migração de dados Delta 

A maneira mais performática de identificar arquivos novos ou alterados do AWS S3 é usando a convenção de nomeação particionada por tempo – quando seus dados no AWS S3 foram particionados com informações de fatia de tempo no nome do arquivo ou da pasta (por exemplo, /yyyy/mm/dd/file.csv), então seu pipeline pode facilmente identificar quais arquivos/pastas copiarão incrementalmente. 

Alternativamente, se seus dados no AWS S3 não forem particionados por tempo, o ADF poderá identificar arquivos novos ou alterados por sua Data LastModifiedDate.   A maneira como funciona é que o ADF irá escanear todos os arquivos do AWS S3, e apenas copiar o arquivo novo e atualizado cujo último carimbo de tempo modificado é maior do que um determinado valor.  Esteja ciente de que se você tiver um grande número de arquivos no S3, a varredura inicial do arquivo pode levar um longo tempo, independentemente de quantos arquivos correspondem à condição do filtro.  Neste caso, é sugerido que você particione os dados primeiro, usando a mesma configuração 'prefixo' para migração inicial de instantâneos, para que a varredura de arquivos possa acontecer em paralelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Para cenários que requerem tempo de execução de Integração auto-hospedado no Azure VM 

Se você está migrando dados para um link privado ou deseja permitir um intervalo ip específico no firewall Amazon S3, você precisa instalar o tempo de execução de Integração auto-hospedado no Azure Windows VM. 

- A configuração recomendada para começar para cada Azure VM é Standard_D32s_v3 com memória de 32 vCPU e 128 GB.  Você pode continuar monitorando a utilização da CPU e da memória do IR VM durante a migração de dados para ver se você precisa escalar ainda mais a VM para obter melhor desempenho ou reduzir a VM para economizar custos. 
- Você também pode dimensionar associando até 4 nós VM com um único IR auto-hospedado.  Um único trabalho de cópia executando contra um IR auto-hospedado irá particionar automaticamente o conjunto de arquivos e aproveitar todos os nós de VM para copiar os arquivos em paralelo.  Para alta disponibilidade, recomenda-se começar com 2 nós VM para evitar um único ponto de falha durante a migração de dados. 

### <a name="rate-limiting"></a>Limitação de taxa 

Como uma prática recomendada, conduza um POC de desempenho com um conjunto de dados de amostra representativa, para que você possa determinar um tamanho de partição apropriado. 

Comece com uma única partição e uma única atividade de cópia com a configuração DIU padrão.  Aumente gradualmente a configuração DIU até atingir o limite de largura de banda da sua rede ou limite de largura de banda dos armazenamentos de dados, ou você atingiu o máximo de 256 DIU permitido em uma única atividade de cópia. 

Em seguida, aumente gradualmente o número de atividades de cópia simultâneas até atingir os limites do seu ambiente. 

Quando você encontrar erros de estrangulamento relatados pela atividade de cópia do ADF, reduza a configuração de simultâneo ou DIU no ADF ou considere aumentar os limites de largura de banda/IOPS da rede e dos armazenamentos de dados.  

### <a name="estimating-price"></a>Preço estimado 

> [!NOTE]
> Este é um exemplo hipotético de preços.  Seu preço real depende do produto real em seu ambiente.

Considere o seguinte pipeline construído para migrar dados de S3 para Azure Blob Storage: 

![pipeline de preços](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Vamos assumir o seguinte: 

- O volume total de dados é de 2 PB 
- Migração de dados sobre HTTPS usando a arquitetura da primeira solução 
- 2 PB é dividido em partições de 1 K e cada cópia move uma partição 
- Cada cópia é configurada com DIU=256 e atinge 1 GBps throughput 
- ForEach sicurrency é definido para 2 e throughput agregado é de 2 GBps 
- No total, leva 292 horas para concluir a migração 

Aqui está o preço estimado com base nas premissas acima: 

![tabela de preços](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Referências adicionais 
- [Conector do Serviço de Armazenamento Simples da Amazon](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob Storage connector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage) (Conector do Armazenamento de Blobs do Azure)
- [Conector do Azure Data Lake Store Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guia de ajuste de desempenho de atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Criação e configuração de execução de integração auto-hospedada](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [HA de tempo de execução de integração auto-hospedado e escalabilidade](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerações de segurança da movimentação de dados](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Armazenar credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiar arquivo incrementalmente com base no nome do arquivo particionado por tempo](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copiar arquivos novos e alterados com base no LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Página de preços do ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modelo

Aqui está o [modelo](solution-template-migration-s3-azure.md) para começar a migrar petabytes de dados que consistem em centenas de milhões de arquivos do Amazon S3 para o Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)
