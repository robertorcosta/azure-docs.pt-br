---
title: Gravar funções avançadas de R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Aprenda a escrever uma função R para computação estatística avançada no Banco de Dados Azure SQL usando Serviços de Aprendizagem de Máquina (visualização).
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
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60702445"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Este artigo descreve como incorporar funções matemáticas e de utilidade R em um procedimento armazenado em SQL. As funções estatísticas avançadas que são complicadas de implementar no T-SQL podem ser feitas no R com apenas uma única linha de código.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nestes exercícios, primeiro você precisa ter um Banco de dados SQL do Azure com os Serviços do Machine Learning (com R) habilitados. Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

- Verifique se você instalou a versão mais recente do SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)). Você pode executar scripts R usando outras ferramentas de consulta ou gerenciamento de banco de dados, mas neste início rápido, você usará o SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Criar um procedimento armazenado para gerar números aleatórios

Para simplificar, vamos usar o `stats` pacote R instalado e carregado por padrão com o Banco de Dados SQL do Azure usando Serviços de Aprendizagem de Máquina (visualização). O pacote contém centenas de funções para `rnorm` tarefas estatísticas comuns, entre elas a função. Esta função gera um número especificado de números aleatórios usando a distribuição normal, dado um desvio padrão e meios.

Por exemplo, o código R a seguir retorna 100 números em uma média de 50, dado um desvio padrão de 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Para chamar esta linha de R de `sp_execute_external_script` T-SQL, execute e adicione a função R no parâmetro r script, assim:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

E se você quiser facilitar a geração de um conjunto de números aleatórios diferente?

Isso é fácil quando combinado com SQL. Você define um procedimento armazenado que obtém os argumentos do usuário e, em seguida, passa esses argumentos para o script R como variáveis.

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

O `utils` pacote, instalado por padrão, fornece uma variedade de funções de utilidade para investigar o ambiente R atual. Essas funções podem ser úteis se você estiver encontrando discrepâncias na forma como seu código R é executado em SQL e em ambientes externos. Por exemplo, você pode usar a função R `memory.limit()` para obter memória para o ambiente atual de R.

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
> Muitos usuários gostam de usar as funções `system.time` de `proc.time`tempo do sistema em R, como e , para capturar o tempo usado pelos processos R e analisar problemas de desempenho.
