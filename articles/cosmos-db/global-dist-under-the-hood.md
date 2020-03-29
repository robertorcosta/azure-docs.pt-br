---
title: Distribuição global com o Azure Cosmos DB – nos bastidores
description: Este artigo apresenta detalhes técnicos relacionados à distribuição global do Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a46a69476a2ad6550bc7b3a533fd09565d461db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872121"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuição de dados global com o Azure Cosmos DB – nos bastidores

O Azure Cosmos DB é um serviço fundamental no Azure, por isso é implantado em todas as regiões do Azure em todo o mundo, incluindo o público, soberano, Departamento de Defesa (DoD) e nuvens do governo. Em um data center, podemos implantar o Azure Cosmos DB em grandes carimbos de computadores, cada um com armazenamento local dedicado. Em um data center, o Azure Cosmos DB é implantado em vários clusters, cada um executando potencialmente várias gerações de hardware. As máquinas dentro de um cluster são normalmente espalhadas em 10-20 domínios de falha para alta disponibilidade dentro de uma região. A imagem a seguir mostra a topologia do sistema de distribuição global do Cosmos DB:

![Topologia do sistema](./media/global-dist-under-the-hood/distributed-system-topology.png)

**A distribuição global no Azure Cosmos DB é chave de tempo:** A qualquer momento, com alguns cliques ou programáticamente com uma única chamada de API, você pode adicionar ou remover as regiões geográficas associadas ao seu banco de dados Cosmos. Um banco de dados cosmos, por sua vez, consiste em um conjunto de contêineres Cosmos. No Cosmos DB, os contêineres servem como unidades lógicas de distribuição e escalabilidade. As coleções, as tabelas e os gráficos que você cria são (internamente) apenas contêineres do Cosmos. Os recipientes são completamente agnósticos e fornecem um escopo para uma consulta. Os dados em um contêiner do Cosmos são indexados automaticamente após a ingestão. A indexação automática permite que os usuários consultarem os dados sem os problemas de esquema ou gerenciamento de índices, especialmente em uma configuração distribuída globalmente.  

- Em uma determinada região, os dados dentro de um contêiner são distribuídos usando uma chave de partição, que você fornece e é gerenciada de forma transparente pelas partições físicas subjacentes *(distribuição local).*  

- Cada partição física também é replicada em regiões geográficas *(distribuição global).* 

Quando um aplicativo que usa o Cosmos DB escala elasticamente o throughput em um contêiner Cosmos ou consome mais armazenamento, o Cosmos DB lida de forma transparente com as operações de gerenciamento de partições (split, clone, delete) em todas as regiões. Independentemente da escala, da distribuição ou de falhas, o Cosmos DB continua a fornecer uma única imagem do sistema dos dados dentro de contêineres, que são distribuídos globalmente em qualquer número de regiões.  

Como mostrado na imagem a seguir, os dados dentro de um contêiner são distribuídos ao longo de duas dimensões - dentro de uma região e em todas as regiões, em todo o mundo:  

![partições físicas](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Uma partição física é implementada por um grupo de réplicas, chamada *de conjunto de réplicas*. Cada máquina hospeda centenas de réplicas que correspondem a várias partições físicas dentro de um conjunto fixo de processos, como mostrado na imagem acima. As réplicas correspondentes às partições físicas são colocadas dinamicamente e com balanceamento de carga entre os computadores dentro de um cluster e os data centers dentro de uma região.  

Uma réplica pertence exclusivamente a um locatário do Azure Cosmos DB. Cada réplica hospeda uma instância do [Mecanismo de Banco de Dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) do Cosmos DB, que gerencia os recursos, bem como os índices associados. O mecanismo de banco de dados Cosmos opera em um sistema de tipo ars baseado em seqüência de registro de átomos (ARS). O motor é agnóstico ao conceito de um esquema, borrando o limite entre a estrutura e os valores de ocorrência dos registros. O Cosmos DB obtém total independência de esquema indexando tudo automaticamente no momento da ingestão de maneira eficiente, o que permite aos usuários consultar seus dados distribuídos globalmente sem precisarem lidar com gerenciamento de esquema ou índice.

O mecanismo de banco de dados Cosmos consiste em componentes, incluindo a implementação de vários primitivos de coordenação, tempos de execução de linguagem, o processador de consulta e os subsistemas de armazenamento e indexação responsáveis pelo armazenamento transacional e indexação de dados, Respectivamente. Para fornecer durabilidade e alta disponibilidade, o Mecanismo de Banco de Dados mantém os dados e o índice em SSDs e replica-os entre as instâncias do Mecanismo de Banco de Dados dentro dos conjuntos de réplica, respectivamente. Inquilinos maiores correspondem a uma escala maior de throughput e armazenamento e têm ou maiores ou mais réplicas ou ambas. Cada componente do sistema é completamente assíncrono: nenhum thread jamais bloqueia e cada thread faz trabalho de curta duração sem incorrer em nenhuma alternância de thread desnecessária. A limitação de taxa e a contrapressão são inseridas em toda a pilha do controle de admissão para todos os caminhos de E/S. O motor de banco de dados cosmos foi projetado para explorar a concorrência de grãos finos e para fornecer alto throughput enquanto opera dentro de quantidades frugal de recursos do sistema.

A distribuição global do Cosmos DB conta com duas abstrações-chave – *conjuntos de réplicas* e *conjuntos de partição*. Um conjunto de réplicas é um bloco de Lego modular para coordenação, enquanto que um conjunto de partições é uma sobreposição dinâmica de uma ou mais partições físicas distribuídas geograficamente. Para entender como a distribuição global funciona, precisamos entender essas duas abstrações principais. 

## <a name="replica-sets"></a>Conjuntos de réplicas

Uma partição física é materializada como um grupo autogerenciado e com balanceamento de carga dinâmico de réplicas distribuídas entre vários domínios de falha, chamados de conjunto de réplicas. Esse conjunto implementa coletivamente o protocolo de computador de estado replicado para tornar os dados na partição física altamente disponíveis, duráveis e consistentes. A associação de conjunto de réplicas *N* é dinâmica – ela mantém a flutuação entre *o NMin* e o *NMax* com base nas falhas, nas operações administrativas e no tempo para as réplicas com falha se regenerarem/recuperarem. Com base em alterações de associação, a replicação de protocolo também reconfigura o tamanho de leitura e os quóruns de gravação. Para distribuir uniformemente o throughput atribuído a uma determinada partição física, empregamos duas ideias: 

- Primeiro, o custo de processamento das solicitações de gravação no líder é maior do que o custo de aplicar as atualizações no seguidor. Do mesmo modo, o líder tem mais recursos de sistema orçados que os seguidores. 

- Em segundo lugar, sempre que possível, o quórum de leitura para um nível de coerência específico é composto exclusivamente pelas réplicas de seguidores. Evitamos entrar em contato com o líder para atender às leituras, a menos que necessário. Empregamos uma série de ideias a partir da pesquisa feita sobre a relação de [carga e capacidade](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nos sistemas baseados em quórum para os cinco [modelos de consistência](consistency-levels.md) que a Cosmos DB suporta.  

## <a name="partition-sets"></a>Conjuntos de partições

Um grupo de partições físicas, uma de cada uma das regiões de banco de dados do Cosmos, é composto para gerenciar o mesmo conjunto de chaves replicadas em todas as regiões configuradas. Este primitivo de coordenação superior é chamado *de conjunto de partições* - uma sobreposição dinâmica geograficamente distribuída de partições físicas gerenciando um determinado conjunto de chaves. Enquanto uma determinada partição física (uma réplica definida) é escopo dentro de um cluster, um conjunto de partições pode abranger clusters, data centers e regiões geográficas, conforme mostrado na imagem abaixo:  

![Conjuntos de partição](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Você pode pensar em um conjunto de partições como um "superconjunto de réplicas" geograficamente disperso composto por vários conjuntos de réplicas com o mesmo conjunto de chaves. Semelhante a um conjunto de réplicas, a adesão de um conjunto de partições também é dinâmica – ela flutua com base em operações implícitas de gerenciamento de partições físicas para adicionar/remover novas partições para/a partir de um determinado conjunto de partições (por exemplo, quando você dimensiona o throughput em um contêiner, adiciona/remove uma região ao seu banco de dados Cosmos ou quando ocorrem falhas). Em virtude de ter cada uma das partições (de um conjunto de partições) gerenciar a associação definida por partição dentro de seu próprio conjunto de réplicas, a associação é totalmente descentralizada e altamente disponível. Durante a reconfiguração de um conjunto de partições, a topologia da sobreposição entre as partições físicas também é estabelecida. A topologia é selecionada dinamicamente com base no nível de consistência, distância geográfica e largura de banda de rede disponível entre a origem e as partições físicas de destino.  

O serviço permite que você configure seus bancos de dados do Cosmos com uma ou várias regiões de gravação e, dependendo da escolha, conjuntos de partições são configurados para aceitar as gravações em exatamente uma ou em todas as regiões. O sistema usa um protocolo de consenso aninhado de dois níveis – um nível opera dentro das réplicas de um conjunto de réplicas de uma partição física que aceita as gravações e o outro opera no nível de um conjunto de partições para fornecer garantias completas de ordenação para todas as gravações confirmadas no conjunto de partições. Esse consenso aninhado em várias camadas é crítico para a implementação de nossos contratos rigorosos de alta disponibilidade, bem como a implementação dos modelos de coerência, que o Cosmos DB oferece a seus clientes.  

## <a name="conflict-resolution"></a>Resolução de conflitos

Nosso design para propagação da atualização, resolução de conflitos e acompanhamento de causalidade é inspirado no trabalho anterior sobre [algoritmos epidêmicos](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e o sistema [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf). Embora kernels de ideias tenham sobrevivido e apresentem um quadro de referência conveniente para comunicação com o design do sistema do Cosmos DB, eles também foram submetidos a transformações significativas ao serem aplicados ao sistema do Cosmos DB. Isso era necessário, porque os sistemas anteriores não foram projetados nem com a governança de recursos nem com a escala em que o Cosmos DB precisa operar, nem para fornecer as capacidades (por exemplo, consistência de sobismo limitada) e a rigorosa e SLAs abrangentes que a Cosmos DB entrega aos seus clientes.  

Lembre-se de que um conjunto de partições é distribuído em várias regiões e segue o protocolo de replicação do Cosmos DB (vários mestres) para replicar os dados entre as partições físicas que compõem um determinado conjunto de partições. Cada partição física (de um conjunto de partições) aceita gravações e prepara leituras tipicamente para os clientes que são locais dessa região. As gravações aceitas por uma partição física em uma região são confirmadas permanentemente e altamente disponibilizadas na partição física, antes de serem reconhecidas para o cliente. São gravações provisórias e propagadas para outras partições físicas dentro do conjunto de partições usando um canal antientropia. Os clientes podem solicitar gravações provisórias ou confirmadas passando um cabeçalho de solicitação. A propagação antientropia (incluindo a frequência da propagação) é dinâmica, baseada na topologia do conjunto de partições, na proximidade regional entre as partições físicas e no nível de coerência configurado. Em um conjunto de partições, o Cosmos DB segue um esquema de confirmação primário com uma partição de arbitrador selecionada dinamicamente. A seleção do arbitrador é dinâmica e é parte integrante da reconfiguração do conjunto de partições com base na topologia da sobreposição. As gravações confirmadas (incluindo atualizações multilinha/em lote) têm garantia de serem pedidas. 

Podemos empregar os relógios de vetor codificados (contendo a ID da região e relógios lógicos correspondendo a cada nível de consenso no conjunto de réplicas e no conjunto de partições, respectivamente) para vetores de versão e acompanhamento de causalidade detectarem e resolverem conflitos de atualização. A topologia e o algoritmo de seleção de par são projetados para garantir os armazenamentos fixo e mínimo e a sobrecarga mínima de rede dos vetores de versão. O algoritmo garante a propriedade de convergência estrita.  

Para os bancos de dados do Cosmos configurados com várias regiões de gravação, o sistema oferece diversas políticas de resolução automática de conflitos para os desenvolvedores escolherem, incluindo: 

- **Last-Write-Wins (LWW)**, que, por padrão, usa uma propriedade de carimbo de tempo definida pelo sistema (que é baseada no protocolo de relógio de sincronização de tempo). O Cosmos DB também permite que você especifique qualquer outra propriedade numérica personalizada a ser usada para resolução de conflitos.  
- **Política de resolução de conflitos definida por aplicativo (Personalizado)** (expressa através de procedimentos de fusão), que é projetada para a reconciliação semântica definida pelo aplicativo de conflitos. Esses procedimentos são invocados após a detecção de conflitos de entre gravações sob responsabilidade de uma transação de banco de dados no lado do servidor. O sistema fornece exatamente uma garantia para a execução de um procedimento de fusão como parte do protocolo de compromisso. Existem [várias amostras de resolução de conflitos](how-to-manage-conflicts.md) disponíveis para você brincar.  

## <a name="consistency-models"></a>Modelos de coerência

Se você configurar seu banco de dados Cosmos com uma única ou múltipla região de gravação, você pode escolher entre os cinco modelos de consistência bem definidos. Com várias regiões de gravação, os seguintes aspectos são alguns aspectos notáveis dos níveis de consistência:  

A consistência de obsoleto delimitado garante que todas as leituras estarão dentro de prefixos *K* ou *T* segundos a partir da última gravação em qualquer uma das regiões. Além disso, leituras com consistência de obsoleto limitado são garantidas como monótonas e com garantias de prefixo consistentes. O protocolo antientropia opera de maneira limitada por taxa e garante que os prefixos não se acumulam e a contrapressão sobre as gravações não precisem ser aplicadas. A consistência da sessão garante leitura monótona, escrita monótona, leitura de suas próprias gravações, gravação segue leitura e garantias de prefixo consistentes, em todo o mundo. Para as bases de dados configuradas com forte consistência, os benefícios (baixa latência de gravação, alta disponibilidade de gravação) de várias regiões de gravação não se aplicam, devido à replicação síncrona entre regiões.

A semântica dos cinco modelos de consistência no Cosmos DB são descritas [aqui](consistency-levels.md), e matematicamente descritas usando uma especificação TLA+ de alto nível [aqui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Próximas etapas

Em seguida, saiba como configurar a distribuição global usando os seguintes artigos:

* [Adicionar ou remover regiões de sua conta de banco de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Como criar uma política de resolução de conflitos personalizada](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
