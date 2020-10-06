---
title: incluir arquivo
description: incluir arquivo
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665577"
---
O **Azure Data Lake Storage Gen2** não é um serviço dedicado ou um tipo de conta de armazenamento. Ele é a versão mais recente de funcionalidades dedicadas à análise de Big Data.  Essas funcionalidades estão disponíveis em uma conta de armazenamento de uso geral v2 ou de BlockBlobStorage e você pode obtê-las habilitando o recurso **Namespace hierárquico** da conta. Para destinos de escala, confira estes artigos. 

- [Destinos de escala para armazenamento de blobs](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage).
- [Destinos de escala para contas de armazenamento padrão](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts).

O **Azure Data Lake Storage Gen1** é um serviço dedicado. Ele é um repositório em hiper-escala corporativo para cargas de trabalho de análise de Big Data. Você pode usar o Data Lake Storage Gen1 para capturar dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias. Não há limite de quantidade de dados que podem ser armazenados em uma conta do Data Lake Storage Gen1.

| **Recurso** | **Limite** | **Comentários** |
| --- | --- | --- |
| Número máximo de contas do Data Lake Storage Gen1, por assinatura e por região |10 | Para solicitar um aumento desse limite, entre em contato com o suporte. |
| Número máximo de ACLs de acesso, por arquivo ou por pasta |32 | Este é um limite rígido. Usar grupos para gerenciar o acesso com menos entradas. |
| Número máximo de ACLs padrões, por arquivo ou por pasta |32 | Este é um limite rígido. Usar grupos para gerenciar o acesso com menos entradas. |
