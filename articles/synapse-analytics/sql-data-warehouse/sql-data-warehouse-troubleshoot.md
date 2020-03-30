---
title: Solução de problemas
description: Solução de problemas Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b1b841c7532e975f9a1ad928ffd0559231207fb2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350016"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Solução de problemas SQL Analytics no Azure Synapse
Este artigo lista perguntas de solução de problemas comuns.

## <a name="connecting"></a>Connecting
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Falha de logon do usuário 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, erro: 18456) | Esse erro ocorre quando um usuário do AAD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o pool SQL ao qual deseja se conectar na hora da conexão ou adicione o usuário ao banco de dados principal.  Consulte o artigo [visão geral da segurança](sql-data-warehouse-overview-manage-security.md) para obter mais detalhes. |
| A entidade de segurança do servidor "MyUserName" não é capaz de acessar o banco de dados "mestre" no contexto de segurança atual. Não é possível abrir o banco de dados padrão do usuário. Falha no logon. Falha de logon do usuário 'MyUserName'. (Microsoft SQL Server, erro: 916) | Esse erro ocorre quando um usuário do AAD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o pool SQL ao qual deseja se conectar na hora da conexão ou adicione o usuário ao banco de dados principal.  Consulte o artigo [visão geral da segurança](sql-data-warehouse-overview-manage-security.md) para obter mais detalhes. |
| Erro CTAIP                                                  | Esse erro pode ocorrer quando um login foi criado no banco de dados mestre do servidor SQL, mas não no banco de dados SQL.  Se você encontrar esse erro, examine o artigo [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md) .  Este artigo explica como criar um login e um usuário no master e, em seguida, como criar um usuário no banco de dados SQL. |
| Bloqueado pelo firewall                                          | Os pools SQL são protegidos por firewalls para garantir que apenas endereços IP conhecidos tenham acesso a um banco de dados. Os firewalls são seguros por padrão, o que significa que você deve habilitar explicitamente um endereço IP ou um intervalo de endereços antes de se conectar.  Para configurar seu firewall para acesso, siga as etapas na seção [Configurar o acesso ao servidor de firewall para o IP do cliente](create-data-warehouse-portal.md) nas [Instruções de provisionamento](create-data-warehouse-portal.md). |
| Não é possível conectar-se com a ferramenta ou driver                           | O SQL Analytics recomenda o uso de [SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) [SSDT para Visual Studio](sql-data-warehouse-install-visual-studio.md)ou [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) para consultar seus dados. Para obter mais informações sobre drivers e conectar-se ao Azure Synapse, consulte [Drivers for Azure Synapse](sql-data-warehouse-connection-strings.md) e [Conecte-se aos artigos do Azure Synapse.](sql-data-warehouse-connect-overview.md) |

## <a name="tools"></a>Ferramentas
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| O Pesquisador de objetos do Visual Studio não tem usuários de AAD           | Esse é um problema conhecido.  Como alternativa, exiba os usuários em [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Consulte [Autenticação no Azure Synapse](sql-data-warehouse-authentication.md) para saber mais sobre como usar o Azure Active Directory com o SQL Analytics. |
| O scriptamento manual, usando o assistente de script ou a conexão via SSMS é lento, não responde ou produz erros | Certifique-se de que os usuários foram criados no banco de dados mestre. Nas opções de scripting, certifique-se também de que a edição do mecanismo seja definida como "Microsoft Azure SQL Data Warehouse Edition" e o tipo de mecanismo é "Microsoft Azure SQL Database". |
| Gerar falhas de script no SSMS                               | A geração de um script para SQL Analytics falha se a opção "Gerar script para objetos dependentes" for definida como "True". Como solução de solução, os usuários devem ir manualmente para **Ferramentas -> Opções ->SQL Server Object Explorer -> Gerar script para opções dependentes e definir como falso** |

## <a name="performance"></a>Desempenho
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Solucionar problemas de desempenho da consulta                            | Caso você esteja tentando solucionar problemas de uma determinada consulta, comece com [Aprendendo a monitorar suas consultas](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemas espaciais tempDB | Monitore o uso do espaço [TempDB.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  As causas comuns para ficar sem espaço tempDB são:<br>- Não há recursos suficientes alocados na consulta fazendo com que os dados sejam derramados no TempDB.  Ver [gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) <br>- As estatísticas estão faltando ou desatualizadas causando movimentação excessiva de dados.  Consulte [A tabela de estatísticas de manutenção](sql-data-warehouse-tables-statistics.md) para obter detalhes sobre como criar estatísticas<br>- O espaço tempDB é alocado por nível de serviço.  [Dimensionar seu pool SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) para uma configuração DWU mais alta aloca mais espaço TempDB.|
| Planos e consultas com um desempenho ruim normalmente são o resultado da falta de estatísticas | A causa mais comum do mau desempenho é a falta de estatísticas em tabelas.  Confira [Como manter estatísticas de tabela](sql-data-warehouse-tables-statistics.md) para obter detalhes sobre como criar estatísticas e por que elas são essenciais para o desempenho. |
| Baixa simultaneidade/consultas em fila                             | É importante entender o [Gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) para compreender como equilibrar a alocação de memória com simultaneidade. |
| Como implementar as práticas recomendadas                              | O melhor lugar para começar a aprender maneiras de melhorar o desempenho da consulta é o artigo [de práticas recomendadas do SQL Analytics.](sql-data-warehouse-best-practices.md) |
| Como melhorar o desempenho com o dimensionamento                      | Às vezes, a solução para melhorar o desempenho é simplesmente adicionar mais poder de computação às suas [consultas, dimensionando seu pool SQL](sql-data-warehouse-manage-compute-overview.md). |
| Desempenho ruim da consulta como resultado da baixa qualidade do índice     | Às vezes, as consultas podem apresentar lentidão devido à [Baixa qualidade do índice columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Consulte este artigo para saber mais e para saber como [Recriar índices a fim de melhorar a qualidade do segmento](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Gerenciamento do sistema
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: não foi possível executar a operação, pois o servidor excederia a cota de Unidade de Transação de Banco de Dados permitida de 45000. | Reduza a [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) do banco de dados que você está tentando criar ou [solicite um aumento de cota](sql-data-warehouse-get-started-create-support-ticket.md). |
| Investigação da utilização de espaço                              | Confira [Tamanhos da tabela]( ../../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) para entender a utilização do espaço de seu sistema. |
| Ajuda com o gerenciamento de tabelas                                    | Confira o artigo [Visão geral da tabela](sql-data-warehouse-tables-overview.md) para obter ajuda com o gerenciamento de suas tabelas.  Este artigo também inclui links para tópicos mais detalhados, como [Tipos de dados de tabela](sql-data-warehouse-tables-data-types.md), [Distribuindo uma tabela](sql-data-warehouse-tables-distribute.md), [Indexando uma tabela](sql-data-warehouse-tables-index.md), [Particionando uma tabela](sql-data-warehouse-tables-partition.md), [Mantendo as estatísticas da tabela](sql-data-warehouse-tables-statistics.md) e [Tabelas temporárias](sql-data-warehouse-tables-temporary.md). |
| A barra de progresso da criptografia de dados transparente (TDE) não está atualizada no portal Dozure | Você pode exibir o estado de TDE via [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Diferenças do Banco de Dados SQL
| Problema                                 | Resolução                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Recursos do Banco de Dados SQL sem suporte     | Confira [Recursos de tabela sem suporte](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipos de dados do Banco de Dados SQL sem suporte   | Confira [Tipos de dados sem suporte](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Limitações de DELETE e UPDATE         | Confira [Soluções alternativas de UPDATE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [Soluções alternativas de DELETE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) e [Como usar CTAS para contornar a sintaxe UPDATE e DELETE sem suporte](sql-data-warehouse-develop-ctas.md). |
| Não há suporte para a instrução MERGE      | Confira [Substituir instruções MERGE](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Limitações de procedimento armazenado          | Confira [Limitações de procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md#limitations) para entender algumas dessas limitações. |
| UDFs não oferecem suporte a instruções SELECT | Esta é uma limitação atual de nossos UDFs.  Confira [CREATE FUNCTION](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) para ver a sintaxe para a qual damos suporte. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais ajuda na busca de soluções para o problema, a seguir são apresentados alguns outros recursos que você pode experimentar.

* [Blogues](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Solicitações de recursos](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
