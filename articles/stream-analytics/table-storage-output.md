---
title: Saída de armazenamento de tabela do Azure Stream Analytics
description: Este artigo descreve o armazenamento de tabelas do Azure como saída para Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 3e5b747e55c8f246167bcf8bbde3542146e69706
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891958"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Saída de armazenamento de tabela do Azure Stream Analytics

[Armazenamento de Tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O armazenamento de tabelas é um repositório de chave/atributo NoSQL da Microsoft, que você pode usar para dados estruturados com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de tabelas. |
| Conta de armazenamento |O nome da conta de armazenamento para a qual você está enviando a saída. |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da tabela. Caso ainda não exista, a tabela será criada. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador exclusivo para a partição em uma tabela que forma a primeira parte da chave primária da entidade. É um valor de cadeia de caracteres que pode ter um tamanho de até 1 KB. |
| Chave de linha |O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo de uma entidade em uma partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ter um tamanho de até 1 KB. |
| Tamanho do lote |É o número de registros para uma operação em lote. O padrão (100) é suficiente para a maioria dos trabalhos. Consulte [Especificação da operação de lote da tabela](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) para obter mais detalhes sobre como modificar essa configuração. |

## <a name="partitioning"></a>Particionamento

A chave de partição é qualquer coluna de saída. O número de gravadores de saída segue o particionamento de entrada para [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para obter o tamanho máximo da mensagem, consulte [limites de armazenamento do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). O padrão é 100 entidades por transação única, mas você pode configurá-lo para um valor menor, conforme necessário.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando a CLI do Azure](quick-create-azure-cli.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando um modelo do Resource Manager](quick-create-azure-resource-manager.md)
* [Início rápido: criar um trabalho de Stream Analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md)
