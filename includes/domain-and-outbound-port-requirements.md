---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 79a8bc73f416c8d10d83e7ad94a727094f072b00
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331423"
---
| Nomes de domínios                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo runtime de integração auto-hospedada para criação interativa. |
| `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` | 443            | Necessárias para que o runtime de integração auto-hospedada se conecte ao serviço do Data Factory. <br>Para um Data Factory recém-criado, localize o FQDN na sua chave de Runtime de Integração Auto-Hospedada que está no formato {datafactory}.{região}.datafactory.azure.net. Para o Data Factory antigo, se você não vir o FQDN na sua chave de Integração Auto-Hospedada, use *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Exigido pelo runtime de integração auto-hospedada para fazer o download das atualizações. Se você tiver desabilitado a atualização automática, poderá ignorar a configuração desse domínio. |
| `*.core.windows.net`          | 443            | Usada pelo runtime de integração auto-hospedada para se conectar à conta de armazenamento do Azure ao usar o recurso [cópia em etapas](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Obrigatório somente quando você copia do Banco de Dados SQL do Azure ou do Azure Synapse Analytics ou para ambos; caso contrário, opcional. Use o recurso de cópia em etapas para copiar dados para o Banco de Dados SQL ou Synapse Analytics sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obrigatório somente quando você copia do Azure Data Lake Storage ou para ele; caso contrário, opcional. |
