---
title: Diferenças entre APIs de gerenciamento e APIs de serviço
description: As APIs funcionam nas diferentes camadas do serviço de lote do Azure.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115390"
---
# <a name="service-level-and-management-level-apis"></a>APIs de nível de serviço e gerenciamento

O lote do Azure tem dois conjuntos de APIs, um para o nível de serviço e outro para o nível de gerenciamento. A nomenclatura costuma ser semelhante, mas elas retornam resultados diferentes. Se você quiser logs de atividade, precisará usar as APIs de gerenciamento. As APIs de nível de serviço ignoram a camada de gerenciamento de recursos do Azure e não são registradas.


O gerenciamento de lote e o serviço de lote têm APIs para pool, por exemplo. 
- Essa API para excluir o pool é direcionada diretamente na conta do lote:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Essa API para excluir o https://docs.microsoft.com/rest/api/batchmanagement/pool/delete pool é direcionada à camada Management.Azure.com.

