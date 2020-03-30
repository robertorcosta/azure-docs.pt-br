---
title: 'Tutorial: como migrar seu banco de dados SQLite para o Azure SQL Database Serverless'
description: Aprenda a executar uma migração off-line do SQLite para o Azure SQL Database Serverless usando o Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780503"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Como migrar seu banco de dados SQLite para o Azure SQL Database Serverless
Para muitas pessoas, o SQLite oferece sua primeira experiência de bancos de dados e programação SQL. Sua inclusão em muitos sistemas operacionais e aplicativos populares faz do SQLite um dos motores de banco de dados mais amplamente implantados e usados no mundo. E como é provável que seja o primeiro mecanismo de banco de dados que muitas pessoas usam, muitas vezes pode acabar como uma parte central de projetos ou aplicativos. Nesses casos em que o projeto ou aplicativo supera a implementação inicial do SQLite, os desenvolvedores podem precisar migrar seus dados para um armazenamento de dados confiável e centralizado.

O Azure SQL Database serverless é um nível de computação para bancos de dados únicos que dimensiona automaticamente a computação com base na demanda de carga de trabalho e fatura a quantidade de cálculo usada por segundo. O nível de computação sem servidor também pausa automaticamente os bancos de dados durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

Depois de seguir as etapas abaixo, seu banco de dados será migrado para o Azure SQL Database Serverless, permitindo que você disponibilize seu banco de dados para outros usuários ou aplicativos na nuvem e pague apenas pelo que você usa, com alterações mínimas no código do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure
- Banco de dados SQLite2 ou SQLite3 que você deseja migrar
- Um ambiente Windows
  - Se você não tiver um ambiente windows local, você pode usar uma VM do Windows no Azure para a migração. Mova e disponibilize seu arquivo de banco de dados SQLite na VM usando o Azure Files e o Storage Explorer.

## <a name="steps"></a>Etapas

1. Provisionar um novo Banco de Dados SQL do Azure no nível de computação sem servidor.

    ![captura de tela do portal Azure mostrando exemplo de provisionamento para banco de dados azure sql sem servidor](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Certifique-se de ter seu arquivo de banco de dados SQLite disponível em seu ambiente Windows. Instale um Driver SQLite ODBC se você ainda não tiver um (há muitos disponíveis no Open Source, por exemplo, http://www.ch-werner.de/sqliteodbc/).

3. Crie um Sistema DSN para o banco de dados. Certifique-se de usar o aplicativo Data Source Administrator que corresponde à arquitetura do sistema (32 bits vs 64 bits). Você pode encontrar qual versão você está executando nas configurações do sistema.

    - Abra o Administrador de Origem de Dados ODBC em seu ambiente.
    - Clique na guia DSN do sistema e clique em "Adicionar"
    - Selecione o conector SQLite ODBC que você instalou e dê à conexão um nome significativo, por exemplo, sqlitemigrationsource
    - Defina o nome do banco de dados como o arquivo .db
    - Salvar e sair

4. Baixe e instale o tempo de execução de integração auto-hospedado. A maneira mais fácil de fazer isso é a opção de instalação do Expresso, conforme detalhado na documentação. Se você optar por uma instalação manual, você precisará fornecer ao aplicativo uma chave de autenticação, que pode ser localizada na sua instância de Fábrica de Dados por:

    - Iniciando o ADF (Autor e Monitor do serviço no portal Azure)
    - Clique na guia "Autor" (lápis azul) à esquerda
    - Clique em Conexões (inferior esquerdo) e, em seguida, tempos de execução de integração
    - Adicione o novo tempo de execução de integração auto-hospedado, dê-lhe um nome, selecione *Opção 2*.

5. Crie um novo serviço vinculado para o banco de dados SQLite de origem em sua Fábrica de Dados.

    ![captura de tela mostrando lâmina de serviços vinculados vazios na Fábrica de Dados Azure](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. Em Conexões, em Linked Service, clique em Novo

7. Procure e selecione o conector "ODBC"


    ![captura de tela mostrando o logotipo do conector ODBC na lâmina de serviços vinculados na Fábrica de Dados Azure](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Dê ao serviço vinculado um nome significativo, por exemplo, "sqlite_odbc". Selecione o tempo de execução da integração no downdown "Conectar via tempo de execução de integração". Digite o abaixo na seqüência de conexões, substituindo a variável Catálogo Inicial pelo filepath para o arquivo .db e o DSN com o nome da conexão DSN do sistema: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Defina o tipo de autenticação como Anônimo

10. Testar a conexão

    ![captura de tela mostrando conexão bem sucedida na Fábrica de Dados Do Azure](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Crie outro serviço vinculado para seu destino SQL sem servidor. Selecione o banco de dados usando o assistente de serviço vinculado e forneça as credenciais de autenticação SQL.

    ![captura de tela mostrando o Banco de Dados SQL do Azure selecionado na Fábrica de Dados do Azure](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Extrair as instruções CREATE TABLE do seu banco de dados SQLite. Você pode fazer isso executando o script python abaixo em seu arquivo de banco de dados.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Crie as tabelas de aterrissagem no ambiente de destino SQL sem servidor copiando as instruções de tabela CREATE do arquivo CreateTables.sql e executando as instruções SQL no Editor de Consulta no portal Azure.

14. Retorne à tela inicial da sua Fábrica de Dados e clique em "Copiar dados" para executar através do assistente de criação de empregos.

    ![captura de tela mostrando o logotipo do assistente de dados de cópia na fábrica de dados do Azure](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Selecione todas as tabelas do banco de dados SQLite de origem usando as caixas de seleção e mapeie-as para as tabelas de destino no Azure SQL. Uma vez executado o trabalho, você migrou com sucesso seus dados do SQLite para o Azure SQL!

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Quickstart: Crie um único banco de dados no Banco de Dados SQL do Azure usando o portal Azure](sql-database-single-database-get-started.md).
- Para limites de recursos, consulte [Limites de recursos de camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
