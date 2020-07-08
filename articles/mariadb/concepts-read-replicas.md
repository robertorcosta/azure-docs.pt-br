---
title: Ler réplicas-banco de dados do Azure para MariaDB
description: 'Saiba mais sobre réplicas de leitura no banco de dados do Azure para MariaDB: escolhendo regiões, criando réplicas, conectando a réplicas, monitorando a replicação e parando a replicação.'
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: bed89b325ce28ab969bad5ed30802bdb67a21a96
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86076548"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Réplicas de leitura no Banco de Dados do Azure para MariaDB

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para MariaDB para um servidor somente leitura. Você pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição de arquivo de log (binlog) do mecanismo MariaDB com a ID de transação global (GTID). Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog](https://mariadb.com/kb/en/library/replication-overview/).

Réplicas são novos servidores que você gerencia de forma semelhante ao banco de dados do Azure regular para servidores MariaDB. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Para saber mais sobre a replicação do GTID, consulte a [documentação de replicação do MariaDB](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Comunicação sem tendência
>
> A Microsoft dá suporte a um ambiente diversificado e de inclusão. Este artigo contém referências à palavra _subordinada_. O [Guia de estilo da Microsoft para comunicação sem tendência](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isso como uma palavra de exclusão. A palavra é usada neste artigo para fins de consistência porque, atualmente, ela é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como réplicas são somente leitura, elas não reduzem diretamente os encargos de capacidade de gravação no mestre. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso ler réplica usa replicação assíncrona. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica acabarão se tornando consistentes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente da do servidor mestre. A replicação entre regiões pode ser útil para cenários como o planejamento de recuperação de desastres ou para trazer dados mais próximos dos seus usuários.

Você pode ter um servidor mestre em qualquer [banco de dados do Azure para a região MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A figura abaixo mostra quais regiões de réplica estão disponíveis dependendo da sua região mestra.

[ ![Ler regiões de réplica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal
Você pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor mestre estiver localizado. As regiões de réplica universal compatíveis incluem:

Leste da Austrália, sudeste da Austrália, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2, Oeste EUA Central.

### <a name="paired-regions"></a>Regiões emparelhadas
Você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre além das regiões de réplica universal. Se não souber o par da sua região, você pode encontrar essa informação no [artigo Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para o planejamento de recuperação de desastres, recomendamos criar a réplica na região emparelhada, e não em uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam isolamento físico e residência de dados.  

No entanto, há certas limitações a serem consideradas: 

* Disponibilidade regional: o banco de dados do Azure para MariaDB está disponível na França central, Norte dos EAU e na Alemanha central. Entretanto, suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões do Azure são emparelhadas em uma direção apenas. Essas regiões incluem Oeste da Índia, Sul do Brasil e US Gov - Virgínia. 
   Isso significa que um servidor mestre no Oeste da Índia pode criar uma réplica no Sul da Índia. Entretanto, um servidor mestre no Sul da Índia não pode criar uma réplica no Oeste da Índia. Isso ocorre porque a região secundária do Oeste da Índia é o Sul da Índia, mas a região secundária do Sul da Índia não é o Oeste da Índia.

## <a name="create-a-replica"></a>Criar uma réplica

> [!IMPORTANT]
> O recurso ler réplica só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Assegure-se de que o servidor principal esteja em um desses níveis de preços.

Se um servidor mestre não tiver servidores de réplica, o mestre será reiniciado primeiro para se preparar para a replicação.

Quando você inicia o fluxo de trabalho criar réplica, é criado um banco de dados do Azure em branco para o servidor MariaDB. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

> [!NOTE]
> Se você não tem alerta de armazenamento configurado em seus servidores, recomendamos que você faça isso. O alerta informa quando um servidor está se aproximando de seu limite de armazenamento, o que afetará a replicação.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica

Uma réplica herda as regras de firewall do servidor mestre na criação. Posteriormente, essas regras são independentes do servidor mestre.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, como faria em um servidor de banco de dados do Azure normal para MariaDB. Para um servidor chamado **myreplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando a CLI do mysql:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação

O banco de dados do Azure para MariaDB fornece a métrica **atraso de replicação em segundos** no Azure monitor. Essa métrica está disponível apenas para réplicas.

Essa métrica é calculada usando a `seconds_behind_master` métrica disponível no comando do MariaDB `SHOW SLAVE STATUS` .

Defina um alerta para informá-lo quando o retardo de replicação atingir um valor que não é aceitável para sua carga de trabalho.

## <a name="stop-replication"></a>Parar replicação

Você pode interromper a replicação entre um mestre e uma réplica. Após a replicação ser interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não alcança o servidor mestre.

Quando você escolhe interromper a replicação em uma réplica, ela perde todos os links para o mestre anterior e outras réplicas. Não há failover automatizado entre um mestre e sua réplica.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Não há nenhum failover automatizado entre servidores mestre e de réplica. 

Como a replicação é assíncrona, há um atraso entre o mestre e a réplica. A quantidade de latência pode ser influenciada por vários fatores, como a intensidade da carga de trabalho em execução no servidor mestre e a latência entre os data centers. Na maioria dos casos, a latência de réplica varia entre alguns segundos e alguns minutos. Você pode acompanhar o retardo de replicação real usando o *retardo de réplica*de métrica, que está disponível para cada réplica. Essa métrica mostra o tempo desde a última transação reproduzida. É recomendável que você identifique o que é o retardo médio, observando o atraso da réplica em um período de tempo. Você pode definir um alerta na latência de réplica, de modo que, se ele ficar fora do intervalo esperado, você poderá executar uma ação.

> [!Tip]
> Se você realizar o failover para a réplica, o retardo no momento em que você desvincular a réplica do mestre indicará a quantidade de dados perdidos.

Depois que você decidir que deseja fazer failover para uma réplica, 

1. Parar a replicação na réplica<br/>
   Essa etapa é necessária para tornar o servidor de réplica capaz de aceitar gravações. Como parte desse processo, o servidor de réplica será desvinculado do mestre. Depois que você inicia a interrupção da replicação, o processo de back-end normalmente leva cerca de 2 minutos para ser concluído. Consulte a seção [parar replicação](#stop-replication) deste artigo para entender as implicações dessa ação.
    
2. Aponte seu aplicativo para a réplica (antiga)<br/>
   Cada servidor tem uma cadeia de conexão exclusiva. Atualize seu aplicativo para apontar para a réplica (antiga) em vez do mestre.
    
Depois que o aplicativo processar leituras e gravações com êxito, você terá concluído o failover. A quantidade de tempo de inatividade com a qual suas experiências de aplicativo dependerão quando você detectar um problema e concluir as etapas 1 e 2 acima.

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Tipos de preço

No momento, as réplicas de leitura estão disponíveis apenas nas camadas de preços de uso geral e de otimização de memória.

> [!NOTE]
> O custo da execução do servidor de réplica é baseado na região em que o servidor de réplica está sendo executado.

### <a name="master-server-restart"></a>Reinicialização do servidor mestre

Ao criar uma réplica para um mestre que não tem nenhuma réplica existente, primeiro o mestre será reiniciado para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como um novo banco de dados do Azure para o servidor MariaDB. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica

Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento, período de retenção de backup e versão do mecanismo MariaDB. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

Regras de firewall e configurações de parâmetros são herdadas do servidor mestre para a réplica quando a réplica é criada. As regras da réplica são independentes posteriormente.

### <a name="stopped-replicas"></a>Réplicas paradas

Se você interromper a replicação entre um servidor mestre e uma réplica de leitura, a réplica interrompida se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos

Quando um servidor mestre é excluído, a replicação é interrompida para todas as réplicas de leitura. Essas réplicas se tornam servidores autônomos automaticamente e podem aceitar leituras e gravações. O próprio servidor mestre é excluído.

### <a name="user-accounts"></a>Contas de usuário

Os usuários no servidor mestre são replicados para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

### <a name="server-parameters"></a>Parâmetros do Servidor

Para impedir que os dados fiquem fora de sincronia e evitar possíveis perdas de dados ou danos, alguns parâmetros de servidor estão bloqueados para serem atualizados ao usar réplicas de leitura.

Os seguintes parâmetros do servidor estão bloqueados nos servidores mestre e de réplica:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

O parâmetro [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) está bloqueado nos servidores de réplica.

Para atualizar um dos parâmetros acima no servidor mestre, exclua os servidores de réplica, atualize o valor do parâmetro no mestre e recrie as réplicas.

### <a name="other"></a>Outros

- A criação de uma réplica de uma réplica não é suportada.
- Tabelas na memória podem fazer com que as réplicas fiquem fora de sincronia. Essa é uma limitação da tecnologia de replicação MariaDB.
- Verifique se as tabelas de servidor mestre contêm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre o mestre e as réplicas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](howto-read-replicas-portal.md)
- Saiba como [criar e gerenciar réplicas de leitura usando a CLI do Azure e o API REST](howto-read-replicas-cli.md)
