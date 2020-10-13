---
title: Replicação de dados-para MariaDB
description: Saiba mais sobre como usar a replicação de dados para sincronizar de um servidor externo no banco de dados do Azure para o serviço MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 66e280f20109967f029a14e368fdb0aeea269aad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536606"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replicar dados no Banco de Dados do Azure para MariaDB

A Replicação de Dados permite sincronizar dados de um servidor do MariaDB executado localmente em máquinas virtuais ou em serviços de banco de dados hospedados por outros provedores de nuvem no serviço do Banco de Dados do Azure para MariaDB. A Replicação de Dados é baseada na replicação nativa com base na posição do arquivo de log binário (binlog) para o MariaDB. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Quando usar a replicação nos dados
Os cenários principais nos quais considerar o uso da replicação nos dados são:

- **Sincronização de dados híbrida:** com a Replicação de Dados, é possível manter os dados sincronizados entre os servidores locais e o Banco de Dados do Azure para MariaDB. Essa sincronização é útil para criar aplicativos híbridos. Esse método é atraente quando você tem um servidor de banco de dados local existente, mas deseja mover os dados para uma região mais próxima aos usuários finais.
- **Sincronização de várias nuvens:** para soluções de nuvem complexas, use a Replicação de Dados para sincronizar dados entre o Banco de Dados do Azure para MariaDB e diferentes provedores de nuvem, incluindo máquinas virtuais e serviços de banco de dados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
O [*banco de dados do sistema MySQL*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) no servidor de origem não está replicado. As alterações nas contas e permissões no servidor de origem não são replicadas. Se você criar uma conta no servidor de origem e essa conta precisar acessar o servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais tabelas estão contidas no banco de dados do sistema, consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requisitos
- A versão do servidor de origem deve ser pelo menos MariaDB versão 10,2.
- As versões do servidor de origem e de réplica devem ser as mesmas. Por exemplo, ambos devem ser MariaDB versão 10.2.
- Cada tabela deve ter uma chave primária.
- O servidor de origem deve usar o mecanismo InnoDB.
- O usuário deve ter permissões para configurar o log binário e criar novos usuários no servidor de origem.
- Se o servidor de origem tiver o SSL habilitado, verifique se o certificado de autoridade de certificação SSL fornecido para o domínio foi incluído no `mariadb.az_replication_change_master` procedimento armazenado. Consulte os [exemplos](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) a seguir e o `master_ssl_ca` parâmetro.
- Verifique se o endereço IP do servidor de origem foi adicionado ao banco de dados do Azure para as regras de firewall do servidor de réplica MariaDB. Atualizar regras de firewall usando o [Portal do Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) ou a [CLI do Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Verifique se o computador que hospeda o servidor de origem permite o tráfego de entrada e de saída na porta 3306.
- Verifique se o servidor de origem tem um **endereço IP público**, se o DNS está acessível publicamente ou se tem um FQDN (nome de domínio totalmente qualificado).

### <a name="other"></a>Outros
- A replicação de dados têm suporte apenas em tipos de preços de Uso Geral e Otimizados para Memória.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar a replicação de dados](howto-data-in-replication.md).
