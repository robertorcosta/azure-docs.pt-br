---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249941"
---
Um ID de recurso do Azure AD indica o público para o qual um token que é emitido pode ser usado para fornecer acesso a um recurso do Azure. No caso do Azure Storage, o ID de recurso pode ser específico para uma única conta de armazenamento, ou pode ser aplicado a qualquer conta de armazenamento. A tabela a seguir descreve os valores que você pode fornecer para o ID de recurso:

|ID de Recurso  |Descrição  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | O ponto final do serviço para uma determinada conta de armazenamento. Use esse valor para adquirir um token para autorizar solicitações a essa conta específica do Azure Storage e somente serviço. Substitua o valor entre parênteses com o nome da sua conta de armazenamento.      |
|`https://storage.azure.com/`     | Use para adquirir um token para autorizar solicitações a qualquer conta do Azure Storage.        |
