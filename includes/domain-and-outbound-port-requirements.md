---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74559289"
---
| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para se conectar aos serviços de movimentação de dados no Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Necessárias para que o runtime de integração auto-hospedada se conecte ao serviço do Data Factory. |
| `download.microsoft.com`    | 443            | Exigido pelo runtime de integração auto-hospedada para fazer o download das atualizações. Se você tiver desabilitado a atualização automática, poderá ignorar a configuração desse domínio. |
| `*.core.windows.net`          | 443            | Usada pelo runtime de integração auto-hospedada para se conectar à conta de armazenamento do Azure ao usar o recurso [cópia em etapas](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Necessário somente quando você copia de ou para o banco de dados SQL do Azure ou o Azure SQL Data Warehouse e opcional caso contrário. Use o recurso de cópia em etapas para copiar dados para o banco de dados SQL ou SQL Data Warehouse sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Necessário somente quando você copia de ou para Azure Data Lake Store e opcional caso contrário. |
