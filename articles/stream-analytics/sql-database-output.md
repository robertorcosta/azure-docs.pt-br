---
title: Saída do banco de dados SQL do Azure de Azure Stream Analytics
description: Este artigo descreve o banco de dados SQL do Azure como saída para Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d61cad0be25f5aa1a4c63bf3dc128196ad80fdad
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875560"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Saída do banco de dados SQL do Azure de Azure Stream Analytics

Você pode usar o [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) como uma saída para os dados que sejam relacionais por natureza ou para aplicativos que dependam da hospedagem do conteúdo em um banco de dados relacional. Azure Stream Analytics trabalhos gravam em uma tabela existente no banco de dados SQL. O esquema da tabela deve corresponder exatamente aos campos e seus tipos na saída de trabalho. Você também pode especificar um [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída por meio da opção de saída do Banco de Dados SQL. Para saber mais sobre as maneiras de melhorar a taxa de transferência de gravação, consulte o artigo [Stream Analytics com o Banco de Dados SQL do Azure como saída](stream-analytics-sql-output-perf.md).

Você também pode usar o [Azure SQL instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) como uma saída. Você precisa [configurar um ponto de extremidade público no SQL instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e, em seguida, definir manualmente as configurações a seguir em Azure Stream Analytics. A máquina virtual do Azure que executando o SQL Server com um banco de dados anexado também tem suporte da definição manual das configurações abaixo.

## <a name="output-configuration"></a>Configuração de saída

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de Banco de Dados SQL.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
| Banco de dados | O nome do banco de dados para o qual você está enviando sua saída. |
| Nome do servidor | O nome lógico do SQL Server ou o nome da instância gerenciada. Para o SQL Instância Gerenciada, é necessário especificar a porta 3342. Por exemplo, *sampleserver.public.database.windows.net,3342* |
| Nome de Usuário | O nome de usuário que tem acesso de gravação ao banco de dados. O Stream Analytics tem suporte apenas à autenticação do SQL. |
| Senha | A senha para se conectar ao banco de dados. |
| Tabela | O nome da tabela em que a saída é gravada. O nome da tabela diferencia maiúsculas de minúsculas. O esquema dessa tabela deve corresponder exatamente ao número de campos e tipos gerados pela sua saída de trabalho. |
|Herdar esquema de partição| Uma opção para herdar o esquema de partição da etapa da sua consulta anterior para possibilitar uma topologia totalmente paralela com vários gravadores na tabela. Para obter mais informações, confira [Saída do Azure Stream Analytics para Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md).|
|Contagem máxima do lote| O limite máximo recomendado do número de registros enviados com cada transação de inserção em massa.|

Há dois adaptadores que permitem a saída do Azure Stream Analytics para o Azure Synapse Analytics (anteriormente SQL Data Warehouse): Banco de Dados SQL e Azure Synapse. Recomendamos que você escolha o adaptador do Azure Synapse Analytics em vez do adaptador do banco de dados SQL se qualquer uma das condições a seguir for verdadeira:

* **Taxa de transferência**: se a taxa de transferência esperada agora ou no futuro for maior que 10 MB/s, use a opção de saída do Azure Synapse para melhorar o desempenho.

* **Partições de entrada**: caso tenha oito ou mais partições de entrada, use a opção de saída do Azure Synapse para uma expansão melhor.

## <a name="partitioning"></a>Particionamento

O particionamento precisa ser habilitado e baseado na cláusula PARTITION BY na consulta. Quando a opção herdar particionamento está habilitada, ela segue o particionamento de entrada para [consultas totalmente paralelizáveiss](stream-analytics-scale-jobs.md). Para saber mais sobre como obter melhor desempenho de taxa de transferência de gravação ao carregar dados no Banco de Dados SQL do Azure, consulte a [saída do Azure Stream Analytics para o Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída

Você pode configurar o tamanho máximo da mensagem usando a **contagem máxima de lotes**. O máximo padrão é 10.000 e o mínimo padrão é 100 linhas por inserção em massa única. Para obter mais informações, consulte [limites do SQL do Azure](../sql-database/sql-database-resource-limits.md). Inicialmente, cada lote é inserido em massa com a contagem máxima de lotes. O lote é dividido na metade (até a contagem de lote mínima) com base em erros de nova tentativa do SQL.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando a CLI do Azure](quick-create-azure-cli.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando um modelo do Resource Manager](quick-create-azure-resource-manager.md)
* [Início rápido: criar um trabalho de Stream Analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-vs-code.md)
