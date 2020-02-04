---
title: Criar e executar scripts simples do R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Execute scripts simples do R em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 5b2f8231952d25f5858f8e06a957f1056ecc3651
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768501"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Início Rápido: Criar e executar scripts R simples em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Neste início rápido, você criará e executará um conjunto de scripts do R usando os Serviços do Machine Learning (com R) no Banco de Dados SQL do Azure.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um [Banco de Dados SQL do Azure](sql-database-single-database-get-started.md) com uma [regra de firewall no nível do servidor](sql-database-server-level-firewall-rule.md)
- [Serviços do Machine Learning](sql-database-machine-learning-services-overview.md) com R habilitado. [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).
- SSMS ([SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms))

> [!NOTE]
> Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente.

Este exemplo usa o procedimento armazenado [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) para encapsular um script R bem formado.

## <a name="run-a-simple-script"></a>Executar um script simples

Para executar um script R, você o passará como um argumento para o procedimento armazenado do sistema, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

Nas etapas a seguir, você executará esse script R de exemplo em seu banco de dados SQL:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Abra o **SQL Server Management Studio** e conecte-se ao seu banco de dados SQL.

   Se você precisar de ajuda para se conectar, veja o [Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um Banco de Dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Passar o script R completo para o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   O script é passado por meio do argumento `@script`. Tudo dentro do argumento `@script` precisa ser código R válido.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Se você receber erros, talvez a versão prévia pública dos Serviços de Machine Learning (com R) não esteja habilitada para seu banco de dados SQL. Veja os [Pré-requisitos](#prerequisites) acima.

   > [!NOTE]
   > Se você for um administrador, poderá executar automaticamente o código externo. Você pode conceder permissão a outros usuários usando o comando:
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<nome de usuário\>* .

2. O resultado correto é calculado e a função R `print` retorna o resultado na janela **Mensagens**.

   O resultado deve ser semelhante ao mostrado a seguir.

    **Resultados**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Executar um script de Olá, Mundo

Um script de exemplo típico é aquele que apenas gera a cadeia de caracteres "Olá, Mundo". Execute o comando a seguir.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Entradas para esse procedimento armazenado incluem:

| | |
|-|-|
| @language | define a extensão da linguagem a ser chamada, neste caso, R |
| @script | define os comandos passados para o runtime do R. O seu script R inteiro deve ser colocado neste argumento como texto Unicode. Você também pode adicionar texto a uma variável do tipo **nvarchar** e chamar a variável |
| @input_data_1 | dados retornados pela consulta, passados para o runtime R, que retorna os dados ao SQL Server como uma estrutura de dados |
|WITH RESULT SETS | cláusula que define o esquema da tabela de dados retornada para o SQL Server, adicionando "Olá, Mundo" com o nome da coluna, **int** para o tipo de dados |

O comando gera o seguinte texto:

| Olá, Mundo |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Use entradas e saídas

Por padrão, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) aceita um único conjunto de dados como entrada, que normalmente é fornecido na forma de uma consulta SQL válida. Em seguida, ele retorna uma única estrutura de dados do R como saída.

Por enquanto, examinaremos apenas as variáveis de entrada e saída padrão de [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** e **OutputDataSet**.

1. Crie uma pequena tabela de dados de teste.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Use a instrução `SELECT` para consultar a tabela.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Resultados**

    ![Conteúdo da tabela RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Execute o script R a seguir. Ele recupera os dados da tabela usando a instrução `SELECT`, passa-os pelo runtime do R e retorna os dados como uma estrutura de dados. A cláusula `WITH RESULT SETS` define o esquema da tabela de dados retornada para o Banco de Dados SQL, adicionando o nome de coluna *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Resultados**

    ![Saída do script R que retorna dados de uma tabela](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Agora, vamos alterar os nomes das variáveis de entrada e de saída. Os nomes padrão das variáveis de entrada e de saída são **InputDataSet** e **OutputDataSet**, respectivamente. Este script altera os nomes para **SQL_in** e **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Observe que R diferencia maiúsculas de minúsculas. As variáveis de entrada e saída usadas no script R (**SQL_out**, **SQL_in**) precisam corresponder aos valores definidos com `@input_data_1_name` e `@output_data_1_name`, inclusive no uso de maiúsculas e minúsculas.

   > [!TIP]
   > Só é possível passar um conjunto de dados de entrada como parâmetro, e você pode retornar apenas um conjunto de dados. No entanto, você pode chamar outros conjuntos de dados em seu código R e pode retornar saídas de outros tipos, além do conjunto de dados. Você também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro para que ele retorne com os resultados.

1. Você também pode gerar valores usando apenas o script R sem dados de entrada (`@input_data_1` está definido como em branco).

   O script a seguir gera o texto "Olá" e "mundo".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Resultados**

    ![Resultados da consulta usando @script como entrada](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Verificar a versão do R

Se você quiser ver qual versão de R está instalada no banco de dados SQL, execute o script a seguir.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

A função `print` do R retorna a versão para a janela **Mensagens**. Na saída de exemplo abaixo, veja que o Banco de Dados SQL, neste caso, tem a versão 3.4.4 do R instalada.

**Resultados**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>Listar pacotes do R

A Microsoft fornece alguns pacotes de R pré-instalados com os Serviços de Machine Learning no banco de dados SQL.

Para ver uma lista de quais pacotes do R estão instalados, incluindo informações de versão, dependências, licença e caminho de biblioteca, execute o script a seguir.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

A saída é de `installed.packages()` em R e é retornada como um conjunto de resultados.

**Resultados**

![Pacotes instalados no R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Próximas etapas

Para criar um modelo de machine learning usando o R no Banco de Dados SQL, siga este início rápido:

> [!div class="nextstepaction"]
> [Criar e treinar um modelo preditivo em R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-quickstart-r-train-score-model.md)

Para saber mais sobre os Serviços de Machine Learning do Banco de Dados SQL do Azure com R (versão prévia), confira os seguintes artigos.

- [Serviços de Machine Learning do Banco de Dados SQL do Azure com R (versão prévia)](sql-database-machine-learning-services-overview.md)
- [Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-functions.md)
- [Trabalhar usando dados do R e do SQL nos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-data-issues.md)
