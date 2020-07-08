---
title: Transações distribuídas entre bancos de dados na nuvem
description: Visão geral das transações de banco de dados elástico com o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5c94234644fcefb70a40ba0b2c21e6e205be0e65
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829407"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Transações distribuídas entre bancos de dados na nuvem
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

As transações de banco de dados elástico para o banco de dados SQL do Azure permitem que você execute transações que abrangem vários bancos de dados no banco de dados SQL. As transações de banco de dados elástico para o banco de dados SQL estão disponíveis para aplicativos .NET usando ADO .NET e se integram à experiência de programação familiar usando as classes [System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Para obter a biblioteca, confira [.NET Framework 4.6.1 (Web Installer)](https://www.microsoft.com/download/details.aspx?id=49981).

No local, um cenário como esse geralmente requer a execução do Microsoft Coordenador de Transações Distribuídas (MSDTC). Como o MSDTC não está disponível para aplicativos de plataforma como serviço no Azure, a capacidade de coordenar transações distribuídas agora foi diretamente integrada ao banco de dados SQL. Os aplicativos podem se conectar a qualquer banco de dados no banco de dados SQL para iniciar transações distribuídas, e um dos banco de dados coordenará de forma transparente a transação distribuída, conforme mostrado na figura a seguir.

  ![Transações distribuídas com o Banco de Dados SQL do Azure usando transações de banco de dados elástico ][1]

## <a name="common-scenarios"></a>Cenários comuns

As transações de banco de dados elástico para o banco de dados SQL permitem que os aplicativos façam alterações atômicas nos dados armazenados em vários bancos diferentes, no banco de dados SQL. A visualização se concentra nas experiências de desenvolvimento do lado do cliente em C# e .NET. Há planos para uma experiência do lado do servidor usando o T-SQL para um momento posterior.  
As transações de banco de dados elástico visam os seguintes cenários:

* Aplicativos com vários bancos de dados no Azure: com esse cenário, os dados são particionados verticalmente em vários bancos de dados no banco de dados SQL, de modo que os diferentes tipos de dado residam em bancos diferentes. Algumas operações exigem alterações nos dados, que são mantidas em dois ou mais bancos de dado. O aplicativo usa transações de banco de dados elástico para coordenar as alterações nos bancos de dados e garantir a atomicidade.
* Aplicativos de banco de dados fragmentados no Azure: com esse cenário, a camada data usa a [biblioteca de cliente do banco](elastic-database-client-library.md) de dado elástico ou a autofragmentação para particionar horizontalmente os dados em vários bancos de dado no banco de dados SQL. Um caso de uso importante é a necessidade de realizar mudanças atômicas em um aplicativo multilocatário fragmentado quando as alterações se estendem aos locatários. Pense, por exemplo, em uma transferência de um locatário para outro, ambos residentes em bancos de dados diferentes. Um segundo caso é a fragmentação refinada para acomodar as necessidades de capacidade para um locatário grande, que, por sua vez, normalmente implica que algumas operações atômicas precisam ser ampliadas em vários bancos de dados usados para o mesmo locatário. Um terceiro caso são as atualizações atômicas de dados de referência que são replicados nos bancos de dados. As operações atômicas e transacionadas, juntamente com estas linhas, agora podem ser coordenadas em vários bancos de dados usando a visualização.
  As transações de banco de dados elástico usam o protocolo 2PC para garantir a atomicidade das transações nos bancos de dados. É uma boa opção para transações que envolvem menos de 100 bancos de dados por vez em uma única transação. Esses limites não são impostos, mas um deve esperar taxas de desempenho e êxito para que as transações de banco de dados elástico sejam prejudicadas ao exceder esses limites.

## <a name="installation-and-migration"></a>Instalação e migração

Os recursos para transações de banco de dados elástico no banco de dados SQL são fornecidos por meio de atualizações para as bibliotecas .NET System.Data.dll e System.Transactions.dll. As DLLs garantem que o protocolo 2PC seja usado quando necessário para garantir a atomicidade. Para começar a desenvolver aplicativos usando as transações de banco de dados elástico, instale o [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Quando executadas em uma versão anterior do .NET Framework, as transações não serão promovidas a uma transação distribuída, e uma exceção será gerada.

Após a instalação, você pode usar as APIs de transação distribuída no System. Transactions com conexões com o banco de dados SQL. Se você já tiver aplicativos do MSDTC usando essas APIs, basta recompilar os aplicativos existentes para o .NET 4.6 depois de instalar o .NET Framework 4.6.1. Se seus projetos tiverem como destino o .NET 4,6, eles usarão automaticamente as DLLs atualizadas da nova versão da estrutura e as chamadas da API de transação distribuída em combinação com as conexões com o banco de dados SQL agora serão bem sucedidos.

Lembre-se de que transações de banco de dados elástico não exigem a instalação do MSDTC. Em vez disso, as transações de banco de dados elástico são gerenciadas diretamente pelo e no banco de dados SQL. Isso simplifica significativamente os cenários de nuvem, já que uma implantação do MSDTC não é necessária para usar transações distribuídas com o banco de dados SQL. A Seção 4 explica detalhadamente como implantar as transações de banco de dados elástico e o .NET Framework necessário juntamente com seus aplicativos em nuvem no Azure.

## <a name="development-experience"></a>Experiência de desenvolvimento

### <a name="multi-database-applications"></a>Aplicativos de vários bancos de dados

O exemplo de código a seguir usa a experiência de programação conhecida com o .NET System.Transactions. A classe TransactionScope estabelece uma transação de ambiente no .NET. (Uma "transação de ambiente" é aquela que reside no thread atual.) Todas as conexões abertas no TransactionScope participam da transação. Se diferentes bancos de dados participarem, a transação será elevada automaticamente a uma transação distribuída. O resultado da transação é controlado pela definição do escopo a concluir para indicar uma confirmação.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Aplicativos de banco de dados fragmentado

As transações de banco de dados elástico para o banco de dados SQL também oferecem suporte à coordenação de transações distribuídas, em que você usa o método OpenConnectionForKey da biblioteca de cliente do banco de dados elástico para abrir conexões para uma camada expandida. Considere os casos em que você precisa garantir a consistência transacional das alterações em vários valores-chave de fragmentação diferentes. As conexões com os fragmentos que hospedam os diferentes valores-chave de fragmentação são intermediadas usando o OpenConnectionForKey. Em geral, as conexões podem ocorrer para diferentes fragmentos, de modo que para assegurar as garantias transacionais seja necessária uma transação distribuída.
O exemplo de código a seguir ilustra essa abordagem. Ele pressupõe que uma variável chamada shardmap seja usada para representar um mapa do fragmento na biblioteca do cliente de banco de dados elástico:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="net-installation-for-azure-cloud-services"></a>Instalação do .NET para os Serviços de Nuvem do Azure

O Azure fornece várias ofertas para hospedar aplicativos .NET. Uma comparação entre as diferentes ofertas está disponível em [Comparação entre o Serviço de Aplicativo do Azure, os Serviços de Nuvem e as Máquinas Virtuais](/azure/architecture/guide/technology-choices/compute-decision-tree). Se o SO convidado da oferta for inferior ao .NET 4.6.1 exigido para transações elásticas, será necessário atualizar o SO para 4.6.1.

Para Azure App serviço, não há suporte para atualizações para o SO convidado no momento. Para as Máquinas virtuais do Azure, basta fazer logon na VM e executar o instalador do .NET Framework mais recente. Para os Serviços de Nuvem do Azure, você precisará incluir a instalação de uma versão mais recente do .NET em tarefas de inicialização da sua implantação. Os conceitos e as etapas estão documentados em [Instalar o .NET em uma Função do Serviço de Nuvem](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observe que o instalador do .NET 4.6.1 pode exigir mais armazenamento temporário durante o processo de inicialização nos Serviços de Nuvem do Azure que o instalador do .NET 4.6. Para garantir uma instalação bem-sucedida, você precisa aumentar o armazenamento temporário para o serviço de nuvem do Azure no seu arquivo ServiceDefinition.csdef na seção LocalResources e nas configurações do ambiente de sua tarefa de inicialização, conforme mostrado no exemplo a seguir:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="transactions-across-multiple-servers"></a>Transações entre vários servidores

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As transações de banco de dados elástico têm suporte em diferentes servidores no banco de dados SQL do Azure. Quando as transações cruzam os limites do servidor, os servidores participantes precisam primeiro ser inseridos em uma relação de comunicação mútua. Após a relação de comunicação ser estabelecida, qualquer banco de dados em qualquer um dos dois servidores poderá participar de transações elásticas com bancos de dados do outro servidor. Com transações que abrangem mais de dois servidores, uma relação de comunicação precisa estar em vigor para qualquer par de servidores.

Use os cmdlets do PowerShell a seguir para gerenciar as relações de comunicação entre servidores para transações de Banco de Dados Elástico:

* **New-AzSqlServerCommunicationLink**: Use este cmdlet para criar uma nova relação de comunicação entre dois servidores no banco de dados SQL do Azure. A relação é simétrica, o que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzSqlServerCommunicationLink**: Use este cmdlet para recuperar relações de comunicação existentes e suas propriedades.
* **Remove-AzSqlServerCommunicationLink**: Use este cmdlet para remover uma relação de comunicação existente.

## <a name="monitoring-transaction-status"></a>Monitorando o status da transação

Use DMVs (exibições de gerenciamento dinâmico) no banco de dados SQL para monitorar o status e o progresso de suas transações de banco de dados elástico contínuas. Todas as DMVs relacionadas a transações são relevantes para transações distribuídas no banco de dados SQL. Você pode encontrar a lista correspondente de DMVs aqui: [Funções e exibições de gerenciamento dinâmico relacionadas a transações (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Estas DMVs são especialmente úteis:

* **sys.dm\_tran\_active\_transactions**: lista as transações atualmente ativas e seu status. A coluna UOW (Unidade de Trabalho) pode identificar as diferentes transações filho que pertencem à mesma transação distribuída. Todas as transações dentro da mesma transação distribuída carregam o mesmo valor UOW. Para saber mais, confira a [documentação do DMV](https://msdn.microsoft.com/library/ms174302.aspx).
* **sys.dm\_tran\_database\_transactions**: fornece informações adicionais sobre as transações, como a colocação da transação no log. Para saber mais, confira a [documentação do DMV](https://msdn.microsoft.com/library/ms186957.aspx).
* **sys.dm\_tran\_locks**: fornece informações sobre os bloqueios que são atualmente mantidos por transações em andamento. Para saber mais, confira a [documentação do DMV](https://msdn.microsoft.com/library/ms190345.aspx).

## <a name="limitations"></a>Limitações

As seguintes limitações atualmente se aplicam a transações de banco de dados elástico no banco de dados SQL:

* Há suporte apenas para transações entre bancos de dados no SQL Database. Outros provedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bancos de dados fora do banco de dados SQL não podem participar de transações de banco de dados elástico. Isso significa que as transações de banco de dados elástico não podem ser ampliadas em SQL Server locais e no banco de dados SQL do Azure. Para transações distribuídas no local, continue a usar o MSDTC.
* Há suporte somente para transações coordenadas pelo cliente a partir de um aplicativo .NET. Há planos para suporte do lado do servidor para o T-SQL, como INICIAR TRANSAÇÃO DISTRIBUÍDA, mas ainda não está disponível.
* Não há suporte para transações entre serviços WCF. Por exemplo, você tem um método de serviço WCF que executa uma transação. Colocar a chamada dentro de um escopo de transação falhará como [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Próximas etapas

Para dúvidas, entre em contato conosco no [Microsoft Q&uma página de perguntas para o banco de dados SQL](https://docs.microsoft.com/answers/topics/azure-sql-database.html). Para solicitações de recursos, adicione-as ao [Fórum de comentários do banco de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
 