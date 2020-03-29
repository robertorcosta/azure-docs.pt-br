---
title: Diferenças entre APIs de gestão e APIs de serviço - Azure Batch | Microsoft Docs
description: As APIs funcionam nas diferentes camadas do serviço Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672753"
---
# <a name="service-level-and-management-level-apis"></a>APIs de nível de serviço e nível de gerenciamento

O Azure Batch possui dois conjuntos de APIs, um para o nível de serviço e outro para o nível de gerenciamento. A nomeação é muitas vezes semelhante, mas eles retornam resultados diferentes. Se você deseja registros de atividades, então você precisa usar as APIs de gerenciamento. As APIs de nível de serviço contornam a camada de gerenciamento de recursos do Azure e não estão registradas.


O gerenciamento de lotes e o serviço de lote têm APIs para Pool, por exemplo. 
- Esta API para excluir pool é direcionada diretamente na conta de lote:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Esta API para https://docs.microsoft.com/rest/api/batchmanagement/pool/delete excluir pool é direcionada à camada management.azure.com.

