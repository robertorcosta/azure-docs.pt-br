---
title: Sincronização LDAP no Ranger e Apache Ambari no Azure HDInsight
description: Resolva a sincronização LDAP no Ranger e no Ambari e forneça diretrizes gerais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463213"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronização LDAP no Ranger e Apache Ambari no Azure HDInsight

Os clusters do Enterprise Security Package do HDInsight (ESP) usam o Ranger para autorização. O Apache Ambari e o Ranger sincronizam usuários e grupos de forma independente e funcionam um pouco diferentemente. Este artigo destina-se a tratar da sincronização LDAP no Ranger e no Ambari.

## <a name="general-guidelines"></a>Diretrizes gerais

* Sempre implante clusters com grupos.
* Em vez de alterar os filtros de grupo no Ambari e no Ranger, tente gerenciar todos eles no Azure AD e use grupos aninhados para trazer os usuários necessários.
* Depois que um usuário é sincronizado, ele não é removido mesmo que o usuário não faça parte dos grupos.
* Se você precisar alterar os filtros LDAP diretamente, use a interface do usuário primeiro, pois ele contém algumas validações.

## <a name="users-are-synced-separately"></a>Os usuários são sincronizados separadamente

O Ambari e o Ranger não compartilham o banco de dados do usuário porque atendem a duas finalidades diferentes. Se um usuário precisar usar a interface do usuário do amAmbari, o usuário precisará ser sincronizado com o Ambari. Se o usuário não estiver sincronizado com o Ambari, a interface do usuário/API do Ambari o rejeitará, mas outras partes do sistema funcionarão (elas são protegidas pelo Ranger ou pelo Resource Manager e não Ambari). Se você quiser incluir o usuário em uma política de Ranger, sincronize o usuário para o Ranger.

Quando um cluster seguro é implantado, os membros do grupo são sincronizados transitivamente (todos os subgrupos e seus membros) para Ambari e ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Sincronização e configuração de usuário do Ambari

Dos nós de cabeçalho, um trabalho cron, `/opt/startup_scripts/start_ambari_ldap_sync.py`, é executado a cada hora para agendar a sincronização do usuário. O trabalho cron chama as APIs REST do Ambari para executar a sincronização. O script envia uma lista de usuários e grupos para sincronização (já que os usuários podem não pertencer aos grupos especificados, ambos são especificados individualmente). O Ambari sincroniza o sAMAccountName como o nome de usuário e todos os membros do grupo, transitivamente.

Os logs devem estar em `/var/log/ambari-server/ambari-server.log`. Para obter mais informações, consulte [Configurar o nível de log Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

Em clusters Data Lake, o gancho de criação de usuário post é usado para criar as pastas base para os usuários sincronizados e eles são definidos como os proprietários das pastas base. Se o usuário não estiver sincronizado com o Ambari corretamente, o usuário poderá enfrentar falhas ao acessar o preparo e outras pastas temporárias.

### <a name="update-groups-to-be-synced-to-ambari"></a>Atualizar grupos a serem sincronizados com o Ambari

Se você não puder gerenciar associações de grupos no Azure AD, terá duas opções:

* Execute uma sincronização de uma vez, conforme descrito mais completamente em [sincronizar usuários e grupos LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Sempre que a associação do grupo for alterada, você precisará fazer essa sincronização novamente.

* Escreva um trabalho cron, chame a [API Ambari periodicamente](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) com os novos grupos.

## <a name="ranger-user-sync-and-configuration"></a>Sincronização e configuração de usuário do Ranger

O Ranger tem um mecanismo de sincronização embutido que é executado a cada hora para sincronizar os usuários. Ele não compartilha o banco de dados do usuário com Ambari. O HDInsight configura o filtro de pesquisa para sincronizar o usuário administrador, o usuário Watchdog e os membros do grupo especificados durante a criação do cluster. Os membros do grupo serão sincronizados de forma transitiva:

* Desabilite a sincronização incremental.
* Habilitar o mapa de sincronização do grupo de usuários.
* Especifique o filtro de pesquisa para incluir os membros do grupo transitivo.
* Sincronize o sAMAccountName para os usuários e o atributo de nome para grupos.

### <a name="group-or-incremental-sync"></a>Grupo ou sincronização incremental

O Ranger dá suporte a uma opção de sincronização de grupo, mas funciona como uma interseção com o filtro de usuário. Não é uma União entre associações de grupo e filtro de usuário. Um caso de uso típico para o filtro de sincronização de grupo no Ranger é-grupo filtro: (DN = clusteradmingroup), filtro de usuário: (cidade = Seattle).

A sincronização incremental funciona apenas para os usuários que já estão sincronizados (na primeira vez). A incremental não sincronizará nenhum novo usuário adicionado aos grupos após a sincronização inicial.

### <a name="update-ranger-sync-filter"></a>Atualizar filtro de sincronização do Ranger

O filtro LDAP pode ser encontrado na interface do usuário do amAmbari, na seção de configuração de sincronização de usuários do Ranger. O filtro existente estará no formato `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`. Certifique-se de adicionar predicado no final e testar o filtro usando `net ads` comando de pesquisa ou LDP. exe ou algo semelhante.

## <a name="ranger-user-sync-logs"></a>Logs de sincronização do usuário do Ranger

A sincronização de usuários do Ranger pode ocorrer fora de um dos cabeçalho. Os logs estão em `/var/log/ranger/usersync/usersync.log`. Para aumentar o nível de detalhes dos logs, execute as seguintes etapas:

1. Faça logon no Ambari.
1. Vá para a seção de configuração do Ranger.
1. Vá para a seção **Log4J avançada de usersync** .
1. Altere o `log4j.rootLogger` para `DEBUG` nível (depois de alterar, ele deve ser semelhante a `log4j.rootLogger = DEBUG,logFile,FilterLog`).
1. Salve a configuração e reinicie o Ranger.

## <a name="next-steps"></a>Próximas etapas

* [Problemas de autenticação no Azure HDInsight](./domain-joined-authentication-issues.md)
* [Sincronizar usuários do Azure AD com um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
