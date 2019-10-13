---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285594"
---
| Nomes de domínio                  | Portas de saída | DESCRIÇÃO                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Necessárias para que o tempo de execução de integração auto-hospedado se conecte aos serviços de movimentação de dados no Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Necessárias para que o tempo de execução de integração auto-hospedado se conecte ao serviço do Data Factory. |
| `download.microsoft.com`    | 443            | Exigido pelo tempo de execução da integração auto-hospedada para fazer o download das atualizações. Se tiver desabilitado a atualização automática, você pode ignorar isso. |
| `*.core.windows.net`          | 443            | Usada pelo tempo de execução de integração auto-hospedado para se conectar à conta de armazenamento do Azure ao usar o recurso [cópia em etapas](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | (Opcional) Necessária ao copiar de ou para o Banco de Dados SQL do Azure ou SQL Data Warehouse do Azure. Use o recurso de cópia em etapas para copiar dados para o Banco de Dados SQL do Azure ou SQL Data Warehouse do Azure sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Opcional) Necessária ao copiar de ou para o  Azure Data Lake Store. |
