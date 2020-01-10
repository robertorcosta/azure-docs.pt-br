---
title: incluir arquivo
description: incluir arquivo
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780226"
---
Para ler dados no armazenamento de arquivo morto, primeiro você deve alterar a camada do blob para quente ou fria. Esse processo é conhecido como reidratação e pode levar horas para ser concluído. Recomendamos tamanhos de blob grandes para o desempenho ideal de reidratação. Reidratar vários blobs pequenos simultaneamente pode inserir um tempo adicional. Atualmente, há duas prioridades de reidratar, alta (visualização) e padrão, que podem ser definidas por meio da propriedade *x-MS-reidratar-Priority* opcional em uma operação de [definição](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) de BLOB ou de [cópia](https://docs.microsoft.com/rest/api/storageservices/copy-blob) de BLOB.

* **Prioridade padrão**: a solicitação reidratação será processada na ordem em que foi recebida e pode levar até 15 horas.
* **Alta prioridade (visualização)** : a solicitação reidratação será priorizada em relação às solicitações padrão e poderá ser concluída em menos de uma hora. A alta prioridade pode levar mais de 1 hora, dependendo do tamanho do blob e da demanda atual. As solicitações de alta prioridade têm garantia de serem priorizadas em relação às solicitações de prioridade padrão.

> [!NOTE]
> A prioridade padrão é a opção reidratação padrão para arquivamento. Alta prioridade é uma opção mais rápida que custará mais do que a reidratação de prioridade padrão e geralmente é reservada para uso em situações de restauração de dados de emergência.

Depois que uma solicitação reidratação é iniciada, ela não pode ser cancelada. Durante a reidratação, você pode marcar a propriedade de blob *Status do arquivo* para confirmar se a camada foi alterada. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade status do arquivo é removida, e a propriedade de blob de *Nível de Acesso* reflete a nova camada frequente ou esporádica.
