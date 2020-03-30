---
title: Sincronização LDAP em Ranger e Apache Ambari no Azure HDInsight
description: Dirija-se à sincronização LDAP em Ranger e Ambari e forneça diretrizes gerais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463213"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronização LDAP em Ranger e Apache Ambari no Azure HDInsight

Os clusters do HDInsight Enterprise Security Package (ESP) usam o Ranger para autorização. Apache Ambari e Ranger sincronizam usuários e grupos de forma independente e funcionam um pouco diferente. Este artigo destina-se a abordar a sincronização LDAP em Ranger e Ambari.

## <a name="general-guidelines"></a>Diretrizes gerais

* Sempre implante clusters com grupos.
* Em vez de alterar filtros de grupo em Ambari e Ranger, tente gerenciar tudo isso no Azure AD e use grupos aninhados para trazer os usuários necessários.
* Uma vez que um usuário é sincronizado, ele não é removido mesmo se o usuário não faz parte dos grupos.
* Se você precisar alterar os filtros LDAP diretamente, use a ia primeiro, pois ela contém algumas validações.

## <a name="users-are-synced-separately"></a>Os usuários são sincronizados separadamente

Ambari e Ranger não compartilham o banco de dados do usuário porque servem a dois propósitos diferentes. Se um usuário precisa usar a UI Ambari, então o usuário precisa ser sincronizado com ambari. Se o usuário não estiver sincronizado com o Ambari, a Ambari UI/ API irá rejeitá-la, mas outras partes do sistema funcionarão (estas são guardadas pelo Ranger ou Pelo Gerenciador de Recursos e não pela Ambari). Se você quiser incluir o usuário em uma política ranger, então sincronize o usuário com Ranger.

Quando um cluster seguro é implantado, os membros do grupo são sincronizados transitivamente (todos os subgrupos e seus membros) tanto para ambari quanto para ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Sincronização e configuração do usuário Ambari

Dos nódulos da cabeça, um `/opt/startup_scripts/start_ambari_ldap_sync.py`trabalho de cron, é executado a cada hora para agendar a sincronização do usuário. O trabalho de cron chama as APIs de descanso Ambari para realizar a sincronização. O script envia uma lista de usuários e grupos para sincronizar (como os usuários podem não pertencer aos grupos especificados, ambos são especificados individualmente). O Ambari sincroniza o sAMAccountName como o nome de usuário e todos os membros do grupo, transitivamente.

Os troncos devem `/var/log/ambari-server/ambari-server.log`estar dentro . Para obter mais informações, consulte [Configurar o nível de registro ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

Nos clusters do Data Lake, o gancho de criação de usuário pós é usado para criar as pastas domésticas para os usuários sincronizados e eles são definidos como os proprietários das pastas domésticas. Se o usuário não estiver sincronizado com o Ambari corretamente, o usuário poderá enfrentar falhas no acesso ao estágio e outras pastas temporárias.

### <a name="update-groups-to-be-synced-to-ambari"></a>Grupos de atualização a serem sincronizados com Ambari

Se você não pode gerenciar membros de grupos no Azure AD, você tem duas opções:

* Execute uma sincronização de um tempo como descrito de forma mais completa em [Sincronizar Usuários e Grupos LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Sempre que a associação do grupo mudar, você terá que fazer essa sincronização novamente.

* Escreva um trabalho de cron, ligue para a [API Ambari periodicamente](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) com os novos grupos.

## <a name="ranger-user-sync-and-configuration"></a>Sincronização e configuração do usuário ranger

Ranger tem um mecanismo de sincronização embutido que funciona a cada hora para sincronizar os usuários. Ele não compartilha o banco de dados de usuários com ambari. O HDInsight configura o filtro de pesquisa para sincronizar o usuário de administrador, o usuário do cão de guarda e os membros do grupo especificados durante a criação do cluster. Os membros do grupo serão sincronizados transitivamente:

* Desativar a sincronização incremental.
* Habilite o mapa de sincronização do grupo de usuário.
* Especifique o filtro de pesquisa para incluir os membros do grupo transitivo.
* Sincronize sAMAccountName para usuários e atributo de nome para grupos.

### <a name="group-or-incremental-sync"></a>Sincronização de grupo ou incremental

O Ranger suporta uma opção de sincronização de grupo, mas funciona como uma intersecção com o filtro do usuário. Não é uma união entre membros do grupo e filtro de usuário. Um estojo de uso típico para filtro de sincronização de grupo em Ranger é - filtro de grupo: (dn=clusteradmingroup), filtro de usuário: (city=seattle).

A sincronização incremental funciona apenas para os usuários que já estão sincronizados (a primeira vez). O incremental não sincronizará nenhum novo usuário adicionado aos grupos após a sincronização inicial.

### <a name="update-ranger-sync-filter"></a>Atualizar filtro de sincronização Ranger

O filtro LDAP pode ser encontrado na UI Ambari, na seção de configuração Ranger user-sync. O filtro existente estará na `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`forma . Certifique-se de adicionar predicado no final `net ads` e testar o filtro usando o comando search ou ldp.exe ou algo semelhante.

## <a name="ranger-user-sync-logs"></a>Registros de sincronização do usuário do Ranger

A sincronização do usuário do Ranger pode acontecer a partir de qualquer um dos headnodes. Os registros estão `/var/log/ranger/usersync/usersync.log`dentro . Para aumentar a verbosidade dos logs, faça as seguintes etapas:

1. Faça login em Ambari.
1. Vá para a seção de configuração ranger.
1. Vá para a seção Advanced **usersync-log4j.**
1. Alterar `log4j.rootLogger` o `DEBUG` nível (Após a `log4j.rootLogger = DEBUG,logFile,FilterLog`mudança deve parecer ).
1. Salve a configuração e o ranger de reinicialização.

## <a name="next-steps"></a>Próximas etapas

* [Problemas de autenticação no Azure HDInsight](./domain-joined-authentication-issues.md)
* [Sincronizar usuários do Azure AD com um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
