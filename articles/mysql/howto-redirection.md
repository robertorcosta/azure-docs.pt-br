---
title: Conecte-se com o redirecionamento - Banco de dados Azure para MySQL
description: Este artigo descreve como você pode configurar seu aplicativo para se conectar ao Banco de Dados Azure para MySQL com redirecionamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246316"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Conecte-se ao banco de dados Do Zure para MySQL com redirecionamento

Este tópico explica como conectar um aplicativo seu banco de dados Azure para o servidor MySQL com o modo de redirecionamento. O redirecionamento visa reduzir a latência de rede entre aplicativos clientes e servidores MySQL, permitindo que os aplicativos se conectem diretamente aos nós do servidor backend.

## <a name="before-you-begin"></a>Antes de começar
Faça login no [portal Azure](https://portal.azure.com). Crie um banco de dados Azure para servidor MySQL com a versão do motor 5.6, 5.7 ou 8.0. Para obter detalhes, confira [Como criar o Banco de Dados do Azure para servidor MySQL por meio do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou [Como criar o Banco de Dados do Azure para servidor MySQL usando a CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Atualmente, o redirecionamento só é suportado quando **o SSL está ativado** no banco de dados Azure para o servidor MySQL. Para obter detalhes sobre como configurar o SSL, confira [Usar SSL com o Banco de Dados do Azure para MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

O suporte para redirecionamento em aplicativos PHP está disponível através da extensão [mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) desenvolvida pela Microsoft. 

A extensão mysqlnd_azure está disponível para adicionar aos aplicativos PHP através do PECL e é altamente recomendável instalar e configurar a extensão através do [pacote PECL](https://pecl.php.net/package/mysqlnd_azure)publicado oficialmente.

> [!IMPORTANT]
> O suporte para redirecionamento na extensão [de mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) PHP está atualmente em pré-visualização.

### <a name="redirection-logic"></a>Lógica de redirecionamento

>[!IMPORTANT]
> O redirecionamento lógico/comportamento inicial versão 1.1.0 foi atualizado e **recomenda-se o uso da versão 1.1.0+**.

O comportamento de redirecionamento `mysqlnd_azure.enableRedirect`é determinado pelo valor de . A tabela abaixo descreve o comportamento do redirecionamento com base no valor deste parâmetro a partir da **versão 1.1.0+**.

Se você estiver usando uma versão mais antiga da extensão mysqlnd_azure (versão 1.0.0-1.0.3), o comportamento de redirecionamento é determinado pelo valor de `mysqlnd_azure.enabled`. Os valores `off` válidos são (atos semelhantes `on` aos comportamentos `preferred` descritos na tabela abaixo) e (atos como na tabela abaixo).  

|**mysqlnd_azure.enableRedirect value**| **Comportamento**|
|----------------------------------------|-------------|
|`off` ou `0`|O redirecionamento não será utilizado. |
|`on` ou `1`|- Se o SSL não estiver habilitado no banco de dados Azure para o servidor MySQL, nenhuma conexão será feita. O seguinte erro será retornado: *"mysqlnd_azure.enableRedirect está ativado, mas a opção SSL não está definida na seqüência de conexões. O redirecionamento só é possível com o SSL."*<br>- Se o SSL estiver habilitado no servidor MySQL, mas o redirecionamento não for suportado no servidor, a primeira conexão será abortada e o seguinte erro será retornado: *"A conexão abortada porque o redirecionamento não está ativado no servidor MySQL ou o pacote de rede não atende ao protocolo de redirecionamento."*<br>- Se o servidor MySQL suportar o redirecionamento, mas a conexão redirecionada falhar por qualquer motivo, também abortar a primeira conexão proxy. Retorne o erro da conexão redirecionada.|
|`preferred` ou `2`<br>  (valor padrão)|- mysqlnd_azure usarão o redirecionamento, se possível.<br>- Se a conexão não usar SSL, o servidor não suportar redirecionamento ou a conexão redirecionada falhar de conexão por qualquer motivo não fatal enquanto a conexão proxy ainda for válida, ela voltará para a primeira conexão proxy.|

As seções subseqüentes do documento `mysqlnd_azure` irão delinear como instalar a extensão usando PECL e definir o valor deste parâmetro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões PHP 7.2.15+ e 7.3.2+
- PHP PEAR 
- php-mysql
- Banco de dados Azure para servidor MySQL com SSL ativado

1. Instale [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) com [PECL](https://pecl.php.net/package/mysqlnd_azure). Recomenda-se usar a versão 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Localize o diretório`extension_dir`de extensão ( ) executando o abaixo:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Alterar diretórios para a pasta `mysqlnd_azure.so` retornada e garantir que está localizado nesta pasta. 

4. Localize a pasta para arquivos .ini executando o abaixo: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Alterar diretórios para esta pasta retornada. 

6. Crie um novo arquivo `mysqlnd_azure`.ini para . Certifique-se de que a ordem do alfabeto do nome esteja atrás da do mysqnld, uma vez que os módulos são carregados de acordo com a ordem de nome dos arquivos ini. Por exemplo, `mysqlnd` se .ini for nomeado, `10-mysqlnd.ini`nomeie `20-mysqlnd-azure.ini`o mysqlnd ini como .

7. Dentro do novo arquivo .ini, adicione as seguintes linhas para permitir o redirecionamento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões PHP 7.2.15+ e 7.3.2+
- php-mysql
- Banco de dados Azure para servidor MySQL com SSL ativado

1. Determine se você está executando uma versão x64 ou x86 do PHP executando o seguinte comando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Baixe a versão correspondente x64 ou x86 do [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL do [PECL](https://pecl.php.net/package/mysqlnd_azure) que corresponde à sua versão de PHP. Recomenda-se usar a versão 1.1.0+.

3. Extrair o arquivo zip e `php_mysqlnd_azure.dll`encontrar o DLL chamado .

4. Localize o diretório`extension_dir`de extensão ( ) executando o comando abaixo:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copie `php_mysqlnd_azure.dll` o arquivo no diretório retornado na etapa 4. 

6. Localize a pasta PHP contendo o `php.ini` arquivo usando o seguinte comando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modifique `php.ini` o arquivo e adicione as seguintes linhas extras para permitir o redirecionamento. 

    Na seção Extensões Dinâmicas: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Na seção Configurações do módulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Confirmar o redirecionamento

Você também pode confirmar que o redirecionamento está configurado com o código PHP da amostra abaixo. Crie um arquivo `mysqlConnect.php` PHP chamado e cole o código abaixo. Atualize o nome do servidor, nome de usuário e senha com o seu próprio. 
 
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
Para obter mais informações sobre strings de conexão, consulte [Strings de conexão](howto-connection-string.md).
