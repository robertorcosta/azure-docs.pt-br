---
title: Saída do Azure Synapse Analytics de Azure Stream Analytics
description: Este artigo descreve o Azure Synapse Analytics como saída para Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 63d03559b2a30020ed2e8bd1cd030855b8fb20b7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445098"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Saída do Azure Synapse Analytics de Azure Stream Analytics

O [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) é um serviço de análise ilimitado que reúne o data warehousing corporativo e a análise de Big Data. 

Azure Stream Analytics trabalhos podem gerar uma saída para uma tabela de pools SQL dedicada no Azure Synapse Analytics e podem processar taxas de taxa de transferência de até 200 MB/s. Isso oferece suporte à análise em tempo real e às necessidades de processamento de dados de Hot-Path mais exigentes para cargas de trabalho, como relatórios e painéis.  

A tabela de pools SQL dedicada deve existir antes que você possa adicioná-la como saída para seu trabalho de Stream Analytics. O esquema da tabela deve corresponder aos campos e seus tipos na saída de trabalho. 

Para usar o Azure Synapse como saída, é preciso garantir que a conta de armazenamento esteja configurada. Navegue até as configurações da Conta de armazenamento para configurá-la. São permitidos apenas os tipos de conta de armazenamento com suporte para tabelas: Uso geral V2 e Uso geral V1. Selecione somente a camada Standard. A camada Premium não é compatível.

## <a name="output-configuration"></a>Configuração de saída

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída do Azure Synapse Analytics.

|Nome da propriedade|Descrição|
|-|-|
|Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
|Banco de dados |nome do pool de SQL dedicado no qual você está enviando a saída. |
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