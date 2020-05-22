---
title: Conectar e consultar o SQL do Azure no Edge (visualização)
description: Saiba mais sobre como conectar e consultar o SQL do Azure no Edge (visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17bb5afebe0335bcdcf40298490f94999c04a621
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593945"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Conectar e consultar o SQL do Azure no Edge (visualização)

Depois de implantar o contêiner do SQL do Azure no Edge, você poderá se conectar ao mecanismo de banco de dados do SQL a partir de qualquer um dos seguintes locais.

- Dentro do contêiner
- De outro contêiner do Docker em execução no mesmo host.
- Do computador host
- De qualquer outro computador cliente na rede.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Ferramentas para se conectar ao SQL do Azure no Edge

As conexões com uma instância do SQL do Azure no Edge podem ser feitas de qualquer uma das ferramentas comuns mencionadas abaixo.

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) – As ferramentas de cliente do sqlcmd já estão incluídas na imagem de contêiner do SQL do Azure no Edge. Se você anexar a um contêiner em execução com um shell Bash interativo, poderá executar as ferramentas localmente.
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Para se conectar a um mecanismo de banco de dados do SQL do Azure no Edge de um computador da rede, você precisará do seguinte

- *Endereço IP ou nome de rede do computador host* – Esse é o computador host em que o contêiner do SQL do Azure no Edge está em execução.
- *Mapeamento de porta de host de contêiner do SQL do Azure no Edge* – Esse é o mapeamento de porta para a porta de contêiner do Docker para uma porta no host. Dentro do contêiner, o SQL do Azure no Edge é sempre mapeado para a porta 1433. Isso pode ser alterado como parte da implantação do SQL do Azure no Edge. Para alterar o número da porta, atualize as "opções de criação do contêiner" para o módulo do SQL do Azure no Edge no Azure IoT Edge. No exemplo fornecido abaixo, a porta 1433 no contêiner é mapeada para a porta 1600 no host.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- *Senha SA para a instância do SQL do Azure no Edge* – Esse é o valor especificado para a variável de ambiente **SA_PASSWORD** durante a implantação do SQL do Azure no Edge.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Conectando-se ao mecanismo de banco de dados a partir do contêiner

As [ferramentas de linha de comando do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) são incluídas na imagem de contêiner do SQL do Azure no Edge. Se você anexar ao contêiner com um prompt de comando interativo, poderá executar as ferramentas localmente.

1. Use o comando `docker exec -it` para iniciar um shell bash interativo dentro do contêiner em execução. No exemplo `e69e056c702d` a seguir está é a ID do contêiner.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Você nem sempre precisa especificar a toda a ID do contêiner. Você só precisa especificar caracteres suficientes para identificá-la exclusivamente. Portanto, neste exemplo, pode ser suficiente usar `e6` ou `e69`, em vez da ID completa.

2. Quando estiver dentro do contêiner, conecte-se localmente com a sqlcmd. A sqlcmd não está no caminho por padrão, portanto, você precisará especificar o caminho completo.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Quando terminar com o sqlcmd, digite `exit`.

4. Quando terminar com o prompt de comando interativo, digite `exit`. O contêiner continuará a ser executado depois que você sair do shell bash interativo.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Conectar-se ao SQL do Azure no Edge de outro contêiner no mesmo host

Como dois contêineres em execução no mesmo host estão na mesma rede do Docker, eles podem ser acessados facilmente usando o nome do contêiner e o endereço da porta para o serviço. Por exemplo, se você estiver se conectando à instância do SQL do Azure no Edge de outro módulo python (contêiner) no mesmo host, poderá usar uma cadeia de conexão semelhante à seguinte. O exemplo a seguir pressupõe que o SQL do Azure no Edge esteja configurado para escutar na porta padrão.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Conectar-se ao SQL do Azure no Edge de outro computador da rede

Para se conectar à instância do SQL do Azure no Edge de outro computador na rede, você precisará usar o endereço IP do host do Docker e a porta do host para a qual o contêiner do SQL do Azure no Edge está mapeado. Por exemplo, se o endereço IP do host do Docker for *xxx.xxx.xxx.xxx", e o contêiner do SQL do Azure no Edge for mapeado para a porta do host *1600*, o endereço do servidor para a instância do SQL do Azure no Edge será **xxx.xxx.xxx.xxx,1600**. O script do Python atualizado seria

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Para se conectar a uma instância do SQL do Azure no Edge usando SQL Server Management Studio em execução em um computador Windows, consulte [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Para se conectar a uma instância do SQL do Azure no Edge usando o Visual Studio Code em um computador Windows, Mac ou Linux, consulte [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Para se conectar a uma instância do SQL do Azure no Edge usando o Azure Data Studio em um computador Windows, Mac ou Linux, consulte [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Confira também

[Conectar e consultar](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Instalar as ferramentas do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
