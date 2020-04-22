---
title: Notas de versão
description: Notas sobre a versão do Azure Synapse Analytics (workspaces)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419800"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Notas sobre a versão do Azure Synapse Analytics (versão prévia)

Este artigo descreve as limitações e os problemas com o Azure Synapse Analytics (workspaces). Para obter informações relacionadas, confira [O que é o Azure Synapse Analytics (workspaces)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (workspaces) 

### <a name="azure-synapse-cli"></a>CLI do Azure Synapse

- Problema e impacto sobre o cliente: Workspaces criados pelo SDK não conseguem iniciar o Synapse Studio

- Solução alternativa: Conclua as seguintes etapas: 
  1.    Crie um workspace executando `az synapse workspace create`2.    Extraia a ID da identidade gerenciada executando `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`
  3.    Adicione o workspace como função à conta de armazenamento executando ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`
  4.    Adicione uma regra de firewall executando ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `

## <a name="next-steps"></a>Próximas etapas

* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o Synapse Studio](quickstart-synapse-studio.md)
* [Criar um pool de SQL](quickstart-create-sql-pool.md)
* [Usar o SQL sob demanda](quickstart-sql-on-demand.md)
* [Criar um Pool do Apache Spark](quickstart-create-apache-spark-pool.md)