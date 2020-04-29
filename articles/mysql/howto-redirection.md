---
title: Conectar-se ao redirecionamento-banco de dados do Azure para MySQL
description: Este artigo descreve como você pode configurar seu aplicativo para se conectar ao banco de dados do Azure para MySQL com redirecionamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246316"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Conectar-se ao banco de dados do Azure para MySQL com redirecionamento

Este tópico explica como conectar um aplicativo ao servidor do banco de dados do Azure para MySQL com o modo de redirecionamento. O redirecionamento visa reduzir a latência de rede entre os aplicativos cliente e os servidores MySQL, permitindo que os aplicativos se conectem diretamente aos nós do servidor back-end.

## <a name="before-you-begin"></a>Antes de começar
Entre no [portal do Azure](https://portal.azure.com). Crie um servidor de banco de dados do Azure para MySQL com o mecanismo versão 5,6, 5,7 ou 8,0. Para obter detalhes, confira [Como criar o Banco de Dados do Azure para servidor MySQL por meio do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou [Como criar o Banco de Dados do Azure para servidor MySQL usando a CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

No momento, só há suporte para o redirecionamento quando o **SSL está habilitado** no banco de dados do Azure para o servidor MySQL. Para obter detalhes sobre como configurar o SSL, confira [Usar SSL com o Banco de Dados do Azure para MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

O suporte para redirecionamento em aplicativos PHP está disponível por meio da extensão [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , desenvolvida pela Microsoft. 

A extensão mysqlnd_azure está disponível para ser adicionada a aplicativos PHP por meio do PECL e é altamente recomendável instalar e configurar a extensão por meio do [pacote PECL](https://pecl.php.net/package/mysqlnd_azure)publicado oficialmente.

> [!IMPORTANT]
> O suporte para redirecionamento na extensão de [MYSQLND_AZURE](https://github.com/microsoft/mysqlnd_azure) php está atualmente em versão prévia.

### <a name="redirection-logic"></a>Lógica de redirecionamento

>[!IMPORTANT]
> A lógica/comportamento de redirecionamento que inicia a versão 1.1.0 foi atualizada e **é recomendável usar a versão 1.1.0 +**.

O comportamento de redirecionamento é determinado pelo valor de `mysqlnd_azure.enableRedirect`. A tabela a seguir descreve o comportamento do redirecionamento com base no valor desse parâmetro a partir da **versão 1.1.0 +**.

Se você estiver usando uma versão mais antiga da extensão de mysqlnd_azure (versão 1.0.0-1.0.3), o comportamento de redirecionamento será determinado pelo valor `mysqlnd_azure.enabled`de. Os valores válidos são `off` (atua da mesma forma que o comportamento descrito na tabela abaixo) e `on` (atua como `preferred` na tabela abaixo).  

|**valor mysqlnd_azure. enableRedirect**| **Comportamento**|
|----------------------------------------|-------------|
|`off` ou `0`|O redirecionamento não será usado. |
|`on` ou `1`|-Se o SSL não estiver habilitado no banco de dados do Azure para o servidor MySQL, nenhuma conexão será feita. O seguinte erro será retornado: *"mysqlnd_azure. enableRedirect está on, mas a opção SSL não está definida na cadeia de conexão. O redirecionamento só é possível com SSL. "*<br>-Se o SSL estiver habilitado no servidor MySQL, mas não houver suporte para o redirecionamento no servidor, a primeira conexão será anulada e o seguinte erro será retornado: *"a conexão foi anulada porque o redirecionamento não está habilitado no servidor MySQL ou o pacote de rede não atende ao protocolo de redirecionamento".*<br>-Se o servidor MySQL oferecer suporte ao redirecionamento, mas a conexão redirecionada falhar por algum motivo, também anulará a primeira conexão de proxy. Retornar o erro da conexão redirecionada.|
|`preferred` ou `2`<br>  (valor padrão)|-mysqlnd_azure usará o redirecionamento, se possível.<br>-Se a conexão não usar SSL, o servidor não oferece suporte ao redirecionamento ou a conexão redirecionada falha ao se conectar por qualquer motivo não fatal enquanto a conexão proxy ainda for válida, ela retornará à primeira conexão de proxy.|

As seções subsequentes do documento descreverão como instalar a `mysqlnd_azure` extensão usando PECL e definir o valor desse parâmetro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões do PHP 7.2.15 + e 7.3.2 +
- PÊRA DO PHP 
- PHP-MySQL
- Banco de dados do Azure para servidor MySQL com SSL habilitado

1. Instale o [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) com [PECL](https://pecl.php.net/package/mysqlnd_azure). É recomendável usar a versão 1.1.0 +.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Localize o diretório de extensão`extension_dir`() executando o seguinte:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Altere os diretórios para a pasta retornada e `mysqlnd_azure.so` Verifique se está localizado nesta pasta. 

4. Localize a pasta de arquivos. ini executando o seguinte: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Altere os diretórios para essa pasta retornada. 

6. Crie um novo arquivo. ini para `mysqlnd_azure`o. Certifique-se de que a ordem alfabética do nome seja posterior à do mysqnld, já que os módulos são carregados de acordo com a ordem de nome dos arquivos ini. Por exemplo, se `mysqlnd` . ini for nomeado `10-mysqlnd.ini`, nomeie o mysqlnd ini como `20-mysqlnd-azure.ini`.

7. No novo arquivo. ini, adicione as linhas a seguir para habilitar o redirecionamento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões do PHP 7.2.15 + e 7.3.2 +
- PHP-MySQL
- Banco de dados do Azure para servidor MySQL com SSL habilitado

1. Determine se você está executando uma versão x64 ou x86 do PHP executando o seguinte comando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Baixe a versão x64 ou x86 correspondente do [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll de [PECL](https://pecl.php.net/package/mysqlnd_azure) que corresponde à sua versão do php. É recomendável usar a versão 1.1.0 +.

3. Extraia o arquivo zip e localize a DLL `php_mysqlnd_azure.dll`denominada.

4. Localize o diretório de extensão`extension_dir`() executando o comando abaixo:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copie o `php_mysqlnd_azure.dll` arquivo para o diretório retornado na etapa 4. 

6. Localize a pasta PHP que contém `php.ini` o arquivo usando o seguinte comando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modifique o `php.ini` arquivo e adicione as seguintes linhas extras para habilitar o redirecionamento. 

    Na seção extensões dinâmicas: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Na seção Configurações do módulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Confirmar redirecionamento

Você também pode confirmar se o redirecionamento está configurado com o código PHP de exemplo abaixo. Crie um arquivo PHP chamado `mysqlConnect.php` e cole o código abaixo. Atualize o nome do servidor, o nome de usuário e a senha com o seu próprio. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre cadeias de conexão, consulte [cadeias de conexão](howto-connection-string.md).
