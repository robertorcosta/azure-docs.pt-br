---
title: Erros transitórios de conectividade - Banco de dados Azure para MySQL
description: Aprenda a lidar com erros de conectividade transitórios e conecte-se eficientemente ao Banco de Dados Azure para MySQL.
keywords: conexão mysql,string de conexão,problemas de conectividade,erro transitório,erro de conexão,conecte-se eficientemente
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4f9101b4108f5512ee9779f4633845b34fdfad5a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767868"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Lidar com erros transitórios e conecte-se eficientemente ao Banco de Dados Azure para MySQL

Este artigo descreve como lidar com erros transitórios e conectar-se eficientemente ao Banco de Dados Azure para MySQL.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como uma falha transitória, é um erro que será resolvido por si só. Geralmente, esses erros manifestam como uma conexão para o servidor de banco de dados que está sendo descartado. Além disso, as novas conexões com um servidor não podem ser abertas. Os erros transitórios podem ocorrer, por exemplo, quando ocorre uma falha de hardware ou de rede. Outra razão pode ser uma nova versão de um serviço PaaS que está sendo implantado. A maioria desses eventos são automaticamente mitigados pelo sistema em menos de 60 segundos. Uma prática recomendada para projetar e desenvolver aplicativos na nuvem é esperar erros transitórios. Suponha que pode acontecer em qualquer componente a qualquer momento e ter a lógica apropriada em vigor para lidar com essas situações.

## <a name="handling-transient-errors"></a>Tratamento de erros transitórios

Os erros transitórios devem ser manipulados usando a lógica de repetição. Situações em que devem ser consideradas:

* Ocorre um erro quando você tentar abrir uma conexão
* Uma conexão ociosa é descartada no lado do servidor. Quando você tenta emitir um comando, ele não pode ser executado
* Uma conexão ativa que esteja executando um comando é descartada.

A primeira e a segunda ocorrência são razoavelmente diretas de lidar. Tente abrir a conexão novamente. Quando você tiver êxito, o erro transitório terá sido reduzido pelo sistema. Você pode usar seu Banco de Dados do Azure para MySQL novamente. Recomendamos ter esperas antes de tentar novamente a conexão. Desista se as tentativas iniciais falharem. Dessa forma, o sistema pode usar todos os recursos disponíveis para superar a situação de erro. Um bom padrão a seguir é:

* Aguarde cinco segundos até a primeira tentativa.
* Para cada próxima repetição, a espera aumenta exponencialmente, para até 60 segundos.
* Defina um número máximo de repetições no ponto em que seu aplicativo considera que a operação falhou.

Quando uma conexão com uma transação ativa falha, é mais difícil de lidar com a recuperação corretamente. Há dois casos: se a transação era somente leitura por natureza, é seguro para reabrir a conexão e tentar a transação novamente. Se, no entanto, a transação também estava escrevendo para o banco de dados, você deve determinar se a transação foi revertida ou se ela foi bem sucedida antes do erro transitório acontecer. Nesse caso, você pode simplesmente não ter recebido o reconhecimento de confirmação do servidor de banco de dados.

Uma maneira de fazer isso, é gerar uma ID exclusiva no cliente que é usado para todas as tentativas. Você pode passar essa ID exclusiva como parte da transação para o servidor e armazená-los em uma coluna com uma restrição exclusiva. Dessa forma, com segurança, você pode repetir a transação. Ele terá sucesso se a transação anterior for revertida e o ID exclusivo gerado pelo cliente ainda não existir no sistema. Ocorrerá uma falha indicando que uma violação de chave duplicada se a ID exclusiva foi armazenada anteriormente porque a transação anterior foi concluída com êxito.

Quando o programa se comunica com o Banco de Dados do Azure para MySQL por meio de um middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

Teste a lógica de repetição. Por exemplo, tente executar seu código enquanto faz o dimensionamento para cima ou para baixo dos recursos de computação do seu banco de dados Azure para o servidor MySQL. Seu aplicativo deve lidar com o breve tempo de inatividade encontrado durante a operação sem qualquer problema.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Conecte-se eficientemente ao banco de dados Azure para MySQL

As conexões de banco de dados são um recurso limitado, portanto, fazer uso efetivo do pool de conexões para acessar o Azure Database for MySQL otimiza o desempenho. A seção abaixo explica como usar o pool de conexões ou conexões persistentes para acessar de forma mais eficaz o Azure Database para MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Acessar bancos de dados usando pooling de conexões (recomendado)

O gerenciamento de conexões de banco de dados pode ter um impacto significativo no desempenho do aplicativo como um todo. Para otimizar o desempenho de sua aplicação, o objetivo deve ser reduzir o número de vezes que as conexões são estabelecidas e o tempo para estabelecer conexões em caminhos de código-chave. Recomendamos fortemente o uso de conexões de banco de dados ou conexões persistentes para conectar ao Banco de Dados Azure para MySQL. O pool de conexões de banco de dados lida com a criação, gerenciamento e alocação de conexões de banco de dados. Quando um programa solicita uma conexão de banco de dados, ele prioriza a alocação de conexões de banco de dados ociosas existentes, em vez da criação de uma nova conexão. Após o término do programa usando a conexão de banco de dados, a conexão é recuperada em preparação para uso posterior, em vez de simplesmente ser fechada.

Para melhor ilustração, este artigo fornece [um pedaço de código de amostra](./sample-scripts-java-connection-pooling.md) que usa JAVA como exemplo. Para obter mais informações, consulte [Apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> O servidor configura um mecanismo de tempo para fechar uma conexão que está em um estado ocioso há algum tempo para liberar recursos. Certifique-se de configurar o sistema de verificação para garantir a eficácia das conexões persistentes quando estiver usando-as. Para obter mais informações, consulte [Configurar sistemas de verificação no lado do cliente para garantir a eficácia das conexões persistentes](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Acessar bancos de dados usando conexões persistentes (recomendado)

O conceito de conexões persistentes é semelhante ao do pool de conexões. Substituir conexões curtas por conexões persistentes requer apenas pequenas alterações no código, mas tem um grande efeito em termos de melhorar o desempenho em muitos cenários típicos de aplicativos.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Acessar bancos de dados usando mecanismo de espera e repetição com conexões curtas

Se você tiver limitações de recursos, recomendamos fortemente que você use pooling de banco de dados ou conexões persistentes para acessar bancos de dados. Se o aplicativo usar conexões curtas e experimentar falhas de conexão quando você se aproximar do limite superior no número de conexões simultâneas, você pode tentar esperar e tentar novamente o mecanismo. Você pode definir um tempo de espera apropriado, com um tempo de espera menor após a primeira tentativa. Depois disso, você pode tentar esperar por eventos várias vezes.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Configure mecanismos de verificação em clientes para confirmar a eficácia das conexões persistentes

O servidor configura um mecanismo de tempo para fechar uma conexão que está em um estado ocioso há algum tempo para liberar recursos. Quando o cliente acessa o banco de dados novamente, é equivalente a criar uma nova solicitação de conexão entre o cliente e o servidor. Para garantir a eficácia das conexões durante o processo de utilização, configure um mecanismo de verificação no cliente. Como mostrado no exemplo a seguir, você pode usar o pool de conexões Tomcat JDBC para configurar este mecanismo de verificação.

Ao definir o parâmetro TestOnBorrow, quando há uma nova solicitação, o pool de conexões verifica automaticamente a eficácia de quaisquer conexões ociosas disponíveis. Se tal conexão for eficaz, seu pool de conexão retornado diretamente de outra forma retira a conexão. O pool de conexões então cria uma nova conexão eficaz e a devolve. Esse processo garante que o banco de dados seja acessado de forma eficiente. 

Para obter informações sobre as configurações específicas, consulte o documento oficial de introdução do [pool de conexões JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Você precisa definir principalmente os três parâmetros a seguir: TestOnBorrow (definido como true), ValidationQuery (definido como SELECT 1) e ValidationQueryTimeout (definido como 1). O código de amostra específico é mostrado abaixo:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexão no Banco de Dados do Azure para MySQL](howto-troubleshoot-common-connection-issues.md)
