---
title: incluir arquivo
description: incluir arquivo
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684062"
---
Para ler dados no armazenamento de arquivo morto, primeiro você deve alterar a camada do blob para quente ou fria. Esse processo é conhecido como reidratação e pode levar horas para ser concluído. Recomendamos grandes tamanhos de bolha para um ótimo desempenho de reidratação. Reidratar vários blobs pequenos simultaneamente pode inserir um tempo adicional. Atualmente, existem duas prioridades de reidratação, High e Standard, que podem ser definidas através da propriedade opcional *x-ms-reidrat-priority* em uma operação [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) ou [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Prioridade padrão**: A solicitação de reidratação será processada na ordem recebida e pode levar até 15 horas.
* **Alta prioridade**: A solicitação de reidratação será priorizada em relação às solicitações padrão e poderá terminar em menos de 1 hora. A alta prioridade pode levar mais de 1 hora, dependendo do tamanho da bolha e da demanda atual. Solicitações de alta prioridade são garantidas para serem priorizadas em relação às solicitações de prioridade padrão.

> [!NOTE]
> A prioridade padrão é a opção padrão de reidratação para arquivamento. Alta prioridade é uma opção mais rápida que custará mais do que a reidratação prioritária padrão e geralmente é reservada para uso em situações de restauração de dados de emergência.

Uma vez iniciado um pedido de reidratação, ele não pode ser cancelado. Durante o processo de reidratação, a propriedade *de blob de nível de acesso x-ms* continuará a aparecer como arquivamento até que a reidratação seja concluída em um nível on-line. Para confirmar o estado de reidratação e o progresso, você pode chamar [As Propriedades Get Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) para verificar o *estado x-ms-archive-status* e as propriedades de blob *x-ms-reidrata-prioridade.* O status do arquivo pode ler "reidratar-pendente-a-quente" ou "reidratar-pendente-para-esfriar" dependendo do nível de destino reidratado. A prioridade de reidratar indicará a velocidade de "Alto" ou "Padrão". Após a conclusão, o status do arquivo e as propriedades de prioridade de reidratação são removidos, e a propriedade access tier blob será atualizada para refletir o nível quente ou legal selecionado.
