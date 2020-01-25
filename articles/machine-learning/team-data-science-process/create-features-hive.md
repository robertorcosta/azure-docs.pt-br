---
title: Criar recursos para dados em um Azure HDInsight Hadoop cluster-processo de ciência de dados da equipe
description: Exemplos de consultas de Hive que geram recursos em dados armazenados em um cluster Hadoop do Azure HDInsight.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c926aac3ea4360793ff52b616a55dc6198357c8a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721771"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Criar recursos para os dados em um cluster Hadoop usando as consultas do Hive
Este documento mostra como criar recursos para os dados armazenados em um cluster Hadoop do Azure HDInsight usando consultas do Hive. Essas consultas de Hive usam UDFs (funções definidas pelo usuário) de Hive incorporadas, Os scripts para eles são fornecidos.

As operações necessárias para criar recursos podem ter uso intensivo de memória. O desempenho das consultas do Hive se torna mais crítico nesses casos e pode ser melhorado com o ajuste de determinados parâmetros. O ajuste desses parâmetros é abordado na seção final.

Alguns exemplos de consultas apresentados são específicos para cenários de [Dados de Corridas de Táxi em Nova York](https://chriswhong.com/open-data/foil_nyc_taxi/) e também são fornecidos no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Essas consultas já tem o esquema de dados especificado e estão prontas para ser enviadas para execução. Na seção final, são discutidos os parâmetros que os usuários podem ajustar para que o desempenho das consultas do Hive possa ser melhorado.

Essa tarefa é uma etapa no [TDSP (Processo de Ciência de Dados de Equipe)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, confira [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-account-create.md)
* Provisionou um cluster do Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte [Personalizar os clusters do Hadoop do Azure HDInsight para análise avançada](customize-hadoop-cluster.md).
* Os dados foram carregados para tabelas Hive em clusters do Hadoop do Azure HDInsight. Se não, siga as instruções de [Criar e carregar dados nas tabelas Hive](move-hive-tables.md) para carregar dados nas tabelas Hive primeiro.
* Habilitou o acesso remoto ao cluster. Se precisar de instruções, consulte [Acessar o nó principal do Cluster do Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Geração de recursos
Nesta seção, veja vários exemplos dos modos pelos quais os recursos podem ser gerados usando consultas de Hive. Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser associada à tabela original. Estes são os exemplos apresentados:

1. [Geração de recursos baseada em frequência](#hive-frequencyfeature)
2. [Riscos de variáveis categóricas na classificação binária](#hive-riskfeature)
3. [Extrair recursos do campo Datetime](#hive-datefeatures)
4. [Extrair recursos de campo de texto](#hive-textfeatures)
5. [Calcular a distância entre coordenadas de GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Geração de recursos baseada em frequência
Geralmente é útil calcular as frequências dos níveis de uma variável categórica ou as frequências de determinadas combinações de níveis de diversas variáveis categóricas. Os usuários podem usar o script a seguir para calcular tais frequências:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Riscos de variáveis categóricas na classificação binária
Na classificação binária, as variáveis categóricas não numéricas devem ser convertidas em recursos numéricos quando os modelos que estão sendo utilizados possuem apenas recursos numéricos. Essa conversão é feita substituindo cada nível não numérico por um risco numérico. Esta seção mostra algumas consultas genéricas do Hive que calculam os valores de risco (probabilidades de log) de uma variável categórica.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

Neste exemplo, as variáveis `smooth_param1` e `smooth_param2` são definidas para suavizar os valores de risco calculados por meio dos dados. Riscos tem um intervalo entre -Inf e Inf. Um risco > 0 indica que a probabilidade de o destino ser igual a 1 é maior que 0,5.

Depois da tabela de risco ser calculada, os usuários podem atribuir valores de risco a uma tabela associando-a à tabela de risco. A consulta de associação de Hive foi indicada na seção anterior.

### <a name="hive-datefeatures"></a>Extrair recursos de campos datetime
O Hive vem com um conjunto de UDFs para processar campos datetime. No Hive, o formato de datetime padrão é “aaaa-MM-dd 00:00:00” (“1970-01-01 12:21:32”, por exemplo). Esta seção mostra exemplos que extraem o dia de um mês, o mês de um campo datetime e outros exemplos que convertem uma cadeia de caracteres de datetime em um formato diferente do formato padrão para uma cadeia de caracteres de datetime no formato padrão.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Essa consulta de Hive pressupõe que o *campo\<datetime >* está no formato DateTime padrão.

Se um campo datetime não estiver no formato padrão, é necessário primeiro converter o campo datetime em carimbo de data/hora de Unix e, em seguida, converter o carimbo de data/hora do Unix em uma cadeia de caracteres datetime no formato padrão. Quando o datetime estiver no formato padrão, os usuários poderão aplicar UDFs datetime incorporadas para extrair recursos.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Nessa consulta, se o *campo\<datetime >* tiver o padrão como *03/26/2015 12:04:39*, o *padrão de\<do campo datetime > '* deverá ser `'MM/dd/yyyy HH:mm:ss'`. Para testá-lo, os usuários podem executar

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

O *hivesampletable* nesta consulta vem pré-instalado com todos os clusters do Hadoop do Azure HDInsight por padrão quando os clusters são provisionados.

### <a name="hive-textfeatures"></a>Extrair recursos de campos de texto
Quando a tabela Hive tem um campo de texto que contém uma cadeia de caracteres de palavras delimitada por espaços, a consulta a seguir extrai o comprimento da cadeia de caracteres e o número de palavras na cadeia de caracteres.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Calcular a distância entre conjuntos de coordenadas de GPS
A consulta fornecida nesta seção pode ser aplicada diretamente aos Dados de Viagens de Táxi em NYC. A finalidade dessa consulta é mostrar como aplicar uma função matemática inserida no Hive para gerar recursos.

Os campos que são usados nesta consulta são coordenadas de GPS de locais de saída e chegada, chamadas *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* e *dropoff\_latitude*. As consultas que calculam a distância direta entre as coordenadas de saída e chegada são:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

As equações matemáticas que calculam a distância entre duas coordenadas de GPS podem ser encontradas no site <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Scripts de Tipo Móvel</a>, as quais foram criadas por Peter Lapisu. Nesse JavaScript, a função `toRad()` é apenas *lat_or_lon*PI/180, que converte graus em radianos. Aqui, *lat_or_lon* é a latitude ou a longitude. Como o Hive não fornece a função `atan2`, mas fornece a função `atan`, a função `atan2` é implementada pela função `atan` na consulta de Hive acima usando a definição fornecida na <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédia</a>.

![Criar workspace](./media/create-features-hive/atan2new.png)

Uma lista completa de UDFs internas do Hive pode ser encontrada na seção **Funções Internas** no <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki do Apache Hive</a>.  

## <a name="tuning"></a> Tópicos avançados: ajustar parâmetros de Hive para melhorar a velocidade de consulta
As configurações de parâmetro padrão do cluster de Hive talvez não sejam adequadas para as consultas de Hive e os dados que as consultas estão processando. Esta seção aborda alguns parâmetros que os usuários podem ajustar para melhorar o desempenho das consultas de Hive. Os usuários precisam adicionar as consultas de ajuste de parâmetro antes das consultas de processamento de dados.

1. **Espaço de heap de Java**: para consultas que envolvem o ingresso de grandes conjuntos de dados ou o processamento de registros longos, um dos erros comuns é **ficar sem espaço de heap**. Este erro pode ser evitado configurando os parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* para os valores desejados. Veja um exemplo:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Esse parâmetro aloca a memória de 4 GB para o espaço de heap de Java e também torna a classificação mais eficiente alocando mais memória para ele. É uma boa ideia explorar essas alocações se houver erros de falha de trabalho relacionados ao espaço de heap.

1. **Tamanho do bloco de DFS**: esse parâmetro define a menor unidade de dados armazenada pelo sistema de arquivos. Como um exemplo, se o tamanho do bloco DFS for de 128 MB, qualquer dado de tamanho inferior a 128 MB será armazenado em um único bloco. Os dados maiores que 128 MB são blocos extras atribuídos. 
2. Escolher um tamanho de bloco muito pequeno causa grandes sobrecargas no Hadoop, pois o nó de nome precisa processar muitas solicitações a mais para localizar o bloco relevante em relação ao arquivo. Uma configuração recomendada ao trabalhar com gigabytes de dados (ou mais ainda) é:

        set dfs.block.size=128m;

2. **Otimizar a operação de junção no Hive**: embora as operações de junção na estrutura de mapeamento/redução geralmente ocorram na fase de redução, às vezes, é possível obter enormes ganhos agendando junções na fase de mapeamento (também chamada de “mapjoins”). Defina esta opção:
   
       set hive.auto.convert.join=true;

3. **Especificar o número de mapeadores para o Hive**: embora o Hadoop permita ao usuário definir o número de redutores, o número de mapeadores não é normalmente definido pelo usuário. Um truque que permite certo grau de controle sobre esse número é escolher as variáveis do Hadoop, *mapred.min.split.size* e *mapred.max.split.size*, pois o tamanho de cada tarefa de mapeamento é determinado por:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normalmente, o valor padrão de:
    
   - *mapred.min.split.size* é 0, de
   - *mapred.max.split.size* é **Long.MAX** e de 
   - *dfs.block.size* é 64 MB.

     Como vemos, dado o tamanho dos dados, ajustar esses parâmetros ao “configurá-los” permite ajustar o número de mapeadores usado.

4. Aqui estão algumas outras **opções avançadas** para otimizar o desempenho de Hive. Essas opções permitem que você defina a memória alocada para mapear e reduzir tarefas e pode ser útil para ajustar o desempenho. Tenha em mente que o *mapreduce.reduce.memory.mb* não pode ser maior que o tamanho da memória física de cada nó de trabalho no cluster do Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

