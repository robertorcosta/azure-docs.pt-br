---
title: Sem servidor
description: Este artigo descreve a nova camada de computação sem servidor e a compara com a camada existente de computação provisionada
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 4/3/2020
ms.openlocfilehash: 6a1d2f6079280002c868702a6547c8fd359a7c21
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310116"
---
# <a name="azure-sql-database-serverless"></a>Banco de dados Azure SQL sem servidor

O Azure SQL Database serverless é um nível de computação para bancos de dados únicos que dimensiona automaticamente a computação com base na demanda de carga de trabalho e nas contas da quantidade de cálculo usada por segundo. O nível de computação sem servidor também pausa automaticamente os bancos de dados durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

## <a name="serverless-compute-tier"></a>Camada de computação sem servidor

O nível de computação sem servidor para um único banco de dados é parametrizado por uma faixa de autodimensionamento computado e um atraso de pausa automática.  A configuração desses parâmetros molda a experiência de desempenho do banco de dados e o custo de computação.

![cobrança sem servidor](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configuração de desempenho

- Os **vCores mínimos** e **os vCores máximos** são parâmetros configuráveis que definem a gama de capacidade de computação disponível para o banco de dados. Os limites de memória e E/S são proporcionais ao intervalo de vCore especificado.  
- O **atraso de pausa automática** é um parâmetro configurável que define o período de tempo que o banco de dados deve estar inativo antes de ser automaticamente pausado. O banco de dados é automaticamente retomado quando o próximo login ou outra atividade ocorrer.  Alternativamente, a autopausa pode ser desativada.

### <a name="cost"></a>Custo

- O custo para um banco de dados sem servidor é a soma do custo de computação e custo de armazenamento.
- Quando o uso da computação está entre os limites min e máximo configurados, o custo de computação é baseado no vCore e na memória usada.
- Quando o uso da computação está abaixo dos limites min configurados, o custo de computação é baseado nos vCores min e na memória min configuradas.
- Quando o banco de dados é pausado, o custo de computação é zero e apenas os custos de armazenamento são incorridos.
- O custo de armazenamento é determinado da mesma forma que no nível de computação provisionado.

Para obter mais detalhes de custo, consulte [Billing](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Cenários

A camada sem servidor é otimizada para preço/desempenho para bancos de dados individuais com padrões de uso intermitentes e imprevisíveis que podem gerar atraso no aquecimento de computação após períodos ociosos. Em contraste, o nível de computação provisionado é otimizado para bancos de dados únicos ou vários bancos de dados em pools elásticos com uso médio mais alto que não pode permitir qualquer atraso no aquecimento da computação.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Cenários adequados para a computação sem servidor

- Bancos de dados únicos com padrões de uso intermitentes e imprevisíveis intercalados com períodos de inatividade e menor utilização média de computação ao longo do tempo.
- Bancos de dados únicos no nível de computação provisionado que são frequentemente redimensionados e clientes que preferem delegar redimensionamento computacional para o serviço.
- Novos bancos de dados únicos sem histórico de uso onde o dimensionamento de cálculo é difícil ou não é possível estimar antes da implantação no Banco de Dados SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Cenários adequados para computação provisionada

- Bancos de dados únicos com padrões de uso mais regulares e previsíveis e maior utilização média de computação ao longo do tempo.
- Bancos de dados que não podem tolerar compensações de desempenho resultantes do corte mais frequente de memória ou do atraso de retomada automática após uma pausa.
- Vários bancos de dados com padrões de uso intermitentes e imprevisíveis que podem ser consolidados em piscinas elásticas para uma melhor otimização de desempenho de preço.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparação com a camada de computação provisionada

A tabela a seguir resume as diferenças entre a camada de computação sem servidor e a camada de computação provisionada:

| | **Computação sem servidor** | **Computação provisionada** |
|:---|:---|:---|
|**Padrão de uso do banco de dados**| Uso intermitente e imprevisível com menor utilização média de computação ao longo do tempo. |  Padrões de uso mais regulares com maior utilização média de computação ao longo do tempo, ou vários bancos de dados usando piscinas elásticas.|
| **Esforço de gerenciamento de desempenho** |Inferior|Superior|
|**Dimensionamento de computação**|Automático|Manual|
|**Capacidade de resposta de computação**|Inferior após períodos de inatividade|Imediata|
|**Granularidade de cobrança**|Por segundo|Por hora|

## <a name="purchasing-model-and-service-tier"></a>Camada de serviço e modelo de compra

Atualmente, o Banco de dados SQL sem servidor é compatível apenas com a camada de uso geral da Geração 5 de hardware no modelo de compra de vCore.

## <a name="autoscaling"></a>Dimensionamento automático

### <a name="scaling-responsiveness"></a>Capacidade de resposta de dimensionamento

Em geral, os bancos de dados sem servidor são executados em uma máquina com capacidade suficiente para satisfazer a demanda de recursos sem interrupção para qualquer quantidade de computação solicitada dentro dos limites estabelecidos pelo valor máximo dos vCores. Ocasionalmente, o balanceamento de carga ocorrerá automaticamente se o computador não puder atender à demanda de recursos dentro de alguns minutos. Por exemplo, se a demanda de recursos for de 4 vCores, mas apenas 2 vCores estiverem disponíveis, então pode levar até alguns minutos para carregar o saldo antes que 4 vCores sejam fornecidos. O banco de dados permanece online durante o balanceamento de carga, exceto por um breve período ao final da operação, quando as conexões são perdidas.

### <a name="memory-management"></a>Gerenciamento de memória

A memória para bancos de dados sem servidor é recuperada com mais freqüência do que para bancos de dados de computação provisionados. Esse comportamento é importante para controlar os custos sem servidor e pode impactar o desempenho.

#### <a name="cache-reclamation"></a>Recuperação de cache

Ao contrário dos bancos de dados de computação provisionados, a memória do cache SQL é recuperada de um banco de dados sem servidor quando a utilização de CPU ou cache é baixa.

- A utilização do cache é considerada baixa quando o tamanho total das entradas de cache usadas mais recentemente fica abaixo de um limite por um período de tempo.
- Quando a recuperação de cache é acionada, o tamanho do cache de destino é reduzido incrementalmente a uma fração do seu tamanho anterior e a recuperação só continua se o uso permanecer baixo.
- Quando ocorre a recuperação de cache, a política de seleção de entradas de cache para despejo é a mesma política de seleção que para bancos de dados de computação provisionados quando a pressão de memória é alta.
- O tamanho do cache nunca é reduzido abaixo do limite de memória min, como definido por min vCores que podem ser configurados.

Em bancos de dados de computação sem servidor e provisionados, as entradas de cache podem ser despejadas se toda a memória disponível for usada.

#### <a name="cache-hydration"></a>Hidratação em cache

O cache SQL cresce à medida que os dados são obtidos do disco da mesma forma e com a mesma velocidade dos bancos de dados provisionados. Quando o banco de dados está ocupado, o cache é permitido crescer sem restrições até o limite máximo de memória.

## <a name="autopausing-and-autoresuming"></a>Autopausa e autoruming

### <a name="autopausing"></a>Autopausing

A autopausa é acionada se todas as seguintes condições forem verdadeiras durante a duração do atraso da pausa automática:

- Número de sessões = 0
- CPU = 0 para a carga de trabalho do usuário em execução no pool de usuários

Uma opção é fornecida para desativar a auto-pausa, se desejar.

Os seguintes recursos não suportam auto-pausa.  Ou seja, se algum dos seguintes recursos for usado, o banco de dados permanece on-line independentemente da duração da inatividade do banco de dados:

- Geo-replicação (georeplicação ativa e grupos de failover automático).
- LtR (Retenção de backup a longo prazo).
- O banco de dados de sincronização usado na sincronização de dados SQL.  Ao contrário dos bancos de dados de sincronização, os bancos de dados hub e membros suportam a auto-pausa.
- O banco de trabalho usado em trabalhos elásticos.

A autopausa é temporariamente impedida durante a implantação de algumas atualizações de serviço que exigem que o banco de dados esteja on-line.  Nesses casos, a autopausa torna-se permitida novamente assim que a atualização do serviço for concluída.

### <a name="autoresuming"></a>Autoresuming

O autoruming é acionado se alguma das seguintes condições for em vigor a qualquer momento:

|Recurso|Gatilho de retomada automática|
|---|---|
|Autenticação e autorização|Logon|
|Detecção de ameaças|Ativando/desativando as configurações de detecção de ameaças no nível do banco de dados ou do servidor.<br>Modificando as configurações de detecção de ameaças no nível do banco de dados ou do servidor.|
|Descoberta e classificação de dados|Adicionar, modificar, excluir ou exibir os rótulos de confidencialidade|
|Auditoria|Exibir os registros de auditoria.<br>Atualização ou visualização da política de auditoria.|
|Mascaramento de dados|Adicionar, modificar, excluir ou exibir as regras de mascaramento de dados|
|Transparent Data Encryption|Exibir o estado ou status de Transparent Data Encryption|
|Consultar o armazenamento de dados (desempenho)|Modificando ou visualizando configurações do armazenamento de consultas|
|Ajuste automático|Aplicação e verificação de recomendações de ajuste automático, como indexação automática|
|Cópia de banco de dados|Criar banco de dados como cópia.<br>Exportar para um arquivo BACPAC.|
|Sincronização de dados SQL|Sincronização entre bancos de dados membro e hub que são executados em um cronograma configurável ou são executados manualmente|
|Modificar alguns metadados do banco de dados|Adicionando novas tags de banco de dados.<br>Alterando vCores max, min vCores ou atraso de pausa automática.|
|SQL Server Management Studio (SSMS)|O uso de versões SSMS antes do 18.1 e a abertura de uma nova janela de consulta para qualquer banco de dados no servidor retomará qualquer banco de dados com pausa automática no mesmo servidor. Esse comportamento não ocorre se usar o SSMS versão 18.1 ou posterior.|

O monitoramento, o gerenciamento ou outras soluções que executam qualquer uma das operações listadas acima desencadearão a retomada automática.

O autoruming também é acionado durante a implantação de algumas atualizações de serviçoque exigem que o banco de dados esteja on-line.

### <a name="connectivity"></a>Conectividade

Se um banco de dados sem servidor for pausado, o primeiro login retomará o banco de dados e retornará um erro informando que o banco de dados está indisponível com o código de erro 40613. Depois que o banco de dados for retomado, o logon deve ser repetido para estabelecer a conectividade. Os clientes do banco de dados com lógica de repetição de conexão não devem ser modificados.

### <a name="latency"></a>Latency

A latência para autoresume e autopause um banco de dados sem servidor é geralmente ordem de 1 minuto para autoresume e 1-10 minutos para pausa automática.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Criptografia de dados transparente gerenciada pelo cliente (BYOK)

Se o uso da [criptografia de dados transparente gerenciada pelo cliente](transparent-data-encryption-byok-azure-sql.md) (BYOK) e o banco de dados sem servidor for pausado automaticamente quando ocorrer a exclusão ou revogação da chave, o banco de dados permanece no estado de pausa automática.  Neste caso, após a próxima retomada do banco de dados, o banco de dados fica inacessível em aproximadamente 10 minutos.  Uma vez que o banco de dados se torna inacessível, o processo de recuperação é o mesmo que para bancos de dados de computação provisionados.  Se o banco de dados sem servidor estiver on-line quando ocorrer a exclusão ou revogação da chave, o banco de dados também se tornará inacessível dentro de aproximadamente 10 minutos, da mesma forma que com bancos de dados de computação provisionados.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding em nível de computação sem servidor

Criar um novo banco de dados ou mover um banco de dados existente para um nível de computação sem servidor segue o mesmo padrão de criação de um novo banco de dados em nível de computação provisionado e envolve as duas etapas a seguir.

1. Especifique o objetivo de serviço. O objetivo de serviço prescreve o nível de serviço, a geração de hardware e os vCores max. A tabela a seguir mostra as opções de objetivo de serviço:

   |Nome do objetivo de serviço|Camada de serviço|Geração de hardware|Máximo de vCores|
   |---|---|---|---|
   |GP_S_Gen5_1|Uso Geral|Gen5|1|
   |GP_S_Gen5_2|Uso Geral|Gen5|2|
   |GP_S_Gen5_4|Uso Geral|Gen5|4|
   |GP_S_Gen5_6|Uso Geral|Gen5|6|
   |GP_S_Gen5_8|Uso Geral|Gen5|8|
   |GP_S_Gen5_10|Uso Geral|Gen5|10|
   |GP_S_Gen5_12|Uso Geral|Gen5|12|
   |GP_S_Gen5_14|Uso Geral|Gen5|14|
   |GP_S_Gen5_16|Uso Geral|Gen5|16|

2. Opcionalmente, especifique os vCores min e o atraso de pausa automática para alterar seus valores padrão. A tabela a seguir mostra os valores disponíveis para esses parâmetros.

   |Parâmetro|Opções de valor|Valor padrão|
   |---|---|---|---|
   |Min vCores|Depende do max vCores configurado - veja [os limites de recursos](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|vCores de 0,5|
   |Atraso de pausa automática|Mínimo: 60 minutos (1 hora)<br>Máximo: 10080 minutos (7 dias)<br>Incrementos: 10 minutos<br>Desabilitar pausa automática: -1|60 minutos|


### <a name="create-new-database-in-serverless-compute-tier"></a>Crie um novo banco de dados no nível de computação sem servidor 

Os exemplos a seguir criam um novo banco de dados no nível de computação sem servidor.

#### <a name="use-azure-portal"></a>Usar o portal do Azure

Ver [Quickstart: Crie um único banco de dados no Banco de Dados SQL do Azure usando o portal Azure](sql-database-single-database-get-started.md).


#### <a name="use-powershell"></a>Usar o PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Usar a CLI do Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Use Transact-SQL (T-SQL)

Ao usar o T-SQL, os valores padrão são aplicados para o atraso de vcores min e autopausa.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Para obter detalhes, consulte [CRIAR BANCO DE DADOS](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Mover banco de dados do nível de computação provisionado para o nível de computação sem servidor

Os exemplos a seguir movem um banco de dados do nível de computação provisionado para o nível de computação sem servidor.

#### <a name="use-powershell"></a>Usar o PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Usar a CLI do Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Use Transact-SQL (T-SQL)

Ao usar o T-SQL, os valores padrão são aplicados para o atraso de vcores min e autopausa.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Para obter detalhes, consulte [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Mover o banco de dados do nível de computação sem servidor para o nível de computação provisionado

Um banco de dados sem servidor pode ser movido para uma camada de computação provisionada da mesma forma utilizada para mover um banco de dados de computação provisionada para uma camada de computação sem servidor.

## <a name="modifying-serverless-configuration"></a>Modificando a configuração sem servidor

### <a name="use-powershell"></a>Usar o PowerShell

Modificar os vCores máximos ou mínimos e o atraso de pausa automática é realizado usando `MaxVcore`o `MinVcore`comando `AutoPauseDelayInMinutes` [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) no PowerShell usando os argumentos e argumentos.

### <a name="use-azure-cli"></a>Usar a CLI do Azure

Modificar os vCores máximos ou mínimos e o atraso de pausa automática é realizado usando o `min-capacity`comando `auto-pause-delay` [az sql db update](/cli/azure/sql/db#az-sql-db-update) no Azure CLI usando os `capacity`argumentos e argumentos.


## <a name="monitoring"></a>Monitoramento

### <a name="resources-used-and-billed"></a>Recursos usados e cobrados

Os recursos de um banco de dados sem servidor são encapsulados por pacote de aplicativos, instância SQL e entidades de pool de recursos do usuário.

#### <a name="app-package"></a>Pacote do Aplicativo

O pacote do aplicativo é o limite externo de gerenciamento de recursos de um banco de dados, independentemente de se o banco de dados está em uma camada de computação sem servidor ou provisionada. O pacote do aplicativo contém a instância do SQL e os serviços externos que, juntos, englobam todos os recursos de usuário e do sistema usados por um banco de dados no Banco de Dados SQL. R e pesquisa de texto completo são exemplos de serviços externos. Em geral, a instância do SQL domina a utilização geral de recursos no pacote do aplicativo.

#### <a name="user-resource-pool"></a>Pool de recursos do usuário

O pool de recursos do usuário é o limite interno de gerenciamento de recursos de um banco de dados, independentemente de se o banco de dados está em uma camada de computação sem servidor ou provisionada. O pool de recursos do usuário escopos de CPU e IO para a carga de trabalho do usuário gerada por consultas DDL, como consultas CREATE e ALTER e DML, como SELECIONAR, INSERIR, ATUALIZAR e EXCLUIR. Essas consultas geralmente representam a proporção mais substancial de utilização dentro do pacote do aplicativo.

### <a name="metrics"></a>Métricas

As métricas para monitorar o uso de recursos do pacote de aplicativos e do pool de usuários de um banco de dados sem servidor estão listadas na tabela a seguir:

|Entidade|Métrica|Descrição|Unidades|
|---|---|---|---|
|Pacote do Aplicativo|app_cpu_percent|Percentual de vCores usados pelo aplicativo em relação ao máximo de vCores permitido para o aplicativo.|Porcentagem|
|Pacote do Aplicativo|app_cpu_billed|A quantidade de computação cobrada para o aplicativo durante o período do relatório. O valor pago durante esse período é o produto dessa métrica e o preço unitário de vCore. <br><br>Os valores dessa métrica são determinados pela agregação ao longo do tempo do máximo de CPU usado e a memória usada por segundo. Se o valor usado for menor que a quantidade mínima provisionada conforme definido pelo mínimo de vCores e de memória, a quantidade mínima provisionada será cobrada.Para comparar CPU com memória para fins de cobrança, a memória é normalizada em unidades de vCores ao redimensionar a quantidade de memória em GB por 3 GB por vCore.|Segundos de vCore|
|Pacote do Aplicativo|app_memory_percent|Percentual de memória usada pelo aplicativo em relação ao máximo de memória permitida para o aplicativo.|Porcentagem|
|Pool de usuários|cpu_percent|Percentual de vCores usados pela carga de trabalho do usuário em relação ao máximo de vCores permitido para a carga de trabalho do usuário.|Porcentagem|
|Pool de usuários|data_IO_percent|Percentual de IOPS de dados usados pela carga de trabalho do usuário em relação ao máximo de IOPS de dados permitido para a carga de trabalho do usuário.|Porcentagem|
|Pool de usuários|log_IO_percent|Percentual de MB/s de log usados pela carga de trabalho do usuário em relação ao máximo de MB/s de log permitido para a carga de trabalho do usuário.|Porcentagem|
|Pool de usuários|workers_percent|Percentual de funções de trabalho usadas pela carga de trabalho do usuário em relação ao máximo de funções de trabalho permitidas para a carga de trabalho do usuário.|Porcentagem|
|Pool de usuários|sessions_percent|Percentual de sessões usadas pela carga de trabalho do usuário em relação ao máximo de sessões permitidas para a carga de trabalho do usuário.|Porcentagem|

### <a name="pause-and-resume-status"></a>Pausar e retomar status

No portal do Azure, o status do banco de dados é exibido no painel de visão geral do servidor que lista os bancos de dados que ele contém. O status do banco de dados também é exibido no painel de visão geral do banco de dados.

Usando os seguintes comandos para consultar a pausa e retomar o status de um banco de dados:

#### <a name="use-powershell"></a>Usar o PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Usar a CLI do Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limites de recursos

Para limites de recursos, consulte [o nível de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Cobrança

A quantidade de computação cobrada é a quantidade máxima de uso de CPU e memória usada por segundo. Se a quantidade de CPU e memória usada for menor que a quantidade mínima provisionada para cada uma delas, a quantidade provisionada será cobrada. Para comparar CPU com memória para fins de cobrança, a memória é normalizada em unidades de vCores ao redimensionar a quantidade de memória em GB por 3 GB por vCore.

- **Recurso faturado:** CPU e memória
- **Valor cobrado**: vCore unitário preço * max (min vCores, vCores usados, memória min GB * 1/3, memória GB usada * 1/3) 
- **Frequência de faturamento**: Por segundo

O preço unitário vCore é o custo por vCore por segundo. Consulte a [página de preços do Banco de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/) para obter os preços unitários específicos em uma determinada região.

A quantidade de computação cobrada é exposta pela métrica a seguir:

- **Métrica**: app_cpu_billed (segundos de vCore)
- **Definição**: máx. (mínimo de vCores, vCores usados, GB de memória mín. * 1/3, GB de memória usados * 1/3)
- **Frequência de relatórios**: Por minuto

Essa quantidade é calculada a cada segundo e agregada a cada 1 minuto.

Considere um banco de dados sem servidor configurado com 1 min vCore e 4 vCores max.  Isso corresponde a cerca de 3 GB de memória e memória máxima de 12 GB.  Suponha que o atraso de pausa automática esteja definido para 6 horas e a carga de trabalho do banco de dados esteja ativa durante as primeiras 2 horas de um período de 24 horas e de outra forma inativa.    

Neste caso, o banco de dados é cobrado por computação e armazenamento durante as primeiras 8 horas.  Embora o banco de dados esteja inativo a partir da segunda hora, ele ainda é cobrado para computação nas 6 horas subsequentes com base no cálculo mínimo provisionado enquanto o banco de dados estiver on-line.  Somente o armazenamento é cobrado durante o restante do período de 24 horas enquanto o banco de dados é pausado.

Mais precisamente, a conta de cálculo neste exemplo é calculada da seguinte forma:

|Intervalo de tempo|vCores usados a cada segundo|GB usado cada segundo|Dimensão computacional faturada|vCore segundos cobrados ao longo do intervalo de tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores usados|4 vCores * 3600 segundos = 14400 vCore segundos|
|1:00-2:00|1|12|Memória usada|12 GB * 1/3 * 3600 segundos = 14400 vCore segundos|
|2:00-8:00|0|0|Memória min provisionada|3 GB * 1/3 * 21600 segundos = 21600 vCore segundos|
|8:00-24:00|0|0|Sem cálculo cobrado enquanto pausado|0 vCore segundos|
|Total de segundos vCore faturados ao longo de 24 horas||||50400 vCore segundos|

Suponha que o preço unitário de computação seja de $0,000145/vCore/segundo.  Em seguida, o cálculo cobrado para este período de 24 horas é o produto do preço unitário de computação e vCore segundos cobrados: $0,000145/vCore/segundo * 50400 vCore seconds ~ $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Benefício híbrido do Azure e capacidade reservada

O Azure Hybrid Benefit (AHB) e os descontos de capacidade reservado não se aplicam ao nível de computação sem servidor.

## <a name="available-regions"></a>Regiões disponíveis

O nível de computação sem servidor está disponível em todo o mundo, exceto nas seguintes regiões: China East, China North, Germany Central, Germany Northeast, UK North, UK South 2, West Central US e US Gov Central (Iowa).

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Quickstart: Crie um único banco de dados no Banco de Dados SQL do Azure usando o portal Azure](sql-database-single-database-get-started.md).
- Para limites de recursos, consulte [Limites de recursos de camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
