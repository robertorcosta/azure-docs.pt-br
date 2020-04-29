---
title: Replicação de dados-para MariaDB
description: Saiba mais sobre como usar a replicação de dados para sincronizar de um servidor externo no banco de dados do Azure para o serviço MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1fbcc1fb27d5e6df4641f79c0d634580f74000b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79532053"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replicar dados no Banco de Dados do Azure para MariaDB

A Replicação de Dados permite sincronizar dados de um servidor do MariaDB executado localmente em máquinas virtuais ou em serviços de banco de dados hospedados por outros provedores de nuvem no serviço do Banco de Dados do Azure para MariaDB. A Replicação de Dados é baseada na replicação nativa com base na posição do arquivo de log binário (binlog) para o MariaDB. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Quando usar a replicação nos dados
Os cenários principais nos quais considerar o uso da replicação nos dados são:

- **Sincronização de dados híbrida:** com a Replicação de Dados, é possível manter os dados sincronizados entre os servidores locais e o Banco de Dados do Azure para MariaDB. Essa sincronização é útil para criar aplicativos híbridos. Esse método é atraente quando você tem um servidor de banco de dados local existente, mas deseja mover os dados para uma região mais próxima aos usuários finais.
- **Sincronização de várias nuvens:** para soluções de nuvem complexas, use a Replicação de Dados para sincronizar dados entre o Banco de Dados do Azure para MariaDB e diferentes provedores de nuvem, incluindo máquinas virtuais e serviços de banco de dados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
O [*banco de dados de sistema de mysql*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) no servidor mestre não é replicado. Alterações nas contas e permissões no servidor mestre não são replicadas. Se você criar uma conta no servidor mestre e essa conta precisar acessar o servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais tabelas estão contidas no banco de dados do sistema, consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requisitos
- A versão do servidor mestre deve ser pelo menos a versão 10.2 do MariaDB.
- As versões do servidor principal e de réplica devem ser as mesmas. Por exemplo, ambos devem ser MariaDB versão 10.2.
- Cada tabela deve ter uma chave primária.
- O servidor mestre deve usar o mecanismo InnoDB.
- O usuário deve ter permissões para configurar o log binário e criar novos usuários no servidor mestre.
- Se o servidor mestre tiver o SSL habilitado, verifique se o certificado de autoridade de certificação SSL fornecido para o domínio `mariadb.az_replication_change_master` foi incluído no procedimento armazenado. Consulte os [exemplos](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) a seguir e o `master_ssl_ca` parâmetro.
- Verifique se o endereço IP do servidor mestre foi adicionado às regras de firewall do servidor de réplica do Banco de Dados do Azure para MariaDB. Atualizar regras de firewall usando o [Portal do Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) ou a [CLI do Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Assegure-se de que o computador que hospeda o servidor mestre permita tráfego de entrada e saída na porta 3306.
- Verifique se o servidor mestre tem um **endereço IP público**, se o DNS está acessível publicamente ou se tem um FQDN (nome de domínio totalmente qualificado).

### <a name="other"></a>Outros
- A replicação de dados têm suporte apenas em tipos de preços de Uso Geral e Otimizados para Memória.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar a replicação de dados](howto-data-in-replication.md).
