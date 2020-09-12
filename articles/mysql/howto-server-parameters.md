---
title: Configurar parâmetros do servidor-portal do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar os parâmetros do MySQL Server no Banco de Dados do Azure para MySQL usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/11/2020
ms.openlocfilehash: 8a988895cd8999d15c32d7056d35abf40aeaba7e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420686"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Configurar parâmetros de servidor no banco de dados do Azure para MySQL usando o portal do Azure

O Banco de Dados do Azure para MySQL dá suporte à configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros usando o portal do Azure. Nem todos os parâmetros de servidor podem ser ajustados.

## <a name="configure-server-parameters"></a>Configurar parâmetros do servidor

1. Faça logon no portal do Azure e, em seguida, localize o seu Banco de Dados do Azure para MySQL Server.
2. Na seção **CONFIGURAÇÕES**, clique em **Parâmetros do servidor** para abrir a página de parâmetros do servidor para o servidor do Banco de Dados do Azure para MySQL.
![Página de parâmetros do servidor do Portal do Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Localize as configurações que você precisa ajustar. Examine a coluna **Descrição** para entender a finalidade e os valores permitidos.
![Enumerar a lista suspensa](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em  **salvar** para salvar as alterações.
![Clique em salvar ou descartar mudanças](./media/howto-server-parameters/4-save_parameters.png)
5. Se você tiver salvo os novos valores para os parâmetros, você sempre pode reverter tudo o que fazer com os valores padrão selecionando **Redefinir tudo para o padrão**.
![Redefinir tudo para padrão](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>Parâmetros de configuração não listados

Se o parâmetro de servidor que você deseja atualizar não estiver listado no portal do Azure, você poderá, opcionalmente, definir o parâmetro no nível de conexão usando `init_connect` . Isso define os parâmetros de servidor para cada cliente que se conecta ao servidor. 

1. Na seção **CONFIGURAÇÕES**, clique em **Parâmetros do servidor** para abrir a página de parâmetros do servidor para o servidor do Banco de Dados do Azure para MySQL.
2. Pesquisar por `init_connect`
3. Adicione os parâmetros de servidor no formato: `SET parameter_name=YOUR_DESIRED_VALUE` em valor, a coluna valor.

    Por exemplo, você pode alterar o conjunto de caracteres do seu servidor definindo `init_connect` como `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Clique em **Salvar** para salvar as alterações.

>[!Note]
> `init_connect` pode ser usado para alterar parâmetros que não exigem SUPER privilégio (s) no nível da sessão. Para verificar se você pode definir o parâmetro usando `init_connect` , execute o `set session parameter_name=YOUR_DESIRED_VALUE;` comando e se ele se desconectar com **acesso negado; você precisa de privilégios de super privilégio** , não é possível definir o parâmetro usando ' init_connect '.

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser preenchidas, chamando o procedimento armazenado `mysql.az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL.

> [!NOTE]
> Se estiver executando o comando `mysql.az_load_timezone` do Workbench do MySQL, talvez seja necessário desativar primeiro o modo de atualização segura usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Você deve reiniciar o servidor para garantir que as tabelas de fuso horário sejam populadas corretamente. Para reiniciar o servidor, use a [CLI](howto-restart-server-cli.md) ou o [Portal do Azure](howto-restart-server-portal.md).

Para exibir os valores de fuso horário disponíveis, execute o comando a seguir:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurar o fuso horário de nível global

O fuso horário de nível global pode ser configurado na página **Parâmetros do servidor** no portal do Azure. O exemplo abaixo configura o fuso horário global para o valor "EUA/Pacífico".

![Definir o parâmetro de fuso horário](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Configurar o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser configurado, executando o comando `SET time_zone` a partir de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL. O exemplo abaixo configura o fuso horário para **EUA/Pacífico**.

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MySQL para [Funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Próximas etapas

- [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](concepts-connection-libraries.md).
