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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559289"
---
| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para se conectar aos serviços de movimentação de dados na Fábrica de Dados Do Azure. |
| `*.frontend.clouddatahub.net` | 443            | Necessárias para que o runtime de integração auto-hospedada se conecte ao serviço do Data Factory. |
| `download.microsoft.com`    | 443            | Exigido pelo runtime de integração auto-hospedada para fazer o download das atualizações. Se você tiver desativado a atualização automática, você pode pular a configuração deste domínio. |
| `*.core.windows.net`          | 443            | Usada pelo runtime de integração auto-hospedada para se conectar à conta de armazenamento do Azure ao usar o recurso [cópia em etapas](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Obrigatório apenas quando você copiar de ou para O Banco de Dados SQL do Azure ou Azure SQL Data Warehouse e opcional de outra forma. Use o recurso de cópia em estágio para copiar dados para o SQL Database ou SQL Data Warehouse sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obrigatório apenas quando você copiar de ou para Azure Data Lake Store e opcional de outra forma. |
