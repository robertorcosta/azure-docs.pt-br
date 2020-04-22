---
title: Arquiteturas de referência para bancos de dados Oracle no Azure | Microsoft Docs
description: Referências arquiteturas para executar bancos de dados Oracle Database Enterprise Edition em Máquinas Virtuais do Microsoft Azure.
services: virtual-machines-linux
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: bbb6665299ce9b6521eeb8801d8621dfbdc17f4a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683481"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Arquiteturas de referência para Oracle Database Enterprise Edition no Azure

Este guia detalha informações sobre a implantação de um banco de dados Oracle altamente disponível no Azure. Além disso, este guia mergulha em considerações de recuperação de desastres. Essas arquiteturas foram criadas com base em implantações de clientes. Este guia só se aplica ao Oracle Database Enterprise Edition.

Se você estiver interessado em aprender mais sobre como maximizar o desempenho do seu banco de dados Oracle, consulte [Arquiteta um DB Oracle](oracle-design.md).

## <a name="assumptions"></a>Suposições

- Você tem uma compreensão dos diferentes conceitos do Azure, como zonas de [disponibilidade](../../../availability-zones/az-overview.md)
- Você está executando oracle database Enterprise Edition 12c ou mais tarde
- Você está ciente e reconhece as implicações do licenciamento ao usar as soluções neste artigo

## <a name="high-availability-for-oracle-databases"></a>Alta disponibilidade para bancos de dados Oracle

Alcançar alta disponibilidade na nuvem é uma parte importante do planejamento e design de cada organização. O Microsoft Azure oferece [zonas de disponibilidade](../../../availability-zones/az-overview.md) e conjuntos de disponibilidade (para serem usados em regiões onde as zonas de disponibilidade não estão disponíveis). Leia mais sobre [como gerenciar a disponibilidade de suas máquinas virtuais](../../../virtual-machines/linux/manage-availability.md) para projetar para a nuvem.

Além de ferramentas e ofertas nativas da nuvem, a Oracle fornece soluções para alta disponibilidade, como [Oracle Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7) [Data Guard com FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)e [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) que podem ser configuradas no Azure. Este guia abrange arquiteturas de referência para cada uma dessas soluções.

Finalmente, ao migrar ou criar aplicativos para a nuvem, é importante ajustar o código do aplicativo para adicionar padrões nativos da nuvem, como [padrão de repetição](https://docs.microsoft.com/azure/architecture/patterns/retry) e [padrão de disjuntor.](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) Padrões adicionais definidos no [guia Padrões de Design](https://docs.microsoft.com/azure/architecture/patterns/) em Nuvem podem ajudar seu aplicativo a ser mais resistente.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC na nuvem

O Oracle Real Application Cluster (RAC) é uma solução da Oracle para ajudar os clientes a obter altos throughputs, tendo muitas instâncias acessando um armazenamento de banco de dados (padrão de arquitetura compartilhada em todos os). Embora o Oracle RAC também possa ser usado para alta disponibilidade no local, o Oracle RAC sozinho não pode ser usado para alta disponibilidade na nuvem, pois ele só protege contra falhas de nível de ocorrência e não contra falhas de nível de rack ou data center. Por essa razão, a Oracle recomenda o uso do Oracle Data Guard com seu banco de dados (seja instância única ou RAC) para alta disponibilidade. Os clientes geralmente exigem um SLA alto para executar seus aplicativos de missão crítica. Atualmente, a Oracle RAC não é certificada ou suportada pela Oracle no Azure. No entanto, o Azure oferece recursos como o Azure oferece Zonas de disponibilidade e janelas de manutenção planejadas para ajudar a proteger contra falhas no nível de instância. Além disso, os clientes podem usar tecnologias como Oracle Data Guard, Oracle GoldenGate e Oracle Sharding para alto desempenho e resiliência, protegendo seus bancos de dados contra falhas no nível de rack, bem como falhas geopolíticas e de nível de datacenter.

Ao executar bancos de dados Oracle em várias zonas de [disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview) em conjunto com o Oracle Data Guard ou o GoldenGate, os clientes podem obter um SLA de 99,99%. Nas regiões do Azure onde as zonas de disponibilidade ainda não estão presentes, os clientes podem usar [conjuntos](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) de disponibilidade e obter um SLA de 99,95%.

>NOTA: Você pode ter um alvo de tempo de atividade muito maior do que o SLA de tempo de atividade fornecido pela Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Recuperação de desastres para bancos de dados Oracle

Ao hospedar seus aplicativos de missão crítica na nuvem, é importante projetar para alta disponibilidade e recuperação de desastres.

Para o Oracle Database Enterprise Edition, o Oracle Data Guard é um recurso útil para a recuperação de desastres. Você pode configurar uma instância de banco de dados em standby em uma [região azure emparelhada](/azure/best-practices-availability-paired-regions) e configurar failover do Data Guard para recuperação de desastres. Para perda zero de dados, é recomendável que você implante uma instância Oracle Data Guard Far Sync, além do Active Data Guard. 

Considere configurar a instância Do Guard Far Sync em uma zona de disponibilidade diferente do banco de dados principal oracle se o aplicativo permitir a latência (testes completos são necessários). Use um modo **de disponibilidade máxima** para configurar o transporte síncrono de seus arquivos redo para a instância Far Sync. Esses arquivos são então transferidos assíncronamente para o banco de dados de espera. 

Se o aplicativo não permitir a perda de desempenho ao configurar a instância Far Sync em outra zona de disponibilidade no modo **disponibilidade máxima** (síncrono), você pode configurar uma instância far sync na mesma zona de disponibilidade do seu banco de dados principal. Para obter disponibilidade adicional, considere configurar várias instâncias do Far Sync perto do seu banco de dados principal e pelo menos uma instância próxima ao seu banco de dados de espera (se a função fizer transições). Leia mais sobre o Oracle Data Guard Far Sync neste [whitepaper Oracle Active Data Guard Far Sync](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Ao usar os bancos de dados Oracle Standard Edition, existem soluções ISV como o DBVisit Standby que permitem configurar alta disponibilidade e recuperação de desastres.

## <a name="reference-architectures"></a>Arquiteturas de referência

### <a name="oracle-data-guard"></a>Oracle Data Guard

O Oracle Data Guard garante alta disponibilidade, proteção de dados e recuperação de desastres para dados corporativos. O Data Guard mantém bancos de dados de espera como cópias transacicamente consistentes do banco de dados principal. Dependendo da distância entre os bancos de dados primário e secundário e a tolerância do aplicativo para latência, você pode configurar a replicação síncrona ou assíncrona. Então, se o banco de dados principal estiver indisponível por causa de uma paralisação planejada ou não planejada, o Data Guard pode mudar qualquer banco de dados de espera para a função principal, minimizando o tempo de inatividade. 

Ao usar o Oracle Data Guard, você também pode abrir seu banco de dados secundário para fins somente de leitura. Essa configuração é chamada active Data Guard. O Oracle Database 12c introduziu um recurso chamado Data Guard Far Sync Instance. Esta instância permite configurar uma configuração de perda zero de dados do seu banco de dados Oracle sem ter que comprometer o desempenho.

> [!NOTE]
> O Active Data Guard requer licenciamento adicional. Esta licença também é necessária para usar o recurso Far Sync. Por favor, conecte-se com seu representante da Oracle para discutir as implicações de licenciamento.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard com FSFO
O Oracle Data Guard com O FSFO (Fast-Start Failover) pode fornecer resiliência adicional configurando o corretor em uma máquina separada. O corretor do Data Guard e o banco de dados secundário executam o observador e observam o banco de dados primário para o tempo de inatividade. Isso permite a redundância na configuração do observador do Data Guard também. 

Com o Oracle Database versão 12.2 ou superior, também é possível configurar vários observadores com uma única configuração de corretor oracle Data Guard. Essa configuração oferece disponibilidade adicional, no caso de um observador e o banco de dados secundário experimentar em inatividade o tempo de inatividade. Data Guard Broker é leve e pode ser hospedado em uma máquina virtual relativamente pequena. Para saber mais sobre o Data Guard Broker e suas vantagens, visite a [documentação oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) sobre este tópico.

O diagrama a seguir é uma arquitetura recomendada para usar o Oracle Data Guard no Azure com zonas de disponibilidade. Esta arquitetura permite que você obtenha um SLA de tempo de atividade vm de 99,99%.

![Banco de dados Oracle usando zonas de disponibilidade com Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

No diagrama anterior, o sistema cliente acessa um aplicativo personalizado com back-end Oracle via web. O frontend da web está configurado em um balanceador de carga. O frontend web faz uma chamada para o servidor de aplicativo apropriado para lidar com o trabalho. O servidor de aplicativos consulta o banco de dados Oracle principal. O banco de dados Oracle foi configurado usando uma [máquina virtual otimizada de memória](../../../virtual-machines/windows/sizes-memory.md) hiperthreaded com [vCPUs principais restritos](../../../virtual-machines/windows/constrained-vcpu.md) para economizar em custos de licenciamento e maximizar o desempenho. Vários discos premium ou ultra (Discos Gerenciados) são usados para desempenho e alta disponibilidade.

Os bancos de dados Oracle são colocados em várias zonas de disponibilidade para alta disponibilidade. Cada região é composta por um ou mais data centers equipados com energia independente, resfriamento e rede. Para garantir a resiliência, um mínimo de três zonas separadas são configuradas em todas as regiões habilitadas. A separação física das zonas de disponibilidade dentro de uma região protege os dados contra falhas no data center. Além disso, dois observadores FSFO são configurados em duas zonas de disponibilidade para iniciar e falhar sobre o banco de dados para o secundário quando ocorre uma paralisação. 

Você pode configurar observadores adicionais e/ou bancos de dados de espera em uma zona de disponibilidade diferente (AZ 1, neste caso) do que a região mostrada na arquitetura anterior. Finalmente, os bancos de dados Oracle são monitorados para tempo de atividade e desempenho pelo Oracle Enterprise Manager (OEM). O OEM também permite que você gere vários relatórios de desempenho e uso.

Em regiões onde as zonas de disponibilidade não são suportadas, você pode usar conjuntos de disponibilidade para implantar seu Banco de Dados Oracle de forma altamente disponível. Os conjuntos de disponibilidade permitem que você atinja um tempo de atividade de VM de 99,95%. O diagrama a seguir é uma arquitetura de referência deste uso:

![Banco de dados Oracle usando conjuntos de disponibilidade com Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * O VM Oracle Enterprise Manager não precisa ser colocado em um conjunto de disponibilidade, pois há apenas uma instância de OEM sendo implantado.
> * Os discos ultra não são suportados no momento em uma configuração de conjunto de disponibilidade.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far Sync

O Oracle Data Guard Far Sync fornece zero recurso de proteção contra perda de dados para bancos de dados Oracle. Esse recurso permite que você proteja contra a perda de dados se a máquina de banco de dados falhar. Oracle Data Guard Far Sync precisa ser instalado em uma VM separada. Far Sync é uma instância Oracle leve que só tem um arquivo de controle, arquivo de senha, spfile e logs de espera. Não há arquivos de dados ou arquivos de registro de rego. 

Para proteção contra perda zero de dados, deve haver comunicação síncrona entre seu banco de dados principal e a instância Far Sync. A instância Far Sync recebe refazer da primária de forma síncrona e encaminha-a imediatamente para todos os bancos de dados de espera de forma assíncrona. Essa configuração também reduz a sobrecarga no banco de dados principal, porque ele só precisa enviar o refazer para a instância Far Sync em vez de todos os bancos de dados de espera. Se uma instância do Far Sync falhar, o Data Guard usará automaticamente o transporte assíncrono para o banco de dados secundário a partir do banco de dados principal para manter a proteção de perda de dados próxima a zero. Para maior resiliência, os clientes podem implantar várias instâncias do Far Sync por cada instância de banco de dados (primárias e secundárias).

O diagrama a seguir é uma arquitetura de alta disponibilidade usando o Oracle Data Guard Far Sync:

![Banco de dados Oracle usando zonas de disponibilidade com Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

Na arquitetura anterior, há uma instância Far Sync implantada na mesma zona de disponibilidade da instância do banco de dados para reduzir a latência entre os dois. Nos casos em que o aplicativo é sensível à latência, considere implantar seu banco de dados e instâncias ou instâncias do Far Sync em um [grupo de colocação de proximidade](../../../virtual-machines/linux/proximity-placement-groups.md).

O diagrama a seguir é uma arquitetura que utiliza o Oracle Data Guard FSFO e o Far Sync para alcançar alta disponibilidade e recuperação de desastres:

![Banco de dados Oracle usando zonas de disponibilidade para recuperação de desastres com Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

O GoldenGate permite a troca e manipulação de dados no nível de transação entre múltiplas plataformas heterogêneas em toda a empresa. Ele move transações comprometidas com integridade de transações e despesas mínimas em sua infra-estrutura existente. Sua arquitetura modular oferece flexibilidade para extrair e replicar registros de dados selecionados, alterações transacionais e alterações no DDL (linguagem de definição de dados) em uma variedade de topologias.

O Oracle GoldenGate permite configurar seu banco de dados para alta disponibilidade, fornecendo replicação bidirecional. Isso permite configurar uma configuração **multimestre** ou **ativa.** O diagrama a seguir é uma arquitetura recomendada para a configuração ativa ativa oracle GoldenGate no Azure. Na arquitetura a seguir, o banco de dados Oracle foi configurado usando uma [máquina virtual otimizada de memória](../../../virtual-machines/windows/sizes-memory.md) hiperthreaded com [vCPUs principais restritos](../../../virtual-machines/windows/constrained-vcpu.md) para economizar em custos de licenciamento e maximizar o desempenho. Vários discos premium ou ultra (discos gerenciados) são usados para desempenho e disponibilidade.

![Banco de dados Oracle usando zonas de disponibilidade com Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Uma arquitetura semelhante pode ser configurada usando conjuntos de disponibilidade em regiões onde as zonas de disponibilidade não estão disponíveis no momento.

O Oracle GoldenGate possui processos como Extrato, Bomba e Replicat que ajudam você a replicar os dados de um servidor de banco de dados Oracle para outro. Esses processos permitem configurar uma replicação bidirecional para garantir alta disponibilidade do seu banco de dados se houver tempo de inatividade em nível de zona de disponibilidade. No diagrama anterior, o processo Extrato é executado no mesmo servidor que seu banco de dados Oracle, enquanto os processos Bomba de Dados e Replicat são executados em um servidor separado na mesma zona de disponibilidade. O processo Replicat é usado para receber dados do banco de dados na outra zona de disponibilidade e comprometer os dados para o banco de dados Oracle em sua zona de disponibilidade. Da mesma forma, o processo Bomba de Dados envia dados extraídos pelo processo Extrato para o processo Replicat na outra zona de disponibilidade. 

Embora o diagrama de arquitetura anterior mostre o processo Bomba de Dados e Replicat configurado em um servidor separado, você pode configurar todos os processos Oracle GoldenGate no mesmo servidor, com base na capacidade e uso do seu servidor. Consulte sempre seu relatório AWR e as métricas no Azure para entender o padrão de uso do seu servidor.

Ao configurar a replicação bidirecional Oracle GoldenGate em diferentes zonas de disponibilidade ou diferentes regiões, é importante garantir que a latência entre os diferentes componentes seja aceitável para o seu aplicativo. A latência entre zonas de disponibilidade e regiões pode variar e depende de múltiplos fatores. Recomenda-se que você configure testes de desempenho entre o nível do aplicativo e o nível do banco de dados em diferentes zonas e/ou regiões de disponibilidade para confirmar que ele atende aos requisitos de desempenho do aplicativo.

O nível de aplicativo pode ser configurado em sua própria sub-rede e o nível de banco de dados pode ser separado em sua própria sub-rede. Quando possível, considere usar [o Azure Application Gateway](../../../application-gateway/overview.md) para equilibrar o tráfego entre os servidores do aplicativo. O Azure Application Gateway é um balanceador robusto de carga de tráfego web. Ele fornece afinidade de sessão baseada em cookies que mantém uma sessão de usuário no mesmo servidor, minimizando assim os conflitos no banco de dados. As alternativas ao Gateway de aplicativos são [o Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) e [o Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Sharding Oráculo

Sharding é um padrão de nível de dados que foi introduzido no Oracle 12.2. Ele permite que você particione horizontalmente e dimensione seus dados em bancos de dados independentes. É uma arquitetura sem compartilhar nada onde cada banco de dados está hospedado em uma máquina virtual dedicada, que permite alta leitura e throughput de gravação, além de resiliência e maior disponibilidade. Esse padrão elimina pontos únicos de falha, fornece isolamento de falhas e permite upgrades de rolagem sem tempo de inatividade. O tempo de inatividade de um fragmento ou uma falha no nível do data center não afeta o desempenho ou a disponibilidade dos outros fragmentos em outros data centers. 

O sharding é adequado para aplicativos OLTP de alto nível que não podem pagar qualquer tempo de inatividade. Todas as linhas com a mesma chave de fragmentação são sempre garantidas por estarem no mesmo fragmento, aumentando assim o desempenho proporcionando a alta consistência. Os aplicativos que usam fragmentação devem ter um modelo de dados bem definido e uma estratégia de distribuição de dados (hash, intervalo, lista ou composto consistentes) que acessa principalmente dados usando uma chave de fragmentação (por exemplo, *customerId* ou *accountNum*). O Sharding também permite que você armazene determinados conjuntos de dados mais próximos dos clientes finais, ajudando assim a atender aos seus requisitos de desempenho e conformidade.

Recomenda-se que você replique seus fragmentos para alta disponibilidade e recuperação de desastres. Essa configuração pode ser feita usando tecnologias Oracle, como Oracle Data Guard ou Oracle GoldenGate. Uma unidade de replicação pode ser um fragmento, uma parte de um fragmento, ou um grupo de fragmentos. A disponibilidade de um banco de dados com fragmentos não é afetada por uma paralisação ou desaceleração de um ou mais fragmentos. Para alta disponibilidade, os fragmentos de espera podem ser colocados na mesma zona de disponibilidade onde os fragmentos primários são colocados. Para recuperação de desastres, os fragmentos de espera podem ser localizados em outra região. Você também pode implantar fragmentos em várias regiões para atender o tráfego nessas regiões. Leia mais sobre a configuração de alta disponibilidade e replicação do seu banco de dados fragmentado na [documentação oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle Sharding consiste principalmente nos seguintes componentes. Mais informações sobre esses componentes podem ser encontradas na [documentação oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Catálogo de fragmentos** - Banco de dados Oracle de uso especial que é uma loja persistente para todos os dados de configuração do banco de dados Shard. Todas as alterações de configuração, como adição ou remoção de fragmentos, mapeamento dos dados e DDLs em um banco de dados de fragmentos são iniciadas no catálogo de fragmentos. O catálogo de fragmentos também contém a cópia mestre de todas as tabelas duplicadas em um SDB. 

  O catálogo de fragmentos usa visualizações materializadas para replicar automaticamente alterações em tabelas duplicadas em todos os fragmentos. O banco de dados de catálogo de fragmentos também atua como um coordenador de consulta usado para processar consultas e consultas de vários fragmentos que não especificam uma chave de fragmentação. 
  
  Usar o Oracle Data Guard em conjunto com zonas de disponibilidade ou conjuntos de disponibilidade para a alta disponibilidade do catálogo de fragmentos é uma prática recomendada. A disponibilidade do catálogo de fragmentos não tem impacto na disponibilidade do banco de dados com fragmentos. Um tempo de inatividade no catálogo de fragmentos afeta apenas as operações de manutenção e consultas multisduras durante o breve período que o failover do Data Guard é concluído. As transações on-line continuam a ser roteadas e executadas pelo SDB e não são afetadas por uma paralisação do catálogo.

- **Shard directors** - Serviços leves que precisam ser implantados em cada região/zona de disponibilidade em que seus fragmentos residem. Os Shard Directors são gerentes de serviços globais implantados no contexto do Oracle Sharding. Para alta disponibilidade, recomenda-se que você implante pelo menos um shard director em cada zona de disponibilidade em que seus fragmentos existem. 

  Ao se conectar ao banco de dados inicialmente, as informações de roteamento são configuradas por um diretor de fragmentos e são armazenadas em cache para solicitações subseqüentes, ignorando o diretor de fragmentos. Uma vez que a sessão é estabelecida com um fragmento, todas as consultas SQL e DMLs são suportadas e executadas no escopo do fragmento dado. Este roteamento é rápido e é usado para todas as cargas de trabalho OLTP que realizam transações intra-fragmentos. Recomenda-se usar roteamento direto para todas as cargas de trabalho OLTP que requerem o mais alto desempenho e disponibilidade. O cache de roteamento é atualizado automaticamente quando um fragmento fica indisponível ou ocorrem alterações na topologia de fragmentação. 
  
  Para roteamento de alto desempenho e dependente de dados, a Oracle recomenda o uso de um pool de conexão ao acessar dados no banco de dados com fragmentos. Pools de conexão Oracle, bibliotecas específicas de idiomas e drivers suportam o Oracle Sharding. Consulte a [documentação da Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) para obter mais detalhes.

- **Serviço global** - O serviço global é semelhante ao serviço regular de banco de dados. Além de todas as propriedades de um serviço de banco de dados, um serviço global tem propriedades para bancos de dados fragmentos, como afinidade de região entre clientes e tolerância a defasagem de fragmentos e replicação. Apenas um serviço Global precisa ser criado para ler/gravar dados para/a partir de um banco de dados com fragmentos. Ao usar o Active Data Guard e configurar réplicas somente leitura dos fragmentos, você pode criar outro serviço gGobal para cargas de trabalho somente leitura. O cliente pode usar esses serviços Globais para se conectar ao banco de dados.

- **Bancos de dados fragmentos** - Bancos de dados fragmentos são seus bancos de dados Oracle. Cada banco de dados é replicado usando o Oracle Data Guard em uma configuração broker com FSFO (Fast-Start Failover, failover de partida rápida). Você não precisa configurar failover e replicação do Data Guard em cada fragmento. Isso é configurado e implantado automaticamente quando o banco de dados compartilhado é criado. Se um fragmento em particular falhar, o Oracle Sharing falha automaticamente sobre as conexões do banco de dados da principal para a espera.

Você pode implantar e gerenciar bancos de dados com duas interfaces: Oracle `GDSCTL` Enterprise Manager Cloud Control GUI e/ou o utilitário de linha de comando. Você pode até monitorar os diferentes fragmentos para disponibilidade e desempenho usando o controle cloud. O `GDSCTL DEPLOY` comando cria automaticamente os fragmentos e seus respectivos ouvintes. Além disso, este comando implanta automaticamente a configuração de replicação usada para a alta disponibilidade de nível de fragmento especificada pelo administrador.

Existem diferentes maneiras de fragmento shard um banco de dados:

* Fragmentação gerenciada pelo sistema - Distribui automaticamente através de fragmentos usando particionamento
* Fragmentação definida pelo usuário - Permite especificar o mapeamento dos dados para os fragmentos, que funciona bem quando há requisitos regulatórios ou de localização de dados)
* Fragmento composto - Uma combinação de fragmentação gerenciada pelo sistema e definida pelo usuário para _diferentes espaços de fragmentos_
* Subpartições de tabela - Semelhante a uma tabela particionada regular.

Leia mais sobre os [diferentes métodos de fragmentação](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) na documentação da Oracle.

Embora um banco de dados com fragmentos possa parecer um único banco de dados para aplicativos e desenvolvedores, ao migrar de um banco de dados não-fragmento para um banco de dados com fragmentos, é necessário um planejamento cuidadoso para determinar quais tabelas serão duplicadas versus fragmentos. 

As tabelas duplicadas são armazenadas em todos os fragmentos, enquanto as mesas com fragmentos são distribuídas em diferentes fragmentos. A recomendação é duplicar tabelas pequenas e dimensionais e distribuir/fragmentos das tabelas de fatos. Os dados podem ser carregados em seu banco de dados com fragmentos usando o catálogo de fragmentos como coordenador central ou executando a Bomba de Dados em cada fragmento. Leia mais sobre [a migração de dados para um banco de dados com fragmentos](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) na documentação da Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oráculo sharding com data guard

O Oracle Data Guard pode ser usado para fragmentação com métodos de fragmentação gerenciados pelo sistema, definidos pelo usuário e compostos.

O diagrama a seguir é uma arquitetura de referência para Oracle Sharding com Oracle Data Guard usada para alta disponibilidade de cada fragmento. O diagrama de arquitetura mostra um _método de fragmentação composto_. O diagrama de arquitetura provavelmente difere para aplicativos com diferentes requisitos para localização de dados, balanceamento de carga, alta disponibilidade, recuperação de desastres, etc. e pode usar método diferente para fragmentação. O Oracle Sharding permite que você atenda a esses requisitos e dimensione horizontal e eficientemente, fornecendo essas opções. Uma arquitetura semelhante pode até ser implantada usando o Oracle GoldenGate.

![Oráculo Banco de Dados Sharding usando zonas de disponibilidade com Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Embora o fragmento gerenciado pelo sistema seja o mais fácil de configurar e gerenciar, o fragmento ou o fragmento composto definidos pelo usuário são adequados para cenários onde seus dados e aplicativos são geodistribuídos ou em cenários onde você precisa ter controle sobre a replicação de cada fragmento. 

Na arquitetura anterior, o fragmento composto é usado para distribuir geograficamente os dados e dimensionar horizontalmente os níveis de aplicativos. O fragmento composto é uma combinação de fragmentação gerenciada pelo sistema e definida pelo usuário e, portanto, fornece o benefício de ambos os métodos. No cenário anterior, os dados são primeiro rescacidos em vários espaços separados por região. Em seguida, os dados são ainda mais particionados por hash consistente através de vários fragmentos no espaço de fragmentos. Cada espaço contém vários grupos de fragmentos. Cada grupo de fragmentos tem vários fragmentos e é uma "unidade" de replicação, neste caso. Cada grupo de fragmentos contém todos os dados no espaço de fragmentos. Os grupos A1 e B1 são grupos de fragmentos primários, enquanto os grupos de fragmentos A2 e B2 são de prontidão. Você pode optar por ter fragmentos individuais como a unidade de replicação, em vez de um grupo de fragmentos.

Na arquitetura anterior, um gsm/shard director é implantado em todas as zonas de disponibilidade para alta disponibilidade. A recomendação é implantar pelo menos um GSM/shard director por data center/região. Além disso, uma instância do servidor de aplicativos é implantada em todas as zonas de disponibilidade que contenham um grupo de fragmentos. Essa configuração permite que o aplicativo mantenha a latência entre o servidor de aplicativos e o banco de dados/shardgroup baixo. Se um banco de dados falhar, o servidor de aplicativos na mesma região que o banco de dados de espera pode lidar com solicitações assim que a transição de função de banco de dados acontecer. O Azure Application Gateway e o shard director acompanham as solicitações de solicitação e de latência e rota de resposta em conformidade.

Do ponto de vista do aplicativo, o sistema cliente faz uma solicitação ao Azure Application Gateway (ou outras tecnologias de balanceamento de carga no Azure) que redireciona a solicitação para a região mais próxima do cliente. O Azure Application Gateway também suporta sessões pegajosas, de modo que quaisquer solicitações vindas do mesmo cliente são roteadas para o mesmo servidor de aplicativos. O servidor de aplicativos usa pool de conexão em drivers de acesso a dados. Este recurso está disponível em drivers como JDBC, ODP.NET, OCI, etc. Os drivers podem reconhecer as chaves de fragmentação especificadas como parte da solicitação. [O Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) para clientes JDBC pode permitir que clientes de aplicativos não-Oracle, como Apache Tomcat e IIS, trabalhem com o Oracle Sharding. 

Durante a solicitação inicial, o servidor de aplicativo se conecta ao shard director em sua região para obter informações de roteamento para o fragmento para o qual a solicitação precisa ser roteada. Com base na chave de fragmentação passada, o diretor encaminha o servidor de aplicativos para o respectivo fragmento. O servidor de aplicativos armazena essas informações construindo um mapa e, para solicitações subseqüentes, ignora o diretor de fragmentos e encaminha solicitações diretamente para o fragmento.

#### <a name="oracle-sharding-with-goldengate"></a>Oráculo Sharding com GoldenGate

O diagrama a seguir é uma arquitetura de referência para Oracle Sharding com Oracle GoldenGate para alta disponibilidade na região de cada fragmento. Ao contrário da arquitetura anterior, essa arquitetura apenas retrata alta disponibilidade dentro de uma única região Azure (várias zonas de disponibilidade). Pode-se implantar um banco de dados com alta disponibilidade de várias regiões (semelhante ao exemplo anterior) usando o Oracle GoldenGate.

![Oráculo Database Sharding usando zonas de disponibilidade com goldengate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

A arquitetura de referência anterior usa o método de fragmentação _gerenciado pelo sistema_ para fragmentar os dados. Como a replicação do Oracle GoldenGate é feita em um nível de pedaço, metade dos dados replicados em um fragmento pode ser replicado para outro fragmento. A outra metade pode ser replicada para um fragmento diferente. 

A forma como os dados são replicados depende do fator de replicação. Com um fator de replicação de 2, você terá duas cópias de cada pedaço de dados em seus três fragmentos no grupo de fragmentos. Da mesma forma, com um fator de replicação de 3 e três fragmentos em seu grupo de fragmentos, todos os dados em cada fragmento serão replicados em todos os outros fragmentos do grupo de fragmentos. Cada fragmento no grupo de fragmentos pode ter um fator de replicação diferente. Essa configuração ajuda você a definir seu projeto de alta disponibilidade e recuperação de desastres de forma eficiente dentro de um grupo de fragmentos e em vários grupos de fragmentos.

Na arquitetura anterior, o grupo A e o grupo B contêm os mesmos dados, mas residem em diferentes zonas de disponibilidade. Se ambos os grupos A e fragmentos B tiverem o mesmo fator de replicação de 3, cada linha/pedaço da sua tabela fragmento será replicado 6 vezes entre os dois grupos de fragmentos. Se o grupo A tiver um fator de replicação de 3 e o grupo B tiver um fator de replicação de 2, cada linha/pedaço será replicado 5 vezes entre os dois grupos de fragmentos.

Essa configuração evita a perda de dados se ocorrer uma falha no nível de nível de instância ou na zona de disponibilidade. A camada do aplicativo é capaz de ler e escrever para cada fragmento. Para minimizar conflitos, o Oracle Sharding designa um "pedaço mestre" para cada faixa de valores de hash. Este recurso garante que as solicitações de gravações para um determinado pedaço sejam direcionadas para o pedaço correspondente. Além disso, o Oracle GoldenGate fornece detecção e resolução automática de conflitos para lidar com quaisquer conflitos que possam surgir. Para obter mais informações e limitações para implementar o GoldenGate com o Oracle Sharding, consulte a documentação da Oracle sobre o uso do [Oracle GoldenGate com um banco de dados fragmentado](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

Na arquitetura anterior, um gsm/shard director é implantado em todas as zonas de disponibilidade para alta disponibilidade. A recomendação é implantar pelo menos um GSM/shard director por data center ou região. Além disso, uma instância do servidor de aplicativos é implantada em todas as zonas de disponibilidade que contenham um grupo de fragmentos. Essa configuração permite que o aplicativo mantenha a latência entre o servidor de aplicativos e o banco de dados/shardgroup baixo. Se um banco de dados falhar, o servidor de aplicativos na mesma região que o banco de dados de espera pode lidar com solicitações assim que a função de banco de dados fizer a transição. O Azure Application Gateway e o shard director acompanham as solicitações de solicitação e de latência e rota de resposta em conformidade.

Do ponto de vista do aplicativo, o sistema cliente faz uma solicitação ao Azure Application Gateway (ou outras tecnologias de balanceamento de carga no Azure) que redireciona a solicitação para a região mais próxima do cliente. O Azure Application Gateway também suporta sessões pegajosas, de modo que quaisquer solicitações vindas do mesmo cliente são roteadas para o mesmo servidor de aplicativos. O servidor de aplicativos usa pool de conexão em drivers de acesso a dados. Este recurso está disponível em drivers como JDBC, ODP.NET, OCI, etc. Os drivers podem reconhecer as chaves de fragmentação especificadas como parte da solicitação. [O Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) para clientes JDBC pode permitir que clientes de aplicativos não-Oracle, como Apache Tomcat e IIS, trabalhem com o Oracle Sharding. 

Durante a solicitação inicial, o servidor de aplicativo se conecta ao shard director em sua região para obter informações de roteamento para o fragmento para o qual a solicitação precisa ser roteada. Com base na chave de fragmentação passada, o diretor encaminha o servidor de aplicativos para o respectivo fragmento. O servidor de aplicativos armazena essas informações construindo um mapa e, para solicitações subseqüentes, ignora o diretor de fragmentos e encaminha solicitações diretamente para o fragmento.

## <a name="patching-and-maintenance"></a>Patches e manutenção

Ao implantar suas cargas de trabalho Oracle no Azure, a Microsoft cuida de todos os patches de nível de sistema operacional do host. Qualquer manutenção planejada no nível do SO é comunicada aos clientes com antecedência para permitir que o cliente para esta manutenção planejada. Dois servidores de duas zonas de disponibilidade diferentes nunca são corrigidos simultaneamente. Consulte [Gerenciar a disponibilidade de máquinas virtuais](../../../virtual-machines/linux/manage-availability.md) para obter mais detalhes sobre manutenção e patches de VM. 

O patch do sistema operacional da máquina virtual pode ser automatizado usando [o Azure Automation](../../../automation/automation-tutorial-update-management.md). Patches e manutenção do banco de dados Oracle podem ser automatizados e agendados usando [o Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) ou [o Azure Automation](../../../automation/automation-tutorial-update-management.md) para minimizar o tempo de inatividade. Consulte [A Entrega Contínua e implantações azul/verde](/azure/devops/learn/what-is-continuous-delivery) para entender como ela pode ser usada no contexto de seus bancos de dados Oracle.

## <a name="architecture-and-design-considerations"></a>Considerações de arquitetura e design

- Considere usar [uma máquina virtual otimizada de memória](../../../virtual-machines/windows/sizes-memory.md) hiperthreaded com [vCPUs principais restritos](../../../virtual-machines/windows/constrained-vcpu.md) para sua VM de banco de dados Oracle para economizar em custos de licenciamento e maximizar o desempenho. Use vários discos premium ou ultra (discos gerenciados) para desempenho e disponibilidade.
- Ao usar discos gerenciados, o nome do disco/dispositivo pode ser alterado nas reinicializações. Recomenda-se que você use o dispositivo UUID em vez do nome para garantir que suas montagens persistam entre as reinicializações. Encontre mais informações [aqui](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab).
- Use zonas de disponibilidade para obter alta disponibilidade na região.
- Considere usar discos ultra (quando disponíveis) ou discos premium para o banco de dados Oracle.
- Considere a criação de um banco de dados Oracle em standby em outra região do Azure usando o Oracle Data Guard.
- Considere usar [grupos de colocação de proximidade](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) para reduzir a latência entre seu aplicativo e o nível de banco de dados.
- Configure o [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) para gerenciamento, monitoramento e registro.
- Considere usar o ASM (Oracle Automatic Storage Management, gerenciamento automático de armazenamento) para um gerenciamento simplificado de armazenamento para o seu banco de dados.
- Use [o Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) para gerenciar patches e atualizações em seu banco de dados sem qualquer tempo de inatividade.
- Ajuste o código do aplicativo para adicionar padrões nativos da nuvem, como padrão de [repetição,](/azure/architecture/patterns/retry) [padrão de disjuntor](/azure/architecture/patterns/circuit-breaker)e outros padrões definidos no [guia Padrões de Design](/azure/architecture/patterns/) em Nuvem que podem ajudar seu aplicativo a ser mais resistente.

## <a name="next-steps"></a>Próximas etapas

Revise os seguintes artigos de referência oracle que se aplicam ao seu cenário.

- [Introdução ao Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Conceitos do Corretor de Data Guard oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Configuração do Oracle GoldenGate para alta disponibilidade ativa](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Visão geral do Oráculo Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard Far Sync Zero Data Loss a qualquer distância](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
