---
title: solução de problemas
description: Solução de problemas do Azure Synapse Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 085b907b6a848fb534df63b5465948864048cc19
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256478"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Solução de problemas de análise de SQL no Azure Synapse
Este artigo lista perguntas de solução de problemas comuns.

## <a name="connecting"></a>Connecting
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Falha de logon do usuário 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, erro: 18456) | Esse erro ocorre quando um usuário do AAD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o pool SQL ao qual você deseja se conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Confira o artigo [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md) para obter mais detalhes. |
| A entidade de segurança do servidor "MyUserName" não é capaz de acessar o banco de dados "mestre" no contexto de segurança atual. Não é possível abrir o banco de dados padrão do usuário. Falha no logon. Falha de logon do usuário 'MyUserName'. (Microsoft SQL Server, erro: 916) | Esse erro ocorre quando um usuário do AAD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o pool SQL ao qual você deseja se conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Confira o artigo [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md) para obter mais detalhes. |
| Erro CTAIP                                                  | Esse erro pode ocorrer quando um logon é criado no banco de dados mestre do SQL Server, mas não no banco de dados SQL.  Se você encontrar esse erro, examine o artigo [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md) .  Este artigo explica como criar um logon e um usuário no mestre e como criar um usuário no banco de dados SQL. |
| Bloqueado pelo firewall                                          | Os pools do SQL são protegidos por firewalls para garantir que apenas endereços IP conhecidos tenham acesso a um banco de dados. Os firewalls são seguros por padrão, o que significa que você deve habilitar explicitamente um endereço IP ou um intervalo de endereços antes de se conectar.  Para configurar o firewall para acesso, siga as etapas em [Configurar o acesso de firewall do servidor para o IP do cliente](sql-data-warehouse-get-started-provision.md) nas [instruções de provisionamento](sql-data-warehouse-get-started-provision.md). |
| Não é possível conectar-se com a ferramenta ou driver                           | O SQL Analytics recomenda usar o [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), o [SSDT para Visual Studio](sql-data-warehouse-install-visual-studio.md)ou o [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) para consultar seus dados. Para obter mais informações sobre drivers e conectar-se ao Azure Synapse, confira [drivers do Azure Synapse](sql-data-warehouse-connection-strings.md) e [Conecte-se aos artigos do Azure Synapse](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>Ferramentas
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| O Pesquisador de objetos do Visual Studio não tem usuários de AAD           | Esse é um problema conhecido.  Como alternativa, exiba os usuários em [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Consulte [autenticação no Azure Synapse](sql-data-warehouse-authentication.md) para saber mais sobre como usar Azure Active Directory com a análise de SQL. |
| O script manual, o uso do assistente para criação de scripts ou a conexão via SSMS são lentos, não respondem ou produzem erros | Certifique-se de que os usuários foram criados no banco de dados mestre. Nas opções de script, certifique-se também de que a edição do mecanismo está definida como "Edição do SQL Data Warehouse do Microsoft Azure" e o tipo de mecanismo é "Banco de Dados SQL do Microsoft Azure". |
| Gerar falhas de script no SSMS                               | A geração de um script para análise do SQL falhará se a opção "gerar script para objetos dependentes" estiver definida como "true". Como alternativa, os usuários devem ir manualmente para **ferramentas-> opções-> pesquisador de objetos do SQL Server-> gerar script para opções dependentes e definir como false** |

## <a name="performance"></a>Desempenho
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Solucionar problemas de desempenho da consulta                            | Caso você esteja tentando solucionar problemas de uma determinada consulta, comece com [Aprendendo a monitorar suas consultas](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemas de espaço do TempDB | [Monitorar](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-tempdb) o uso de espaço de tempdb.  As causas comuns para a execução fora do espaço de TempDB são:<br>-Não há recursos suficientes alocados para a consulta, fazendo com que os dados sejam despejados para TempDB.  Consulte [Gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) <br>-As estatísticas estão ausentes ou desatualizadas causando uma movimentação de dados excessiva.  Consulte [mantendo as estatísticas da tabela](sql-data-warehouse-tables-statistics.md) para obter detalhes sobre como criar estatísticas<br>-O espaço de TempDB é alocado por nível de serviço.  [Dimensionar o pool do SQL](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scaling-compute) para uma configuração DWU mais alta aloca mais espaço tempdb.|
| Planos e consultas com um desempenho ruim normalmente são o resultado da falta de estatísticas | A causa mais comum do mau desempenho é a falta de estatísticas em tabelas.  Consulte [mantendo estatísticas de tabela](sql-data-warehouse-tables-statistics.md) para obter detalhes sobre como criar estatísticas e por que elas são críticas para o desempenho. |
| Baixa simultaneidade/consultas em fila                             | É importante entender o [Gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) para compreender como equilibrar a alocação de memória com simultaneidade. |
| Como implementar as práticas recomendadas                              | O melhor lugar para começar a aprender maneiras de melhorar o desempenho da consulta é o artigo [práticas recomendadas da análise do SQL](sql-data-warehouse-best-practices.md) . |
| Como melhorar o desempenho com o dimensionamento                      | Às vezes, a solução para melhorar o desempenho é simplesmente adicionar mais poder de computação às suas consultas, [dimensionando o pool do SQL](sql-data-warehouse-manage-compute-overview.md). |
| Desempenho ruim da consulta como resultado da baixa qualidade do índice     | Algumas vezes as consultas podem ficar lentas devido à [baixa qualidade do índice columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Consulte este artigo para saber mais e para saber como [Recriar índices a fim de melhorar a qualidade do segmento](../sql-data-warehouse/sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Gerenciamento do sistema
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: não foi possível executar a operação, pois o servidor excederia a cota de Unidade de Transação de Banco de Dados permitida de 45000. | Reduza o [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) do banco de dados que você está tentando criar ou [solicite um aumento de cota](sql-data-warehouse-get-started-create-support-ticket.md). |
| Investigação da utilização de espaço                              | Confira [Tamanhos da tabela]( ../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) para entender a utilização do espaço de seu sistema. |
| Ajuda com o gerenciamento de tabelas                                    | Consulte o artigo [visão geral da tabela](../sql-data-warehouse/sql-data-warehouse-tables-overview.md) para obter ajuda com o gerenciamento de suas tabelas.  Este artigo também inclui links para tópicos mais detalhados, como [tipos de dados de tabela](sql-data-warehouse-tables-data-types.md), [distribuição de uma tabela](sql-data-warehouse-tables-distribute.md), [indexação de uma tabela](sql-data-warehouse-tables-index.md), [particionamento de uma tabela](sql-data-warehouse-tables-partition.md), [manutenção de estatísticas de tabela](sql-data-warehouse-tables-statistics.md) e [tabelas temporárias](sql-data-warehouse-tables-temporary.md). |
| A barra de progresso da TDE (Transparent Data Encryption) não está atualizando no portal do Azure | Você pode exibir o estado de TDE via [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Diferenças do Banco de Dados SQL
| Problema                                 | Resolução                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Recursos do Banco de Dados SQL sem suporte     | Confira [Recursos de tabela sem suporte](../sql-data-warehouse/sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipos de dados do Banco de Dados SQL sem suporte   | Confira [Tipos de dados sem suporte](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Limitações de DELETE e UPDATE         | Consulte [soluções alternativas de atualização](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [soluções alternativas de exclusão](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) e [uso de CTAS para contornar a sintaxe de atualização e exclusão sem suporte](sql-data-warehouse-develop-ctas.md). |
| Não há suporte para a instrução MERGE      | Confira [Substituir instruções MERGE](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Limitações de procedimento armazenado          | Confira [Limitações de procedimentos armazenados](../sql-data-warehouse/sql-data-warehouse-develop-stored-procedures.md#limitations) para entender algumas dessas limitações. |
| UDFs não oferecem suporte a instruções SELECT | Esta é uma limitação atual de nossos UDFs.  Confira [CREATE FUNCTION](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) para ver a sintaxe para a qual damos suporte. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais ajuda na busca de soluções para o problema, a seguir são apresentados alguns outros recursos que você pode experimentar.

* [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Solicitações de recursos](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
