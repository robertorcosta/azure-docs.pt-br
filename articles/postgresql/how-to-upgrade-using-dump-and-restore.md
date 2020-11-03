---
title: Atualizar usando despejo e restauração-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve alguns métodos para despejar e restaurar bancos de dados para migrar para uma versão mais recente do banco de dados do Azure para PostgreSQL-servidor único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 26154f4501daba373f1f8b108f1ee7105b1b194f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294239"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Atualizar seu banco de dados PostgreSQL usando despejo e restauração

No banco de dados do Azure para PostgreSQL-servidor único, é recomendável atualizar seu mecanismo de banco de dados PostgreSQL para uma versão principal mais alta usando um destes métodos:
* Método offline usando PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). Nesse método, primeiro você executa o despejo do servidor de origem e, em seguida, restaura esse despejo no servidor de destino.
* Método online usando o [**serviço de migração de banco de dados**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Esse método mantém o banco de dados de destino em sincronia com a origem e você pode escolher quando recortar. No entanto, há alguns pré-requisitos e restrições a serem resolvidos. 

Você pode usar a recomendação a seguir ao decidir entre os métodos online e offline para executar atualizações de versão principais.

| **Banco de dados** | **Despejo/restauração (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Você tem um banco de dados pequeno e pode pagar tempo de inatividade para atualizar  | X | |
| Bancos de dados pequenos (< 10 GB)  | X | X | 
| Bancos de pequenos-médios (10 GB – 100 GB) | X | X |
| Bancos de dados grandes (> 100 GB) |  | X |
| Pode arcar com o tempo de inatividade para atualizar (independentemente do tamanho do banco de dados) | X |  |
| Pode resolver os [pré-requisitos](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) do DMS, incluindo uma reinicialização? |  | X |
| Pode evitar tabelas DDLs e não registradas durante o processo de atualização? | |  X |

Este guia de instruções fornece dois métodos de exemplo sobre como atualizar seus bancos de dados usando os comandos PostgreSQL pg_dump e pg_restore. O processo neste documento é chamado de **atualização** , embora o banco de dados seja  **migrado** do servidor de origem para o servidor de destino. 

> [!NOTE]
> O despejo e a restauração do PostgreSQL podem ser executados de várias maneiras. Você pode optar por usar métodos diferentes do que é mencionado neste documento. Por exemplo, para fazer um despejo seguido pela restauração de um cliente PostgreSQL, consulte a [documentação](./howto-migrate-using-dump-and-restore.md) para obter detalhes e práticas recomendadas do procedimento passo a passo. Para obter uma sintaxe detalhada de despejo e restauração com parâmetros adicionais, consulte os artigos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Pré-requisitos para usar despejo e restauração com o Azure PostgreSQL
 
Para percorrer este guia de instruções, você precisa de:
- Um banco de dados de origem executando 9,5, 9,6 ou 10 (banco de dados do Azure para PostgreSQL – servidor único)
- Servidor de banco de dados de destino com o [servidor do banco de dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)da versão principal do PostgreSQL. 
- Um sistema cliente (Linux) com PostgreSQL instalado e [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) utilitários de linha de comando instalados. 
- Como alternativa, você pode usar [Azure cloud Shell](https://shell.azure.com) ou clicando na Azure cloud Shell na barra de menus na parte superior direita da [portal do Azure](https://portal.azure.com). Você precisará fazer logon em sua conta `az login` antes de executar os comandos dump e Restore.
- O local do cliente PostgreSQL, como uma VM preferencialmente em execução na mesma região que os servidores de origem e de destino). 

## <a name="additional-details-and-considerations"></a>Detalhes e considerações adicionais
- Você pode encontrar a cadeia de conexão para os bancos de dados de origem e de destino clicando em "cadeias de conexão" no Portal. 
- Você pode estar executando mais de um banco de dados em seu servidor. Você pode encontrar a lista de bancos de dados conectando-se ao seu servidor de origem e executando o `\l` .
- Crie bancos de dados correspondentes no servidor de banco de dados de destino.
- Você pode ignorar a atualização `azure_maintenance` ou os bancos de dados de modelo.
- Consulte as tabelas acima para determinar se o banco de dados é adequado para esse modo de migração.
- Se você quiser usar Azure Cloud Shell, a sessão atingirá o tempo limite após 20 minutos. Se o tamanho do seu banco de dados for < 10 GB, você poderá concluir a atualização sem tempo limite. Caso contrário, talvez você precise manter a sessão aberta por outros meios, como pressionar a <Enter> tecla uma vez em 10-15 minutos. 

> [!TIP] 
> - Se você estiver usando a mesma senha para a origem e o banco de dados de destino, poderá definir a `PGPASSWORD=yourPassword` variável de ambiente.  Em seguida, você não precisa fornecer a senha sempre que executar comandos como psql, pg_dump e pg_restore.  Da mesma forma, você pode configurar variáveis adicionais como `PGUSER` , `PGSSLMODE` etc., consulte [variáveis de ambiente do PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
>  
> - Se o seu servidor PostgreSQL exigir conexões TLS/SSL (ativado por padrão no banco de dados do Azure para servidores PostgreSQL), defina uma variável de ambiente `PGSSLMODE=require` para que a ferramenta de pg_restore se conecte ao TLS. Sem o TLS, o erro pode ser lido  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - Na linha de comando do Windows, execute o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. No Linux ou o Bash, execute o comando `export PGSSLMODE=require` antes de executar o comando pg_restore.

## <a name="example-database-used-in-this-guide"></a>Banco de dados de exemplo usado neste guia

 | **Descrição** | **Valor** |
 | ------- | ------- |
 | Servidor de origem (v 9.5) | pg-95.postgres.database.azure.com |
 | Banco de dados de origem | bench5gb |
 | Tamanho do banco de dados de origem | 5 GB |
 | Nome de usuário de origem | pg@pg-95 |
 | Servidor de destino (v11) | pg-11.postgres.database.azure.com |
 | Banco de dados de destino | bench5gb |
 | Nome de usuário de destino | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>Método 1: atualizar com backups de streaming para o destino 

Nesse método, todo o despejo do banco de dados é transmitido diretamente para o servidor de banco de dados de destino e não armazena o despejo no cliente. Portanto, isso pode ser usado com um cliente com armazenamento limitado e até mesmo pode ser executado a partir do Azure Cloud Shell. 

1. Verifique se o banco de dados existe no servidor de destino usando o `\l` comando. Se o banco de dados não existir, crie o banco de dados.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```bash
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Execute o despejo e a restauração como uma única linha de comando usando um pipe. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Por exemplo,

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Depois que o processo de atualização (migração) for concluído, você poderá testar seu aplicativo com o servidor de destino. 
4. Repita esse processo para todos os bancos de dados no servidor.

 A tabela a seguir ilustra o tempo necessário para atualizar usando esse método. Os dados são populados usando [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Como seu banco de dados pode ter diferentes números de objetos com tamanhos variados do que pgbench tabelas e índices gerados, é altamente recomendável testar o despejo e a restauração do banco de dados para entender o tempo real necessário para atualizar seu banco de dados. 

| **Tamanho do banco de dados** | **Tempo gasto** | 
| ----- | ------ |
| 1 GB  | 1-2 minutos |
| 5 GB | 8-10 minutos |
| 10 GB | 15-20 minutos |
| 50 GB | 1 a 1,5 horas |
| 100 GB | 2,5 a 3 horas|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>Método 2: atualizar com despejo paralelo e restauração 

Esse método será útil se você tiver poucas tabelas maiores no banco de dados e quiser paralelizar o processo de despejo e restauração para esse banco de dados. Você precisa de armazenamento em disco local suficiente para acomodar os despejos de backup para seus bancos de dados. Esse processo paralelo de despejo e restauração reduz o tempo de consumo para concluir toda a migração/atualização. Por exemplo, o banco de dados de 50 GB pgbench que levou de 1 a 1,5 h para migrar foi concluído em menos de 30 minutos.

1. Para cada banco de dados no servidor de origem, crie um banco de dados correspondente no servidor de destino.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Por exemplo,
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Execute o comando pg_dump em um formato de diretório com número de trabalhos = 4 (número de tabelas no banco de dados). Com uma camada de computação maior e com mais tabelas, você pode aumentá-la para um número mais alto. Esse pg_dump criará um diretório para armazenar arquivos compactados para cada trabalho.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Por exemplo,
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Em seguida, restaure o backup no servidor de destino.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Por exemplo,
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> O processo mencionado neste documento também pode ser usado para atualizar seu banco de dados do Azure para PostgreSQL-servidor flexível, que está em versão prévia. A principal diferença é que a cadeia de conexão para o destino do servidor flexível é sem o `@dbName` .  Por exemplo, se o nome de usuário for `pg` , o nome de usuários do servidor único na cadeia de conexão será `pg@pg-95` , enquanto com o servidor flexível, você pode simplesmente usar `pg` .

## <a name="next-steps"></a>Próximas etapas

- Quando estiver satisfeito com a função de banco de dados de destino, você poderá remover o servidor de banco de dados antigo. 
- Se você quiser usar o mesmo ponto de extremidade de banco de dados que o servidor de origem, depois de ter excluído o servidor de banco de dados de origem antigo, você poderá criar uma réplica de leitura com o nome do servidor de banco de dados antigo. Quando o estado Steady for estabelecido, você poderá interromper a réplica, o que promoverá o servidor de réplica para ser um servidor independente. Consulte [Replicação](./concepts-read-replicas.md) para obter mais detalhes.
- Lembre-se de testar e validar esses comandos em um ambiente de teste antes de usá-los em produção.
