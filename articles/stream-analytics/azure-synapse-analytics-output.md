---
title: Saída do Azure Synapse Analytics de Azure Stream Analytics
description: Este artigo descreve o Azure Synapse Analytics como saída para Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 04c315f593b90204faaeaec562c18e9e4be301d8
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90881886"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Saída do Azure Synapse Analytics de Azure Stream Analytics

O [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (anteriormente, SQL Data Warehouse) é um serviço de análise ilimitado que reúne o data warehouse empresarial e a análise de Big Data. 

Os trabalhos do Azure Stream Analytics podem gerar uma saída para uma tabela do pool de SQL no Azure Synapse Analytics e podem processar taxas de transferência de até 200 MB/s, o que dá suporte às mais exigentes análises em tempo real e necessidades de processamento de dados de caminho crítico para cargas de trabalho, como relatórios e painéis.  

A tabela do pool de SQL deve existir antes que você possa adicioná-la como uma saída ao seu trabalho do Stream Analytics. O esquema da tabela deve corresponder aos campos e seus tipos na saída de trabalho. 

Para usar o Azure Synapse como saída, é preciso garantir que a conta de armazenamento esteja configurada. Navegue até as configurações da Conta de armazenamento para configurá-la. São permitidos apenas os tipos de conta de armazenamento com suporte para tabelas: Uso geral V2 e Uso geral V1. Selecione somente a camada Standard. A camada Premium não é compatível.

## <a name="output-configuration"></a>Configuração de saída

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída do Azure Synapse Analytics.

|Nome da propriedade|Descrição|
|-|-|
|Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
|Banco de dados |Nome do pool de SQL ao qual você está enviando sua saída. |
|Nome do servidor |Nome do servidor do Azure Synapse.  |
|Nome de Usuário |O nome de usuário que tem acesso de gravação ao banco de dados. O Stream Analytics tem suporte apenas à autenticação do SQL. |
|Senha |A senha para se conectar ao banco de dados. |
|Tabela  | O nome da tabela em que a saída é gravada. O nome da tabela diferencia maiúsculas de minúsculas. O esquema dessa tabela deve corresponder exatamente ao número de campos e tipos gerados pela sua saída de trabalho.|

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando a CLI do Azure](quick-create-azure-cli.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando um modelo do Resource Manager](quick-create-azure-resource-manager.md)
* [Início rápido: criar um trabalho de Stream Analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md)