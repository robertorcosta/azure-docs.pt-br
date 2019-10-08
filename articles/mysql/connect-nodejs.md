---
title: Conectar-se ao Banco de Dados do Azure para MySQL no Node.js
description: Este guia de início rápido fornece vários exemplos de código Node.js que podem ser usados para conectar e consultar dados do banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/21/2018
ms.openlocfilehash: 5ad6fecc3cc06d2c4e2962640201ffcd6f96d87e
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672505"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Início Rápido: Usar o Node.js para conectar e consultar dados no Banco de Dados do Azure para MySQL
Este guia de início rápido mostra como se conectar a um banco de dados do Azure para MySQL usando [Node.js](https://nodejs.org/) de plataformas Mac, Ubuntu Linux e Windows. Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. Este tópico pressupõe que você está familiarizado com o desenvolvimento usando Node.js e começou recentemente a trabalhar com o Banco de Dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido usa os recursos criados em um destes guias como ponto de partida:
- [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

Você também precisará:
- Instalar o tempo de execução do [Node.js](https://nodejs.org).
- Instalar o pacote [mysql](https://www.npmjs.com/package/mysql) para se conectar ao MySQL do aplicativo Node.js. 

## <a name="install-nodejs-and-the-mysql-connector"></a>Instalar o conector do MySQL e o Node.js
Dependendo de sua plataforma, siga as instruções na seção apropriada para instalar o Node.js. Use npm para instalar o pacote do mysql e suas dependências na pasta do seu projeto.

### <a name="windows"></a>**Windows**
1. Acesse a [página de downloads do Node. js](https://nodejs.org/en/download/) e selecione a opção do Windows Installer desejada.
2. Crie uma pasta de projeto local, como `nodejsmysql`. 
3. Abra o prompt de comando e, em seguida, altere o diretório na pasta do projeto, como `cd c:\nodejsmysql\`
4. Execute a ferramenta NPM para instalar a biblioteca do mysql na pasta de projeto.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Verifique a instalação conferindo a saída de texto `npm list`. O número de versão pode variar quando novos patches são publicados.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
1. Insira os seguintes comandos para instalar o **Node.js** e o gerenciador de pacotes **npm** para Node.js.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Execute os seguintes comandos para criar uma pasta de projeto `mysqlnodejs` e instale o pacote do mysql nessa pasta.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Verifique a instalação conferindo o texto de saída da lista npm. O número de versão pode variar quando novos patches são publicados.

### <a name="mac-os"></a>**Mac OS**
1. Insira os seguintes comandos para instalar **brew**, um gerenciador de pacotes fácil de usar para Mac OS X e **Node. js**.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Execute os seguintes comandos para criar uma pasta de projeto `mysqlnodejs` e instale o pacote do mysql nessa pasta.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Verifique a instalação conferindo a saída de texto `npm list`. O número de versão pode variar quando novos patches são publicados.

## <a name="get-connection-information"></a>Obter informações de conexão
Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para MySQL. Você precisa das credenciais de logon e do nome do servidor totalmente qualificado.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e pesquise o servidor que você criou (como **mydemoserver**).
3. Selecione o nome do servidor.
4. No painel **Visão Geral** do servidor, anote o **Nome do servidor** e **Nome de logon do administrador do servidor**. Se você esquecer sua senha, também poderá redefini-la nesse painel.
 ![Nome do servidor do Banco de Dados do Azure para MySQL](./media/connect-nodejs/1_server-overview-name-login.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Executar o código JavaScript no Node.js
1. Cole o código JavaScript em arquivos de texto e salve-o em uma pasta de projeto com extensão de arquivo .js (como C:\nodejsmysql\createtable.js ou /home/username/nodejsmysql/createtable.js).
2. Abra o prompt de comando ou shell do Bash e altere o diretório para a pasta do projeto `cd nodejsmysql`.
3. Para executar o aplicativo, insira o comando de nó seguido pelo nome do arquivo, como `node createtable.js`.
4. No Windows, se o aplicativo de nó não está em seu caminho de variável de ambiente, você precisará usar o caminho completo para iniciar o aplicativo de nó, como `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Conectar-se, criar tabela e inserir dados
Use o código a seguir para se conectar e carregar os dados usando as instruções SQL **CREATE TABLE** e **INSERT INTO**.

O método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) é usado como interface com o servidor MySQL. A função [connect()](https://github.com/mysqljs/mysql#establishing-connections) é usada para estabelecer a conexão com o servidor. A função [query ()](https://github.com/mysqljs/mysql#performing-queries) é usada para executar a consulta SQL no banco de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password` e `database` com os valores que você especificou quando criou o servidor e o banco de dados.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>Ler dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**. 

O método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) é usado como interface com o servidor MySQL. O método [connect()](https://github.com/mysqljs/mysql#establishing-connections) é usado para estabelecer a conexão com o servidor. O método [query()](https://github.com/mysqljs/mysql#performing-queries) é usado para executar a consulta SQL no banco de dados MySQL. A matriz de resultados é usada para armazenar os resultados da consulta.

Substitua os parâmetros `host`, `user`, `password` e `database` com os valores que você especificou quando criou o servidor e o banco de dados.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>Atualizar dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **UPDATE**. 

O método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) é usado como interface com o servidor MySQL. O método [connect()](https://github.com/mysqljs/mysql#establishing-connections) é usado para estabelecer a conexão com o servidor. O método [query()](https://github.com/mysqljs/mysql#performing-queries) é usado para executar a consulta SQL no banco de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password` e `database` com os valores que você especificou quando criou o servidor e o banco de dados.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Excluir dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **DELETE**. 

O método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) é usado como interface com o servidor MySQL. O método [connect()](https://github.com/mysqljs/mysql#establishing-connections) é usado para estabelecer a conexão com o servidor. O método [query()](https://github.com/mysqljs/mysql#performing-queries) é usado para executar a consulta SQL no banco de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password` e `database` com os valores que você especificou quando criou o servidor e o banco de dados.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./concepts-migrate-import-export.md)
