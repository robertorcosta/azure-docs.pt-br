---
title: Diferenças entre APIs de gerenciamento e APIs de serviço – lote do Azure | Microsoft Docs
description: As APIs funcionam nas diferentes camadas do serviço de lote do Azure.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672753"
---
# <a name="service-level-and-management-level-apis"></a>APIs de nível de serviço e gerenciamento

O lote do Azure tem dois conjuntos de APIs, um para o nível de serviço e outro para o nível de gerenciamento. A nomenclatura costuma ser semelhante, mas elas retornam resultados diferentes. Se você quiser logs de atividade, precisará usar as APIs de gerenciamento. As APIs de nível de serviço ignoram a camada de gerenciamento de recursos do Azure e não são registradas.


O gerenciamento de lote e o serviço de lote têm APIs para pool, por exemplo. 
- Essa API para excluir o pool é direcionada diretamente na conta do lote: https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Essa API para excluir o pool https://docs.microsoft.com/rest/api/batchmanagement/pool/delete é destinada à camada management.azure.com.

