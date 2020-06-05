---
title: Guia de dimensionamento do Cluster de Consulta Interativa do HDInsight (LLAP)
description: Guia de dimensionamento de LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: a9b86f09ade0d437436779ef3e4a17fcdede2cf0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663684"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Guia de dimensionamento do Cluster de Consulta Interativa do Azure HDInsight (Hive LLAP)

Este documento descreve o dimensionamento do cluster de Consulta Interativa do HDInsight (cluster Hive LLAP) para uma carga de trabalho normal para obter um desempenho aceitável. As recomendações deste documento são diretrizes genéricas e cargas de trabalho específicas que podem precisar de  ajuste específico.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Tipos de VM padrão do Azure para o Cluster de consulta Interativa do HDInsight (LLAP)**

| Tipo de nó      | Instância | Tamanho     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPUs, 56 GB de RAM, SSD de 400 GB   |
| Trabalho   | **D14 v2**        | **16 vCPUs, 112 GB de RAM, SSD de 800 GB**       |
| ZooKeeper   | A4 v2        | 4 vCPUs, 8 GB de RAM, SSD de 40 GB       |

***Observação: Todos os valores de configuração recomendados se baseiam no nó de trabalho do tipo D14 v2***  

### <a name="configuration"></a>**Configuração:**    
| Chave de configuração      | Valor recomendado  | Descrição |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memória total fornecida, em MB, para todos os contêineres de YARN em um nó | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | A alocação máxima de cada solicitação de contêiner no RM, em MBs. As solicitações de memória maiores que esse valor não entrarão em vigor |
| yarn.scheduler.maximum-allocation-vcores | 12 |O número máximo de núcleos de CPU de cada solicitação de contêiner no Resource Manager. As solicitações maiores que esse valor não entrarão em vigor. |
| yarn.nodemanager.resource.cpu-vcores | 12 | Número de núcleos de CPU por NodeManager que podem ser alocados para contêineres. |
| yarn.scheduler.capacity.root.llap.capacity | 80 (%) | Alocação de capacidade do YARN para fila de LLAP  |
| tez.am.resource.memory.mb | 4096 (MB) | A quantidade de memória em MB a ser usada pelo tez AppMaster |
| hive.server2.tez.sessions.per.default.queue | <número_de_nós_de_trabalho> |O número de sessões de cada fila chamada no hive.server2.tez.default.queues. Esse número corresponde ao número de coordenadores de consulta (Tez AMs) |
| hive.tez.container.size | 4096 (MB) | Tamanho do contêiner Tez especificado em MB |
| hive.llap.daemon.num.executors | 12 | Número de executores por daemon de LLAP | 
| hive.llap.io.threadpool.size | 12 | Tamanho do pool de threads para executores |
| hive.llap.daemon.yarn.container.mb | 77824 (MB) | Memória total em MB usada por daemons de LLAP individuais (memória por daemon)
| hive.llap.io.memory.size | 235520 (MB) | Tamanho do cache em MB por daemon LLAP, desde que o cache de SSD esteja habilitado |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | tamanho da memória em MB para fazer a Junção de mapa |

### <a name="llap-daemon-size-estimations"></a>**Estimativas de tamanho do daemon de LLAP:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Determinação de alocação total de memória YARN para todos os contêineres em um nó**    
Configuração: ***yarn.nodemanager.resource.memory-mb***  

Esse valor indica uma soma máxima da memória em MB que pode ser usada pelos contêineres YARN em cada nó. O valor especificado deve ser menor que a quantidade total de memória física nesse nó.   
Memória total para todos os contêineres YARN em um nó = [memória física total] – [memória para o sistema operacional + outros serviços]  
Defina esse valor como aproximadamente 90% da RAM disponível.  
Para D14 v2, o valor recomendado é  **102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Determinação da quantidade máxima de memória por solicitação de contêiner do YARN**  
Configuração: ***yarn.scheduler.maximum-allocation-mb***

Esse valor indica a alocação máxima de cada solicitação de contêiner no Azure Resource Manager, em MB. As solicitações de memória maiores que o valor especificado não entrarão em vigor. O Resource Manager pode fornecer memória para contêineres em incrementos de *yarn.scheduler.minimum-allocation-mb* e não pode exceder o tamanho especificado por *yarn.scheduler.maximum-allocation-mb*. O valor especificado não deve ser maior que a memória total especificada para todos os contêineres no nó especificado por *yarn.nodemanager.resource.memory-mb*.    
Para nós de trabalho D14 v2, o valor recomendado é **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Determinação da quantidade máxima de vcores por solicitação de contêiner do YARN**  
Configuração: ***yarn.scheduler.maximum-allocation-vcores***  

Esse valor indica o número máximo de núcleos de CPU virtual de cada solicitação de contêiner no Resource Manager. Solicitar um número maior de vcores do que esse valor não terá efeito. É uma propriedade global do Agendador do YARN. Para o contêiner do daemon de LLAP, esse valor pode ser definido como 75% do total de vcores disponíveis. Os 25% restantes devem ser reservados para NodeManager, DataNode e outros serviços em execução nos nós de trabalho.  
Há 16 vcores em VMs D14 v2 e 75% do total de 16 vcores podem ser usadas pelo contêiner do daemon de LLAP.  
Para D14 v2, o valor recomendado é **12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. Número de consultas simultâneas**  
Configuração: ***hive.server2.tez.sessions.per.default.queue***

Esse valor de configuração determina o número de sessões de Tez que podem ser iniciadas paralelamente. Essas sessões de Tez serão iniciadas para cada uma das filas especificadas por "hive.server2.tez.default.queues". Isso corresponde ao número de Tez AMs (Coordenadores de Consulta). É recomendável que seja igual ao número de nós de trabalho. O número de Tez AMs pode ser maior que o número de nós do daemon de LLAP. Sua principal responsabilidade é coordenar a execução da consulta e atribuir fragmentos do plano de consulta aos daemons de LLAP correspondentes para execução. Mantenha esse valor como múltiplo de vários nós daemon LLAP para obter maior taxa de transferência.  

O cluster HDInsight padrão tem quatro daemons de LLAP em execução em quatro nós de trabalho. Portanto, o valor recomendado é **4**.  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. O contêiner Tez e o tamanho mestre do aplicativo Tez**    
Configuração: ***tez.am.resource.memory.mb, hive.tez.container.size***  

*tez.am.resource.memory.mb* - define o tamanho mestre do aplicativo Tez.  
O valor recomendado é **4096 MB**.
   
*hive.tez.container.size* - define a quantidade de memória fornecida para o contêiner Tez. Esse valor deve ser definido entre o tamanho mínimo do contêiner YARN (*yarn.scheduler.minimum-allocation-mb*) e o tamanho máximo de contêiner YARN (*yarn.scheduler.maximum-allocation-mb*). Os executores de daemon de LLAP usam esse valor para limitar o uso de memória por executor.  
O valor recomendado é **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. Alocação de capacidade de fila de LLAP**   
Configuração: ***yarn.scheduler.capacity.root.llap.capacity***  

Esse valor indica uma porcentagem da capacidade fornecida para a fila de LLAP. As alocações de capacidade podem ter valores diferentes para cargas de trabalho diferentes, dependendo de como as filas do YARN estão configuradas. Se sua carga de trabalho for de operações somente leitura, defini-la como 90% da capacidade deve funcionar. No entanto, se sua carga de trabalho for uma combinação de operações de atualização/exclusão/mesclagem usando tabelas gerenciadas, dê 80% da capacidade para a fila de LLAP. A capacidade restante de 20% pode ser usada por outras tarefas, como compactação etc. para alocar contêineres da fila padrão. Dessa forma, as tarefas na fila padrão não retirarão os recursos do YARN.    
Para nós de trabalho D14v2, o valor recomendado para a fila de LLAP é **80**.   
(Para cargas de trabalho somente leitura, pode ser aumentado até 90 como for adequado.)  

#### <a name="7-llap-daemon-container-size"></a>**7. Tamanho do contêiner do daemon de LLAP**    
Configuração: ***hive.llap.daemon.yarn.container.mb***  
   
O daemon de LLAP é executado como um contêiner do YARN em cada nó de trabalho. O tamanho total da memória para o contêiner do daemon de LLAP depende dos seguintes fatores,    
*  Configurações do tamanho do contêiner do YARN (yarn.scheduler.minimum-allocation-mb, yarn.scheduler.maximum-allocation-mb, yarn.nodemanager.resource.memory-mb)
*  Número de Tez AMs em um nó
*  Memória total configurada para todos os contêineres em um nó e capacidade de fila de LLAP  

A memória necessária pelo Tez AM (Tez Application Masters) pode ser calculada da seguinte maneira.  
Para o cluster interativo do HDInsight, por padrão, há um Tez AM por nó de trabalho que atua como um coordenador de consultas. O número de tez AMs pode ser configurado com base em várias consultas simultâneas a serem atendidas.
É recomendável ter 4 GB de memória por Tez AM.

Memória do Tez AM por nó = [ ceil(número de Tez AMs / Número de nós do daemon de LLAP) ]  x [ tamanho do contêiner do Tez AM ]  
Para D14 v2, a configuração padrão tem quatro Tez AMs e quatro nós de daemon de LLAP.  
Memória do Tez AM por nó = (ceil(4/4) x 4 GB) = 4 GB

A memória total disponível para a fila de LLAP por nó de trabalho pode ser calculada da seguinte maneira: Esse valor depende da quantidade total de memória disponível para todos os contêineres de YARN em um nó (*yarn.nodemanager.resource.memory-mb*) e a porcentagem de capacidade configurada para a fila de LLAP (*yarn.scheduler.capacity.root.llap.capacity*).  
Memória total para a fila de LLAP no nó de trabalho = total de memória disponível para todos os contêineres de YARN em um nó x percentual de capacidade para a fila de LLAP.  
Para D14 v2, este valor é [ 100 GB x 0,80 ] = 80 GB.

O tamanho do contêiner do daemon de LLAP é calculado da seguinte maneira;

**Tamanho do contêiner do daemon de LLAP = [memória total disponível para a fila de LLAP] – [memória do Tez AM por nó]**  
    
Para o nó de trabalho D14 v2, HDI 4.0 - o valor recomendado é (80 GB - 4 GB) = **76 GB**   
(Para HDI 3.6, o valor recomendado é **74 GB** porque você deve reservar cerca de 2 GB adicionais para o AM do controle deslizante).  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Determinação do número de executores por daemon de LLAP**  
Configuração: ***hive.llap.daemon.num.executors***, ***hive.llap.io.threadpool.size***

***hive.llap.daemon.num.executors***:   
Essa configuração controla o número de executores que podem realizar tarefas em paralelo por daemon LLAP. Esse valor depende do número de vcores, a quantidade de memória fornecida por executor e a quantidade de memória total disponível para o daemon de LLAP. Normalmente, gostaríamos que esse valor fosse o mais próximo possível do número de vcores.
Há 16 vcores em VMs D14 v2. No entanto, nem todos os vcores podem ser utilizados porque outros serviços como NodeManager, DataNode, Monitor de Métricas etc. também precisam de uma parte dos vcores disponíveis. 

Se você precisar ajustar o número de executores, use 4 GB de memória por executor, conforme especificado por *hive.tez.container.size* e verifique se a memória total necessária para todos os executores não excede a memória total disponível para o contêiner do daemon do LLAP.  
Esse valor pode ser configurado até 75% do total de vcores disponíveis nesse nó.  
Para D14 v2, o valor recomendado é (0,75 X 16) = **12**

***hive.llap.io.threadpool.size***:   
Esse valor especifica o tamanho do pool de threads para executores. Como os executores são corrigidos conforme especificado, será igual ao número de executores por daemon de LLAP.   
Para D14 v2, o valor recomendado é **12**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Determinando o tamanho do cache do daemon de LLAP**  
Configuração: ***hive.llap.io.memory.size***

A memória do contêiner do daemon do LLAP consiste nos seguintes componentes;
*  Reserva dinâmica
*  Memória de heap usada pelos executores (Xmx)
*  Cache na memória por daemon (seu tamanho de memória fora do heap, não aplicável quando o cache SSD está habilitado)
*  Tamanho de metadados de cache na memória (aplicável somente quando o cache de SSD está habilitado)

**Tamanho da reserva dinâmica**: Este tamanho indica uma parte da memória fora do heap usada para sobrecarga de VM Java (metaespaço, pilha de threads, estruturas de dados GC etc.). Em geral, essa sobrecarga é de cerca de 6% do tamanho do heap (Xmx). Para estar no lado mais seguro, este valor pode ser calculado como 6% do tamanho total da memória do daemon de LLAP.  
Para D14 v2, o valor recomendado é teto (76 GB x 0,06) ~= **5 GB**.  

**Tamanho do heap(Xmx)** : É a quantidade de memória heap disponível para todos os executores.
Tamanho total do heap = número de executores x 4 GB  
Para D14 v2, este valor é 12 x 4 GB = **48 GB**  

Se o cache de SSD estiver desabilitado, o cache na memória será a quantidade de memória restante após a retirada do tamanho de reserva dinâmica e o tamanho do heap do tamanho do contêiner do daemon do LLAP.

O cálculo do tamanho do cache difere quando o cache de SSD está habilitado.
A configuração de *hive.llap.io.allocator.mmap* = true habilitará o cache de SSD.
Quando o cache SSD está habilitado, parte da memória será usada para armazenar metadados para o cache de SSD. Os metadados são armazenados na memória e espera-se que seja aproximadamente 10% do tamanho do cache de SSD.   
Tamanho dos metadados do cache de SSD na memória = [ tamanho do contêiner do daemon do LLAP ] - [ reserva dinâmica + tamanho do heap ]  
Para D14 v2, com HDI 4.0, o tamanho dos metadados do cache de SSD na memória = [76 GB]-[5 GB + 48 GB] = **23 GB**  
Para D14 v2, com HDI 3.6, o tamanho dos metadados do cache de SSD na memória = [76 GB]-[5 GB + 48 GB + controle deslizante de 2 GB] = **21 GB**

Considerando o tamanho da memória disponível para armazenar metadados de cache de SSD, podemos calcular o tamanho do cache de SSD que pode ser suportado.  
Tamanho dos metadados na memória para o cache de SSD = 10% do tamanho do cache de SSD       
Tamanho do cache de SSD = tamanho dos metadados na memória para o cache de SSD x 10  

Para D14 v2 e HDI 4.0, o tamanho recomendado do cache de SSD = 23 GB x 10 = **230 GB**  
Para D14 v2 e HDI 3.6, o tamanho recomendado do cache de SSD = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Ajuste da memória de junção de mapa**   
Configuração: ***hive.auto.convert.join.noconditionaltask.size***

Verifique se você tem *hive.auto.convert.join.noconditionaltask* habilitado para que esse parâmetro entre em vigor.
Essa configuração permite especificar o tamanho das tabelas que podem caber na memória para fazer a Junção de mapa. Se a soma do tamanho de tabelas ou partições n-1 para a junção de n vias for menor que o valor configurado, a Junção de mapa é escolhida. O tamanho da memória do executor de LLAP deve ser usado para calcular o limite da conversão automática para a Junção de mapa.
Supõe-se que cada executor tenha 4 GB de tamanho de heap, mas nem todos estarão disponíveis para a junção de mapa. Algumas memórias de heap serão usadas para buffers de classificação, buffers de ordem aleatória, tabelas de hash etc. por outras operações também. Portanto, você pode fornecer 50% de 4 GB de memória de heap para a junção de mapa.  
Observação: Esse valor pode precisar de ajustes adequados para sua carga de trabalho. Se você definir esse valor muito baixo, não poderá usar o recurso de conversão automática. E configurá-lo muito alto poderá resultar em indisponibilidade de exceções de memória ou em pausas de GC que podem resultar em desempenho adverso.  
Para D14 v2, com 4 GB de memória por executor, é recomendável definir esse valor como **2048 MB**.


#### <a name="next-steps"></a>**Próximas etapas**
Se definir esses valores não resolver o problema, visite um dos seguintes...

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o Suporte técnico é fornecido por meio de um dos [Planos de Suporte do Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Outras referências:**
  * [Configurar outras propriedades de LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Configurar o tamanho do heap do servidor do Apache Hive](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Tamanho da memória do mapa de junção para LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Propriedades do mecanismo de execução do Tez](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Aprofundamento do LLAP do Apache Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)  
