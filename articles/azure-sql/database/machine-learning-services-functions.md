---
title: Gravar funções avançadas de R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Saiba como escrever uma função do R para computação estatística avançada no banco de dados SQL do Azure usando Serviços de Machine Learning (versão prévia).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84035637"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve como inserir funções matemáticas e de utilitário R em um procedimento armazenado do SQL. As funções estatísticas avançadas que são complicadas de implementar no T-SQL podem ser feitas no R com apenas uma única linha de código.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nesses exercícios, primeiro você deve ter o [banco de dados SQL do Azure com serviços de Machine Learning (com R)](machine-learning-services-overview.md) habilitado.

- Verifique se você instalou a versão mais recente do SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)). Você pode executar scripts R usando outras ferramentas de consulta ou gerenciamento de banco de dados, mas neste início rápido, você usará o SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Criar um procedimento armazenado para gerar números aleatórios

Para simplificar, vamos usar o `stats` pacote R que está instalado e carregado por padrão com o banco de dados SQL do Azure usando serviços de Machine Learning (versão prévia). O pacote contém centenas de funções para tarefas estatísticas comuns, entre elas a `rnorm` função. Essa função gera um número especificado de números aleatórios usando a distribuição normal, dado um desvio padrão e significa.

Por exemplo, o código R a seguir retorna 100 números em uma média de 50, dado um desvio padrão de 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Para chamar essa linha de R do T-SQL, execute `sp_execute_external_script` e adicione a função r no parâmetro de script r, desta forma:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

E se você quiser facilitar a geração de um conjunto de números aleatórios diferente?

Isso é fácil quando combinado com o SQL. Você define um procedimento armazenado que obtém os argumentos do usuário e, em seguida, passa esses argumentos para o script R como variáveis.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- A primeira linha define cada um dos parâmetros de entrada SQL necessários quando o procedimento armazenado é executado.

- A linha que começa com `@params` define todas as variáveis usadas pelo código R e os tipos de dados SQL correspondentes.

- As linhas imediatamente após mapeiam os nomes de parâmetro SQL para os nomes de variável R correspondentes.

Agora que você encapsulou a função R em um procedimento armazenado, pode facilmente chamar a função e passar valores diferentes, como este:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Usar funções de utilitário de R para solução de problemas

O `utils` pacote, instalado por padrão, fornece uma variedade de funções de utilitário para investigar o ambiente de R atual. Essas funções podem ser úteis se você estiver encontrando discrepâncias na maneira como o código R é executado no SQL e em ambientes externos. Por exemplo, você pode usar a função R `memory.limit()` para obter memória para o ambiente atual de R.

Uma vez que o pacote `utils` é instalado, mas não carregado por padrão, primeiro é necessário usar a função `library()` para carregá-lo.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Muitos usuários gostam de usar as funções de tempo do sistema em R, como `system.time` e `proc.time` , para capturar o tempo usado pelos processos do r e analisar problemas de desempenho.
