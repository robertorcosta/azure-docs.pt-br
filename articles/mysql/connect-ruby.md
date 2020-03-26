---
title: Conectar-se usando Ruby – Banco de Dados do Azure para MySQL
description: Este guia de início rápido fornece vários exemplos de código Ruby que podem ser usados para conectar e consultar dados do banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 12/02/2019
ms.openlocfilehash: dc8c7352856b11cb6cc4c9c404eb567cb72b720d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74770638"
---
# <a name="azure-database-for-mysql-use-ruby-to-connect-and-query-data"></a>Banco de dados do Azure para MySQL: como usar Ruby para conectar e consultar dados
Este guia de início rápido mostra como se conectar a um banco de dados do Azure para MySQL usando um aplicativo [Ruby](https://www.ruby-lang.org) e o [mysql2](https://rubygems.org/gems/mysql2) gem de plataformas Mac, Ubuntu Linux e Windows. Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. Este tópico pressupõe que você está familiarizado com o desenvolvimento usando Ruby e começou recentemente a trabalhar com o Banco de Dados do Azure para MySQL.

## <a name="prerequisites"></a>Prerequisites
Este guia de início rápido usa os recursos criados em um destes guias como ponto de partida:
- [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-ruby"></a>Instalar Ruby
Instale o Ruby, o Gem e a biblioteca MySQL2 no seu computador. 

### <a name="windows"></a>Windows
1. Baixe e instale a versão 2.3 do [Ruby](https://rubyinstaller.org/downloads/).
2. Inicie um novo prompt de comando (cmd) no menu Iniciar.
3. Altere o diretório para a versão 2.3 do diretório Ruby. `cd c:\Ruby23-x64\bin`
4. Teste a instalação do Ruby executando o comando `ruby -v` para ver a versão instalada.
5. Teste a instalação do Gem executando o comando `gem -v` para ver a versão instalada.
6. Compile o módulo Mysql2 para Ruby usando o Gem com a execução do comando `gem install mysql2`.

### <a name="macos"></a>MacOS
1. Instale o Ruby usando Homebrew, executando o comando `brew install ruby`. Para obter mais opções de instalação, consulte a [documentação da instalação](https://www.ruby-lang.org/en/documentation/installation/#homebrew) do Ruby
2. Teste a instalação do Ruby executando o comando `ruby -v` para ver a versão instalada.
3. Teste a instalação do Gem executando o comando `gem -v` para ver a versão instalada.
4. Compile o módulo Mysql2 para Ruby usando o Gem com a execução do comando `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Instale o Ruby executando o comando `sudo apt-get install ruby-full`. Para obter mais opções de instalação, consulte a [documentação da instalação](https://www.ruby-lang.org/en/documentation/installation/) do Ruby
2. Teste a instalação do Ruby executando o comando `ruby -v` para ver a versão instalada.
3. Instale as atualizações mais recentes do Gem executando o comando `sudo gem update --system`.
4. Teste a instalação do Gem executando o comando `gem -v` para ver a versão instalada.
5. Instale gcc, make e outras ferramentas de compilação executando o comando `sudo apt-get install build-essential`.
6. Instale as bibliotecas de desenvolvedor de cliente do MySQL, executando o comando `sudo apt-get install libmysqlclient-dev`.
7. Compile o módulo mysql2 para Ruby usando o Gem com a execução do comando `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Obter informações de conexão
Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para MySQL. Você precisa das credenciais de logon e do nome do servidor totalmente qualificado.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no portal do Azure, clique em **Todos os recursos** e pesquise o servidor que você criou (como **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Visão Geral** do servidor, anote o **Nome do servidor** e **Nome de logon do administrador do servidor**. Se você esquecer sua senha, também poderá redefini-la nesse painel.
 ![Nome do servidor do Banco de Dados do Azure para MySQL](./media/connect-ruby/1_server-overview-name-login.png)

## <a name="run-ruby-code"></a>Executar código Ruby 
1. Cole o código Ruby das seções abaixo em arquivos de texto e salve-os em uma pasta de projeto com a extensão de arquivo .rb (como `C:\rubymysql\createtable.rb` ou `/home/username/rubymysql/createtable.rb`).
2. Para executar o código, inicie o prompt de comando ou o shell Bash. Altere o diretório para a pasta do seu projeto `cd rubymysql`
3. Digite o comando Ruby seguido pelo nome do arquivo, como `ruby createtable.rb`, para executar o aplicativo.
4. No sistema operacional Windows, se o aplicativo Ruby não estiver no seu caminho de variável de ambiente, você precisará usar o caminho completo para iniciar o aplicativo de nó, como `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Conectar-se e criar uma tabela
Use o código a seguir para se conectar e criar uma tabela usando a instrução SQL **CREATE TABLE**, seguida por instruções SQL **INSERT INTO** para adicionar linhas à tabela.

O código usa um método .new() de classe [mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) para se conectar ao banco de dados do Azure para MySQL. Em seguida, ele chama o método [query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) várias vezes para executar os comandos DROP, CREATE TABLE e INSERT INTO. Em seguida, ele chama o método [close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para fechar a conexão antes de encerrar.

Substitua as cadeias de caracteres `host`, `database`, `username` e `password` pelos seus próprios valores. 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Ler dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**. 

O código usa um método .new() de classe [mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) para se conectar ao Banco de Dados do Azure para MySQL. Em seguida, ele chama o método [query ()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para executar os comandos SELECT. Em seguida, ele chama o método [close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para fechar a conexão antes de encerrar.

Substitua as cadeias de caracteres `host`, `database`, `username` e `password` pelos seus próprios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Atualizar dados
Use o código a seguir para conectar-se e atualizar os dados usando uma instrução SQL **UPDATE**.

O código usa um método .new() de classe [mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) para se conectar ao banco de dados do Azure para MySQL. Em seguida, ele chama o método [query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para executar os comandos UPDATE. Em seguida, ele chama o método [close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para fechar a conexão antes de encerrar.

Substitua as cadeias de caracteres `host`, `database`, `username` e `password` pelos seus próprios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Excluir dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **DELETE**. 

O código usa um método .new() de classe [mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) para se conectar ao banco de dados do Azure para MySQL. Em seguida, ele chama o método [query ()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para executar os comandos DELETE. Em seguida, ele chama o método [close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para fechar a conexão antes de encerrar.

Substitua as cadeias de caracteres `host`, `database`, `username` e `password` pelos seus próprios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./concepts-migrate-import-export.md)
