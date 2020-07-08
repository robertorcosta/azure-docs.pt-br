---
title: incluir arquivo
description: incluir arquivo
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797098"
---
Uma tarefa em execução em lote do Azure pode produzir dados de saída quando ele é executado. Com frequência, os dados de saída precisam ser armazenados para a recuperação por outras tarefas no trabalho, o aplicativo cliente que executou o trabalho, ou ambos. As tarefas gravam dados de saída no sistema de arquivos de um nó de computação do Lote, mas todos os dados no nó serão perdidos quando ele for reimaginado ou quando o nó deixar o pool. As tarefas também podem ter um período de retenção de arquivo, após o qual os arquivos criados pela tarefa são excluídos. Por esses motivos, é importante manter a saída da tarefa que você vai precisar posteriormente para um repositório de dados, como o [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/).

Para opções de conta de armazenamento do Lote, confira [Contas do Lote e contas do Armazenamento do Azure](../articles/batch/accounts.md#azure-storage-accounts).
