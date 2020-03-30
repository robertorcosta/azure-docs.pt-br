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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780226"
---
Para ler dados no armazenamento de arquivo morto, primeiro você deve alterar a camada do blob para quente ou fria. Esse processo é conhecido como reidratação e pode levar horas para ser concluído. Recomendamos grandes tamanhos de bolha para um ótimo desempenho de reidratação. Reidratar vários blobs pequenos simultaneamente pode inserir um tempo adicional. Atualmente, existem duas prioridades de reidratação, High (preview) e Standard, que podem ser definidas através da propriedade opcional *x-ms-reidratar-prioridade* em uma operação [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) ou [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Prioridade padrão**: A solicitação de reidratação será processada na ordem recebida e pode levar até 15 horas.
* **Alta prioridade (visualização):** A solicitação de reidratação será priorizada sobre as solicitações padrão e poderá terminar em menos de 1 hora. A alta prioridade pode levar mais de 1 hora, dependendo do tamanho da bolha e da demanda atual. Solicitações de alta prioridade são garantidas para serem priorizadas em relação às solicitações de prioridade padrão.

> [!NOTE]
> A prioridade padrão é a opção padrão de reidratação para arquivamento. Alta prioridade é uma opção mais rápida que custará mais do que a reidratação prioritária padrão e geralmente é reservada para uso em situações de restauração de dados de emergência.

Uma vez iniciado um pedido de reidratação, ele não pode ser cancelado. Durante a reidratação, você pode verificar a propriedade de blob status do *arquivo* para confirmar se o nível foi alterado. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade de status do arquivo é removida, e a propriedade *Access Tier* blob reflete o novo nível quente ou legal.
