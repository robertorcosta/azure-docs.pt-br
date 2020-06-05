---
title: Diferenças entre APIs de gerenciamento e APIs de serviço
description: As APIs funcionam nas diferentes camadas do serviço de Lote do Azure.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867584"
---
# <a name="service-level-and-management-level-apis"></a>APIs de nível de serviço e de nível de gerenciamento

O Lote do Azure tem dois conjuntos de APIs, um para o nível de serviço e o outro para o nível de gerenciamento. A nomenclatura costuma ser semelhante, mas retorna resultados diferentes. Se você deseja logs de atividade, deve usar as APIs de gerenciamento. As APIs de nível de serviço ignoram a camada de Gerenciamento de Recursos do Azure e não são registradas.


Tanto o gerenciamento quanto o serviço do Lote têm APIs para pool, por exemplo. 
- A API para exclusão do pool é direcionada diretamente para a conta do lote:  https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- A API para excluir o pool https://docs.microsoft.com/rest/api/batchmanagement/pool/delete é destinada à camada management.azure.com.

