---
title: 'Notas sobre a versão: Azure Synapse Analytics (workspaces)'
description: Notas sobre a versão do Azure Synapse Analytics (workspaces)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: add5c89e83f33980803bf571239023859653c4f1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059607"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Notas sobre a versão do Azure Synapse Analytics (versão prévia)

Este artigo descreve as limitações e os problemas com o Azure Synapse Analytics (workspaces). Para obter informações relacionadas, confira [O que é o Azure Synapse Analytics (workspaces)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (workspaces) 

### <a name="azure-synapse-cli"></a>CLI do Azure Synapse

- Problema e impacto sobre o cliente: Workspaces criados pelo SDK não conseguem iniciar o Synapse Studio

- Solução alternativa: Conclua as seguintes etapas: 
  1.    Crie um workspace executando `az synapse workspace create`.
  2.    Extraia a ID da identidade gerenciada executando `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Adicione o workspace como função à conta de armazenamento executando ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`.
  4.    Adicione regra de firewall executando ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Próximas etapas

* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o Synapse Studio](quickstart-synapse-studio.md)
* [Criar um pool de SQL](quickstart-create-sql-pool-portal.md)
* [Usar o SQL sob demanda](quickstart-sql-on-demand.md)
* [Criar um Pool do Apache Spark](quickstart-create-apache-spark-pool-portal.md)