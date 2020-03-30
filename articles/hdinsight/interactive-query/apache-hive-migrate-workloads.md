---
title: Migrar Cargas de trabalho do Azure HDInsight 3.6 para hdInsight 4.0
description: Saiba como migrar as cargas de trabalho do Apache Hive no HDInsight 3.6 para o HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214655"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar Cargas de trabalho do Azure HDInsight 3.6 para hdInsight 4.0

Este documento mostra como migrar cargas de trabalho Apache Hive e LLAP no HDInsight 3.6 para o HDInsight 4.0. O HDInsight 4.0 fornece recursos mais novos de Hive e LLAP, como visualizações materializadas e cache de resultados de consulta. Quando você migra suas cargas de trabalho para o HDInsight 4.0, você pode usar muitos recursos mais novos do Hive 3 que não estão disponíveis no HDInsight 3.6.

Este artigo abrange os seguintes assuntos:

* Migração de metadados da Colmeia para HDInsight 4.0
* Migração segura de tabelas ACID e não-ACID
* Preservação das políticas de segurança da Colmeia nas versões do HDInsight
* Execução e depuração de consulta de HDInsight 3.6 para HDInsight 4.0

Uma vantagem da Colmeia é a capacidade de exportar metadados para um banco de dados externo (conhecido como Metastore da Colmeia). O **Metastore da Colmeia** é responsável por armazenar estatísticas de tabela, incluindo o local de armazenamento da tabela, nomes de colunas e informações de índice de tabela. O esquema do banco de dados metastore difere entre as versões de Colmeia. A maneira recomendada de atualizar o metastore da Colmeia com segurança é criar uma cópia e atualizar a cópia em vez do ambiente de produção atual.

## <a name="copy-metastore"></a>Metaloja de cópias

O HDInsight 3.6 e o HDInsight 4.0 exigem diferentes esquemas de metastore e não podem compartilhar um único metastore.

### <a name="external-metastore"></a>Metaloja externa

Crie uma nova cópia do seu metastore externo. Se você estiver usando um metastore externo, uma das maneiras seguras e fáceis de fazer uma cópia do metastore é [restaurar o banco](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) de dados com um nome diferente usando a função de restauração do Banco de Dados SQL.  Veja [Usar armazenamentos de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre como anexar um metastore externo a um cluster HDInsight.

### <a name="internal-metastore"></a>Metaloja interna

Se você estiver usando o metastore interno, você pode usar consultas para exportar definições de objeto sustais no metastore da Colmeia e importá-las para um novo banco de dados.

Uma vez que este script esteja concluído, presume-se que o antigo cluster não será mais usado para acessar qualquer uma das tabelas ou bancos de dados referidos no script.

> [!NOTE]
> No caso de tabelas ACID, uma nova cópia dos dados abaixo da tabela será criada.

1. Conecte-se ao cluster HDInsight usando um [cliente Secure Shell (SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Conecte-se ao HiveServer2 com seu [cliente Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) a partir da sessão ssh aberta, inserindo o seguinte comando:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Este comando gera um arquivo chamado **alltables.hql**.

1. Saia da sua sessão SSH. Em seguida, digite um comando scp para baixar **alltables.hql** localmente.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Faça upload **de alltables.hql** para o *novo* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Em seguida, use o SSH para se conectar ao *novo* cluster HDInsight. Execute o seguinte código da sessão SSH:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Atualizar metaloja

Uma vez que a **cópia** do metastore esteja concluída, execute um script de atualização de esquema no [Script Action](../hdinsight-hadoop-customize-cluster-linux.md) no cluster HDInsight 3.6 existente para atualizar a nova metaloja para o esquema hive 3. Isso permite que o banco de dados seja anexado como metastore HDInsight 4.0.

Use os valores na tabela mais abaixo. Substitua pelos `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` valores apropriados para o metastore da Colmeia **copiado,** separado por espaços. Não inclua ".database.windows.net" ao especificar o nome do servidor SQL.

|Propriedade | Valor |
|---|---|
|Tipo de script|- Personalizado|
|Nome|Atualização da colmeia|
|URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo(s) de nó|Head|
|Parâmetros|SQLSERVERNAME DATABASENAME SENHA DE NOME DE USUÁRIO|

> [!Warning]  
> A atualização que converte o esquema de metadados HDInsight 3.6 para o esquema HDInsight 4.0 não pode ser revertida.

Você pode verificar a atualização executando a seguinte consulta sql no banco de dados:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrar tabelas de Colmeia para HDInsight 4.0

Depois de completar o conjunto anterior de etapas para migrar o Metastore hive para hdInsight 4.0, as tabelas e bancos `show tables` `show databases` de dados registrados no metastore serão visíveis dentro do cluster HDInsight 4.0 executando ou a partir do cluster. Consulte [a execução de consulta em versões do HDInsight](#query-execution-across-hdinsight-versions) para obter informações sobre a execução de consultas em clusters HDInsight 4.0.

Os dados reais das tabelas, no entanto, não são acessíveis até que o cluster tenha acesso às contas de armazenamento necessárias. Para garantir que seu cluster HDInsight 4.0 possa acessar os mesmos dados do seu antigo cluster HDInsight 3.6, complete as seguintes etapas:

1. Determine a conta de armazenamento do Azure de sua tabela ou banco de dados.

1. Se o cluster HDInsight 4.0 já estiver em execução, anexe a conta de armazenamento DoZure ao cluster via Ambari. Se você ainda não criou o cluster HDInsight 4.0, certifique-se de que a conta de armazenamento do Azure seja especificada como a conta de armazenamento de cluster principal ou secundária. Para obter mais informações sobre a adição de contas de armazenamento aos clusters HDInsight, consulte [Adicionar contas adicionais de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Implantar o novo HDInsight 4.0 e conectar-se à nova metaloja

Depois que a atualização do esquema estiver concluída, implante um novo cluster HDInsight 4.0 e conecte o metastore atualizado. Se você já implantou o 4.0, defina-o para que você possa se conectar ao metastore da Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Execute o script de migração de esquemas do HDInsight 4.0

As tabelas são tratadas de forma diferente no HDInsight 3.6 e HDInsight 4.0. Por essa razão, você não pode compartilhar as mesmas tabelas para clusters de versões diferentes. Se você quiser usar o HDInsight 3.6 ao mesmo tempo que o HDInsight 4.0, você deve ter cópias separadas dos dados para cada versão.

Sua carga de trabalho de Colmeia pode incluir uma mistura de mesas ACID e não-ACID. Uma das principais diferenças entre hive no HDInsight 3.6 (Hive 2) e Hive no HDInsight 4.0 (Hive 3) é a conformidade acid-para tabelas. No HDInsight 3.6, ativar a conformidade com o HIVE ACID requer configuração adicional, mas nas tabelas HDInsight 4.0 são compatíveis com ÁCIDO por padrão. A única ação necessária antes da migração é executar uma grande compactação contra a tabela ACID no cluster 3.6. Na exibição colmeia ou de Beeline, execute a seguinte consulta:

```sql
alter table myacidtable compact 'major';
```

Essa compactação é necessária porque as tabelas HDInsight 3.6 e HDInsight 4.0 ACID entendem os deltas ACID de forma diferente. A compactação impõe uma ficha limpa que garante consistência. A seção 4 da documentação de [migração da Colmeia](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contém orientações para compactação em massa das tabelas HDInsight 3.6 ACID.

Depois de concluir as etapas de migração e compactação do metastore, você pode migrar o armazém real. Depois de concluir a migração do armazém Hive, o armazém HDInsight 4.0 terá as seguintes propriedades:

|3.6 |4,0 |
|---|---|
|Tabelas externas|Tabelas externas|
|Tabelas gerenciadas não transacionais|Tabelas externas|
|Tabelas gerenciadas transacionais|Tabelas gerenciadas|

Você pode precisar ajustar as propriedades do seu armazém antes de executar a migração. Por exemplo, se você espera que alguma tabela seja acessada por terceiros (como um cluster HDInsight 3.6), essa tabela deve ser externa assim que a migração estiver concluída. No HDInsight 4.0, todas as tabelas gerenciadas são transacionais. Portanto, as tabelas gerenciadas no HDInsight 4.0 só devem ser acessadas pelos clusters HDInsight 4.0.

Uma vez que as propriedades da tabela estejam definidas corretamente, execute a ferramenta de migração do armazém Hive de um dos headnodes de cluster usando o shell SSH:

1. Conecte-se ao cabeçade do cluster usando SSH. Para obter instruções, consulte [Conecte-se ao HDInsight usando o SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Abra uma concha de login como o usuário da Colmeia executando`sudo su - hive`
1. Determine a versão de pilha `ls /usr/hdp`de plataforma de dados executando . Isso exibirá uma seqüência de versões que você deve usar no próximo comando.
1. Execute o seguinte comando do shell. Substitua pela `STACK_VERSION` seqüência de versões da etapa anterior:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Depois que a ferramenta de migração for concluída, seu armazém Hive estará pronto para o HDInsight 4.0.

> [!Important]  
> As tabelas gerenciadas no HDInsight 4.0 (incluindo tabelas migradas de 3,6) não devem ser acessadas por outros serviços ou aplicativos, incluindo clusters HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Secure Hive em versões HDInsight

Desde o HDInsight 3.6, o HDInsight integra-se ao Azure Active Directory usando o HDInsight Enterprise Security Package (ESP). O ESP usa Kerberos e Apache Ranger para gerenciar as permissões de recursos específicos dentro do cluster. As políticas de Ranger implantadas contra o Hive no HDInsight 3.6 podem ser migradas para o HDInsight 4.0 com as seguintes etapas:

1. Navegue até o painel Do Gerenciador de Serviços ranger em seu cluster HDInsight 3.6.
2. Navegue até a política chamada **HIVE** e exporte a apólice para um arquivo json.
3. Certifique-se de que todos os usuários referidos na política exportada json existam no novo cluster. Se um usuário for referido no json de política, mas não existir no novo cluster, adicione o usuário ao novo cluster ou remova a referência da diretiva.
4. Navegue até o painel **Do Gerenciador de Serviços ranger** em seu cluster HDInsight 4.0.
5. Navegue até a política chamada **HIVE** e importe a política ranger json do passo 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Verifique a compatibilidade e modifique códigos conforme necessário no aplicativo de teste

Ao migrar cargas de trabalho, como programas e consultas existentes, verifique as notas de liberação e a documentação para obter alterações e aplique alterações conforme necessário. Se o cluster HDInsight 3.6 estiver usando um metastore compartilhado de Spark e Hive, será necessária [uma configuração adicional usando o Conector do Armazém Hive.](./apache-hive-warehouse-connector.md)

## <a name="deploy-new-app-for-production"></a>Implantar novo aplicativo para produção

Para mudar para o novo cluster, por exemplo, você pode instalar um novo aplicativo cliente e usá-lo como um novo ambiente de produção, ou você pode atualizar seu aplicativo cliente existente e mudar para HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Mude o HDInsight 4.0 para a produção

Se as diferenças foram criadas no metastore durante os testes, você precisará atualizar as alterações pouco antes de mudar. Neste caso, você pode exportar & importar o metastore e, em seguida, atualizar novamente.

## <a name="remove-the-old-production"></a>Remova a produção antiga

Uma vez confirmado que a versão está completa e totalmente operacional, você pode remover a versão 3.6 e o metastore anterior. Certifique-se de que tudo está migrado antes de excluir o ambiente.

## <a name="query-execution-across-hdinsight-versions"></a>Execução de consulta em versões do HDInsight

Existem duas maneiras de executar e depurar consultas hive/llap dentro de um cluster HDInsight 3.6. A HiveCLI oferece uma experiência de linha de comando e a exibição Tez/Colmeia fornece um fluxo de trabalho baseado em GUI.

No HDInsight 4.0, o HiveCLI foi substituído por Beeline. HiveCLI é um cliente de brechó para hiveserver 1, e Beeline é um cliente JDBC que fornece acesso ao Hiveserver 2. Beeline também pode ser usado para se conectar a qualquer outro ponto final de banco de dados compatível com JDBC. Beeline está disponível fora da caixa no HDInsight 4.0 sem necessidade de instalação.

No HDInsight 3.6, o cliente gui para interagir com o servidor Hive é o Ambari Hive View. O HDInsight 4.0 não é enviado com o Ambari View. Nós fornecemos uma maneira para nossos clientes usarem o Data Analytics Studio (DAS), que não é um serviço CENTRAL HDInsight. O DAS não é enviado com clusters HDInsight fora da caixa e não é um pacote suportado oficialmente. No entanto, o DAS pode ser instalado no cluster usando uma [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) da seguinte forma:

|Propriedade | Valor |
|---|---|
|Tipo de script|- Personalizado|
|Nome|DAS|
|URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Tipo(s) de nó|Head|

Aguarde de 10 a 15 minutos e `https://CLUSTERNAME.azurehdinsight.net/das/`inicie o Data Analytics Studio usando esta URL: .

Uma atualização da UI Ambari e/ou uma reinicialização de todos os componentes Ambari pode ser necessária antes de acessar o DAS.

Uma vez que o DAS esteja instalado, se você não ver as consultas executadas no visualizador de consultas, faça as seguintes etapas:

1. Defina as configurações para Hive, Tez e DAS conforme descrito [neste guia para a instalação das de solução de problemas](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Certifique-se de que as seguintes configurações de diretório de armazenamento do Azure são blobs de página e que estão listadas em: `fs.azure.page.blob.dirs`
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Reinicie HDFS, Hive, Tez e DAS em ambos os headnodes.

## <a name="next-steps"></a>Próximas etapas

* [Anúncio do HDInsight 4.0](../hdinsight-version-release.md)
* [HDInsight 4.0 mergulho profundo](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Colmeia 3 Mesas ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
