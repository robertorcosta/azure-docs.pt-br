---
title: Backup and restore in Azure Database for PostgreSQL - Single Server
description: Learn about automatic backups and restoring your Azure Database for PostgreSQL server - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: cbd434f2f9e1217a3a481a28988f4e1d855ba08b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209649"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Backup and restore in Azure Database for PostgreSQL - Single Server

O Banco de Dados do Azure para PostgreSQL cria backups de servidor automaticamente e os armazena no armazenamento com redundância geográfica ou local configurado pelo usuário. Os backups podem ser usados para restaurar o servidor pontualmente. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.

## <a name="backups"></a>Backups

Azure Database for PostgreSQL takes backups of the data files and the transaction log. Depending on the supported maximum storage size, we either take full and differential backups (4 TB max storage servers) or snapshot backups (up to 16 TB max storage servers). Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

### <a name="backup-frequency"></a>Frequência de backup

Generally, full backups occur weekly, differential backups occur twice a day for servers with a max supported storage of 4 TB. Snapshot backups happen at least once a day for servers that support up to 16 TB of storage. Transaction log backups in both cases occur every five minutes. The first snapshot of full backup is scheduled immediately after a server is created. The initial full backup can take longer on a large restored server. O ponto mais anterior para o qual um novo servidor pode ser restaurado é o momento da conclusão do backup completo inicial. As snapshots are instantanious, servers with support up to 16 TB of storage can be restored all the way back to the create time.

### <a name="backup-redundancy-options"></a>Opções de redundância de backup

O Banco de Dados do Azure para PostgreSQL fornece a flexibilidade de escolher entre o armazenamento de backup com redundância local ou com redundância geográfica nas camadas de Uso Geral e Otimizado para Memória. Quando os backups são armazenados no armazenamento de backup com redundância geográfica, eles não são somente armazenados dentro da região em que o servidor está hospedado, mas também replicados em um [datacenter emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Isso fornece maior proteção e capacidade de restaurar o servidor em uma região diferente em caso de desastre. A camada Básica oferece apenas o armazenamento de backup de redundância local.

> [!IMPORTANT]
> A configuração do armazenamento com redundância local ou geográfica para backup só é permitida durante a criação do servidor. Quando o servidor é provisionado, você não pode alterar a opção de redundância do armazenamento de backup.

### <a name="backup-storage-cost"></a>Custo do armazenamento de backup

O Banco de Dados do Azure para PostgreSQL fornece até 100% de seu armazenamento de servidor configurado como armazenamento de backup sem custo adicional. Normalmente, isso é adequado para uma retenção de backup de sete dias. O armazenamento de backup adicional que for utilizado será cobrado em GB-mês.

Por exemplo, se você tiver provisionado um servidor com 250 GB, terá 250 GB de armazenamento de backup sem custo adicional. O armazenamento acima dos 250 GB é cobrado.

## <a name="restore"></a>Restaurar

No Banco de Dados do Azure para PostgreSQL, a execução de uma restauração cria um novo servidor de backup do servidor original.

Há dois tipos de restauração disponíveis:

- A **Restauração pontual** está disponível em qualquer opção de redundância de backup e cria um novo servidor na mesma região do servidor original.
- A **Restauração geográfica** está disponível somente se você configurou seu servidor para armazenamento com redundância geográfica; ele permite que você restaure o servidor em uma região diferente.

O tempo estimado de recuperação dependerá de vários fatores, incluindo os tamanhos dos bancos de dados, o tamanho do log de transações, a largura de banda de rede e o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo. Normalmente, o tempo de recuperação é menor do que 12 horas.

> [!IMPORTANT]
> Servidores excluídos **não podem** ser restaurados. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Para proteger recursos do servidor, após a implantação, da exclusão acidental ou de alterações inesperadas, os administradores podem usar [bloqueios de gerenciamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Restauração pontual

Independentemente de sua opção de redundância de backup, você pode executar uma restauração para qualquer ponto anterior dentro de seu período de retenção de backup. Um novo servidor é criado na mesma região do Azure do servidor original. Ele é criado com a configuração do servidor original para o tipo de preço, a geração de computação, o número de núcleos virtuais, o tamanho do armazenamento, o período de retenção de backup e a opção de redundância de backup.

A Restauração pontual é útil em vários cenários. Por exemplo, quando um usuário exclui dados acidentalmente, descarta uma tabela ou um banco de dados importante, ou se um aplicativo acidentalmente substitui dados bons por dados inválidos devido a um defeito no aplicativo.

Talvez seja necessário aguardar a execução do próximo backup de log de transações antes de poder restaurar para um ponto anterior nos últimos cinco minutos.

### <a name="geo-restore"></a>Restauração geográfica

É possível restaurar um servidor para outra região do Azure onde o serviço está disponível caso você tenha configurado o servidor para backups com redundância geográfica. Servers that support up to 4 TB of storage can be restored to the geo-paired region, or to any region that supports up to 16 TB of storage. For servers that support up to 16 TB of storage, geo-backups can be restored in any region that support 16 TB servers as well. Review [Azure Database for PostgeSQL pricing tiers](concepts-pricing-tiers.md) for the list of supported regions.

A restauração geográfica é a opção de recuperação padrão quando o servidor não está disponível devido a um incidente na região em que ele está hospedado. Se um incidente de grande escala em uma região resultar na indisponibilidade do seu aplicativo de banco de dados, você poderá restaurar um servidor do backup com redundância geográfica para um servidor em qualquer outra região. Há um atraso entre quando um backup é feito e quando ele é replicado em uma região diferente. Esse atraso pode ser de até uma hora, então, em caso de desastre pode haver perda de dados de até uma hora.

Durante a restauração geográfica, as configurações de servidor que podem ser alteradas incluem as opções de geração de computação, vCore, período de retenção de backup e redundância de backup. Não há suporte para alterar o tipo de preço (básico, uso geral ou com otimização de memória) ou tamanho de armazenamento.

### <a name="perform-post-restore-tasks"></a>Executar tarefas de pós-restauração

Após uma restauração de um dos mecanismos de recuperação, você deve executar as seguintes tarefas para colocar os usuários e os aplicativos novamente em execução:

- Se o novo servidor é usado para substituir o servidor original, redirecione clientes e aplicativos de cliente para o novo servidor
- Verifique se as regras de firewall adequadas no nível do servidor estão em vigor para que os usuários se conectem
- Verifique se as permissões e os logons adequados no nível do banco de dados estão em vigor
- Configurar os alertas, conforme apropriado

## <a name="next-steps"></a>Próximos passos

- Learn how to restore using [the Azure portal](howto-restore-server-portal.md).
- Learn how to restore using [the Azure CLI](howto-restore-server-cli.md).
- Para saber mais sobre continuidade dos negócios, confira a  [visão geral de continuidade dos negócios](concepts-business-continuity.md).
