---
title: 'Notas sobre a versão: Azure Synapse Analytics (versão prévia dos workspaces)'
description: Notas sobre a versão do Azure Synapse Analytics (versão prévia dos workspaces)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031663"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Notas sobre a versão do Azure Synapse Analytics (versão prévia dos workspaces)

Este artigo descreve as limitações e os problemas com o Azure Synapse Analytics (workspaces). Para obter informações relacionadas, confira [O que é o Azure Synapse Analytics (workspaces)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>CLI do Azure

- Problema e impacto sobre o cliente: Workspaces criados pelo SDK não conseguem iniciar o Synapse Studio

- Solução alternativa: Conclua as seguintes etapas: 
  1.    Crie um workspace executando `az synapse workspace create`.
  2.    Extraia a ID da identidade gerenciada executando `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Adicione o workspace como função à conta de armazenamento executando ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`.
  4.    Adicione regra de firewall executando ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Próximas etapas

* [O que é o Azure Synapse](overview-what-is.md)
* [Introdução](get-started.md)
* [perguntas frequentes](overview-faq.md)
