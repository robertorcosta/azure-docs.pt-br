---
title: Solução de erros comuns – Banco de Dados do Azure para MySQL
description: Saiba como solucionar problemas de erros comuns de migração encontrados pelos novos usuários do serviço Banco de Dados do Azure para MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ebe9f936e3d0dfafec23842fcdbfd225995d546b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719771"
---
# <a name="common-errors"></a>Erros comuns

O Banco de Dados do Azure para MySQL é um serviço totalmente gerenciado ativado pela versão da comunidade do MySQL. A experiência do MySQL em um ambiente de serviço gerenciado pode ser diferente da execução do MySQL no próprio ambiente. Neste artigo, você verá alguns dos erros comuns que os usuários podem receber ao fazer a migração para o serviço Banco de Dados do Azure para MySQL ou fazer o desenvolvimento nele pela primeira vez.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Erros devido à ausência do privilégio SUPER e da função DBA

Não há suporte para o privilégio SUPER nem para a função DBA no serviço. Como resultado, você poderá receber alguns erros comuns listados abaixo:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERRO 1419: você não tem o privilégio SUPER e o log binário está habilitado (o *ideal* é usar a variável log_bin_trust_function_creators menos segura)

O erro acima pode ocorrer durante a criação de uma função ou um gatilho, conforme mostrado abaixo, ou durante a importação de um esquema. As instruções DDL como CREATE FUNCTION ou CREATE TRIGGER são gravadas no log binário, de modo que a réplica secundária possa executá-las. O thread SQL de réplica tem privilégios completos, que pode ser explorado para elevar os privilégios. Para se proteger contra esse perigo nos servidores que têm o log binário habilitado, o mecanismo MySQL exige que os criadores de funções armazenadas tenham o privilégio SUPER, além do privilégio habitual CREATE ROUTINE que é necessário. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Resolução:**  para resolver o erro, defina log_bin_trust_function_creators como 1 na folha [Parâmetros do servidor](howto-server-parameters.md) no portal, execute as instruções DDL ou importe o esquema para criar os objetos desejados e reverta o parâmetro log_bin_trust_function_creators para o valor anterior após a criação.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERRO 1227 (42000) na linha 101: acesso negado; você precisará ter (pelo menos um dos) privilégios SUPER para executar essa operação. Falha na operação com o código de saída 1

O erro acima pode ocorrer durante a importação de um arquivo de despejo ou a criação de um procedimento que contenha [definidores](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Resolução:**  para resolver esse erro, o usuário administrador pode conceder privilégios para criar ou executar procedimentos executando o comando GRANT, como nos seguintes exemplos:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Como alternativa, você pode substituir os definidores pelo nome do usuário administrador que está executando o processo de importação, conforme mostrado abaixo.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>Próximas etapas
Se você não encontrou a resposta que estava procurando, considere o seguinte:
- Poste sua pergunta na [página de perguntas do Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) ou no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Envie um email para a Equipe do Banco de Dados do Azure para MySQL [@Ask Azure DB para MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Esse endereço de email não é um alias de suporte técnico.
- Para entrar em contato com o Suporte do Azure, [crie um tíquete no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
