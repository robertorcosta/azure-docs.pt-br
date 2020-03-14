---
title: Configurar parâmetros do servidor-portal do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar os parâmetros do MySQL Server no Banco de Dados do Azure para MySQL usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 8ec6f32d7db0161cef00330aa38601ba9bdb309d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245714"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como configurar parâmetros de servidor no Banco de Dados do Azure para MySQL usando o portal do Azure

O Banco de Dados do Azure para MySQL dá suporte à configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros usando o portal do Azure. Nem todos os parâmetros de servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue até Parâmetros de servidor no portal do Azure

1. Faça logon no portal do Azure e, em seguida, localize o seu Banco de Dados do Azure para MySQL Server.
2. Na seção **CONFIGURAÇÕES**, clique em **Parâmetros do servidor** para abrir a página de parâmetros do servidor para o servidor do Banco de Dados do Azure para MySQL.
![Página de parâmetros do servidor do Portal do Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Localize as configurações que você precisa ajustar. Examine a coluna **Descrição** para entender a finalidade e os valores permitidos.
![Enumerar a lista suspensa](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **Salvar** para salvar as alterações.
![Clique em salvar ou descartar mudanças](./media/howto-server-parameters/4-save_parameters.png)
5. Se você tiver salvo os novos valores para os parâmetros, você sempre pode reverter tudo o que fazer com os valores padrão selecionando **Redefinir tudo para o padrão**.
![Redefinir tudo para padrão](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros de servidor configuráveis

A lista de parâmetros de servidor com suporte está em constante crescimento. Use a guia de parâmetros de servidor no portal do Azure para obter a definição e configurar parâmetros de servidor com base em seus requisitos de aplicativo.

## <a name="non-configurable-server-parameters"></a>Parâmetros do servidor não configuráveis

O tamanho do pool de buffers InnoDB não é configurável e está vinculado ao seu [tipo de preço](concepts-service-tiers.md).

|**Tipo de preço**|**vCore(s)**|**Tamanho do pool de buffers InnoDB em MB <br>(servidores com suporte para até 4 TB de armazenamento)**| **Tamanho do pool de buffers InnoDB em MB <br>(servidores com suporte para até 16 TB de armazenamento)**|
|:---|---:|---:|---:|
|Basic| 1| 832| |
|Basic| 2| 2560| |
|Uso Geral| 2| 3584| 7168|
|Uso Geral| 4| 7680| 15360|
|Uso Geral| 8| 15360| 30720|
|Uso Geral| 16| 31232| 62464|
|Uso Geral| 32| 62976| 125952|
|Uso Geral| 64| 125952| 251904|
|Otimizado para memória| 2| 7168| 14336|
|Otimizado para memória| 4| 15360| 30720|
|Otimizado para memória| 8| 30720| 61440|
|Otimizado para memória| 16| 62464| 124928|
|Otimizado para memória| 32| 125952| 251904|

Esses parâmetros de servidor adicionais não são configuráveis no sistema:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table na camada Básica|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros de servidor que não estão listados aqui são configurados com seus valores padrão iniciais MySQL nas versões [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser preenchidas, chamando o procedimento armazenado `az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL.

> [!NOTE]
> Se estiver executando o comando `az_load_timezone` do Workbench do MySQL, talvez seja necessário desativar primeiro o modo de atualização segura usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Você deve reiniciar o servidor para garantir que as tabelas de fuso horário sejam populadas corretamente. Para reiniciar o servidor, use o [portal do Azure](howto-restart-server-portal.md) ou a [CLI](howto-restart-server-cli.md).

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
