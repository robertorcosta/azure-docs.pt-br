---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f7b11f813232f593be3598a87b6f1ad7a57054b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023340"
---
| Nomes de domínios                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo runtime de integração auto-hospedada para criação interativa. |
| `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` | 443            | Necessárias para que o runtime de integração auto-hospedada se conecte ao serviço do Data Factory. <br>Para um Data Factory recém-criado, localize o FQDN na sua chave de Runtime de Integração Auto-Hospedada que está no formato {datafactory}.{região}.datafactory.azure.net. Para o Data Factory antigo, se você não vir o FQDN na sua chave de Integração Auto-Hospedada, use *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Exigido pelo runtime de integração auto-hospedada para fazer o download das atualizações. Se você tiver desabilitado a atualização automática, poderá ignorar a configuração desse domínio. |
| `*.core.windows.net`          | 443            | Usada pelo runtime de integração auto-hospedada para se conectar à conta de armazenamento do Azure ao usar o recurso [cópia em etapas](../articles/data-factory/copy-activity-performance.md#staged-copy). |
| `*.database.windows.net`      | 1433           | Obrigatório somente quando você copia do Banco de Dados SQL do Azure ou do Azure Synapse Analytics ou para ambos; caso contrário, opcional. Use o recurso de cópia em etapas para copiar dados para o Banco de Dados SQL ou Synapse Analytics sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obrigatório somente quando você copia do Azure Data Lake Storage ou para ele; caso contrário, opcional. |