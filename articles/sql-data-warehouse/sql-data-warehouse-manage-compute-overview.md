---
title: Gerenciar recurso de computação
description: Saiba mais sobre os recursos de escala horizontal de desempenho no SQL Data Warehouse do Azure. Escalar horizontalmente ajustando DWUs, ou reduzir os custos pausando o data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/12/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 936d92d085420e1386e29a924470b9bac9200d43
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039093"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Gerenciar computação no SQL Data Warehouse do Azure
Saiba mais sobre como gerenciar recursos de computação no SQL Data Warehouse do Azure. Reduzir os custos pausando o data warehouse, ou dimensionar o data warehouse para atender às demandas de desempenho. 

## <a name="what-is-compute-management"></a>O que é o gerenciamento de computação?
A arquitetura do SQL Data Warehouse separa armazenamento e computação, permitindo que cada um seja dimensionado independentemente. Como resultado, é possível dimensionar o cálculo para atender às demandas de desempenho independentes do armazenamento de dados. Além disso, você também pode pausar e retomar os recursos de computação. Uma consequência natural dessa arquitetura é que a [cobrança](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) pela computação e pelo armazenamento é separada. Se não for necessário usar o data warehouse por um tempo, você poderá economizar os custos de computação, pausando a computação. 

## <a name="scaling-compute"></a>Dimensionar computação
É possível escalar horizontalmente ou voltar a escala de computação, ajustando a configuração [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) para seu data warehouse. O desempenho de consultas e carregamento pode aumentar linearmente na medida em que você adicionar mais unidades de data warehouse. 

Para etapas de escala horizontal, consulte os inícios rápidos do [Portal do Azure](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md) ou [T-SQL](quickstart-scale-compute-tsql.md). Também é possível executar operações de escala horizontal com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Para executar uma operação de escala, o SQL Data Warehouse primeiro encerra todas as consultas de entrada e, em seguida, reverte as transações para garantir um estado consistente. A escala ocorrerá somente depois que a reversão de transação estiver completa. Para uma operação de escala, o sistema desanexa a camada de armazenamento dos nós de Computação, adiciona nós de Computação e, em seguida, reanexa a camada de armazenamento na camada de Computação. Cada data warehouse é armazenado como 60 distribuições que são distribuídas uniformemente para os nós de Computação. Adicionar mais nós de Computação, adiciona mais potência de computação. À medida que o número de nós de Computação aumenta, o número de distribuições por nó de computação diminui, proporcionando mais potência de computação para suas consultas. Da mesma forma, diminuindo as unidades de data warehouse reduzirá o número de nós de Computação, o que reduz os recursos de computação para consultas.

A tabela a seguir mostra como o número de distribuições por nó de Computação altera na medida em que as unidades do data warehouse mudam.  O DWU6000 fornece 60 nós de Computação e alcança um desempenho de consultas muito maior que o DWU100. 

| Unidades de data warehouse  | \# de nós de Computação | \# de distribuições por nó |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Localizar o tamanho correto das unidades de data warehouse

Para ver os benefícios de desempenho de escalamento horizontal, especialmente para unidades de data warehouse grandes, convém usar pelo menos um conjunto de dados de 1 TB. Para localizar o melhor número de unidades de data warehouse para seu data warehouse, tente escalar e reduzir verticalmente. Execute algumas consultas com diferentes números de unidades de data warehouse após o carregamento dos dados. Como o dimensionamento é rápido, você pode experimentar vários níveis de desempenho diferentes durante uma hora ou menos. 

Recomendações para localizar o melhor número de unidades de data warehouse:

- Para um data warehouse em desenvolvimento, comece selecionando um número menor de unidades de data warehouse.  Um bom ponto de partida é DW400 ou DW200.
- Monitore o desempenho do aplicativo, observando o número de unidades de data warehouse selecionadas em comparação com o desempenho observado.
- Suponha uma escala linear e determine quanto é necessário para aumentar ou diminuir as unidades do data warehouse. 
- Continue fazendo ajustes até alcançar um nível de desempenho ideal para seus requisitos de negócios.

## <a name="when-to-scale-out"></a>Quando escalar horizontalmente
Escalar dimensionalmente unidades de data warehouse afeta os seguintes aspectos do desempenho:

- Melhora o desempenho linear do sistema para exames, agregações e instruções de CTAS.
- Aumenta o número de leitores e gravadores para carregamento de dados.
- Número máximo de consultas simultâneas e slots de simultaneidade.

Recomendações para quando escalar horizontalmente unidades de data warehouse:

- Antes de executar uma operação de transformação ou carregamento de dados pesados, escale horizontalmente para tornar os dados disponíveis mais rapidamente.
- Durante o horário comercial de pico, escale dimensionalmente para acomodar um número de consultas simultâneas maior. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>E se escalar verticalmente não melhorar o desempenho?

Adicionar unidades de data warehouse aumentando o paralelismo. Se o trabalho for dividido uniformemente entre os nós de Computação, o paralelismo adicional irá melhorar o desempenho de consultas. Se ao escalar verticalmente o desempenho não melhorar, há alguns motivos pelos quais isso pode acontecer. Os dados podem estar distorcidos nas distribuições ou as consultas podem estar introduzindo uma grande quantidade de movimentação de dados. Para investigar os problemas de desempenho de consultas, consulte [ Solução de problemas de desempenho](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pausa e retomada de computação
Pausar a computação faz a camada de armazenamento desanexar dos nós de Computação. Os recursos de Computação são liberados da sua conta. Você não será cobrado pela computação enquanto a computação estiver em pausa. Retomar a computação reanexa o armazenamento nos nós de Computação e retoma as cobranças de Computação. Ao pausar um data warehouse:

* Recursos de computação e memória são retornados ao pool de recursos disponíveis no data center
* Os custos da unidade de data warehouse serão nulos durante a pausa.
* O armazenamento de dados não é afetado e seus dados permanecem intactos. 
* O SQL Data Warehouse cancela todas as operações em execução ou em fila.

Ao retomar um data warehouse:

* O SQL Data Warehouse adquire recursos de memória e computação para configuração das unidades de data warehouse.
* Os encargos de computação para as unidades de data warehouse são retomados.
* Os dados tornam-se disponíveis.
* Depois que o data warehouse estiver online, você deverá reiniciar as consultas de carga de trabalho.

Se você quiser que o data warehouse esteja sempre acessível, convém dimensioná-lo para o menor tamanho em vez de pausar. 

Para etapas de pausa e retomada, consulte os inícios rápidos do [Portal do Azure](pause-and-resume-compute-portal.md) ou [PowerShell](pause-and-resume-compute-powershell.md). Também é possível usar a [API REST de pausa](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) ou a [API REST de retomada](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Esvaziar as transações antes de pausar ou dimensionar
É recomendável que as transações existentes sejam concluídas antes de iniciar uma operação de pausa ou de escala.

Ao pausar ou dimensionar o SQL Data Warehouse, nos bastidores, suas consultas são canceladas quando você inicia a pausa ou dimensionar a solicitação.  Cancelar uma simples consulta SELECT é uma operação rápida e quase não terá impacto sobre o tempo que leva para pausar ou dimensionar sua instância.  No entanto, as consultas transacionais, que modificam os dados ou a estrutura dos dados, não conseguirão parar rapidamente.  **As consultas transacionais, por definição, devem ser concluídas na íntegra ou reverter suas alterações.**  Reverter o trabalho concluído por uma consulta transacional pode levar tanto tempo, ou até mais, quanto a alteração original que a consulta estava aplicando.  Por exemplo, se você cancelar uma consulta que estava excluindo linhas que já estava em execução por uma hora, poderá levar uma hora para o sistema inserir de volta as linhas que foram excluídas.  Se você executar a pausa ou o dimensionamento enquanto as transações estiverem em andamento, a pausa ou o dimensionamento poderão demorar muito tempo porque eles têm que esperar a reversão ser concluída antes de prosseguir.

Consulte também [Noções básicas sobre transações](sql-data-warehouse-develop-transactions.md) e [Otimizar transações](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizar o gerenciamento de computação
Para automatizar as operações de gerenciamento de computação, consulte [Gerenciar computação com o Azure Functions](manage-compute-with-azure-functions.md).

Cada operação de escala horizontal, pausa e retomada pode demorar vários minutos para ser concluída. Se você está escalando, pausando ou retomando automaticamente, é recomendável implementar a lógica para assegurar que determinadas operações tenham sido concluídas antes de prosseguir com outra ação. Verificar o estado do data warehouse através de vários pontos de extremidades permite que você implemente corretamente a automação dessas operações. 

Para verificar o estado do data warehouse, consulte o início rápido do [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) ou [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state). Você também pode verificar o estado do data warehouse com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>permissões

Escalar o data warehouse exige as permissões descritas em [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Pausar e Retomar exige a permissão [Contribuidor do DB SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor), especificamente Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Próximas etapas
Consulte o guia de instruções para [gerenciar computação](manage-compute-with-azure-functions.md) outro aspecto do gerenciamento de recursos de computação que está alocando diferentes recursos de computação para consultas individuais. Para obter mais informações, consulte [Classes de recurso para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
