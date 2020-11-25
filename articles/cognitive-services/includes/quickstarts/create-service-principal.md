---
title: Criar uma entidade de serviço do Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95095137"
---
## <a name="create-an-azure-service-principal"></a>Criar uma entidade de serviço do Azure

Para que o aplicativo interaja com sua conta do Azure, você precisa de uma entidade de serviço do Azure para gerenciar permissões. Siga as instruções em [Criar uma entidade de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Ao criar uma entidade de serviço, você verá que ela tem um valor secreto, uma ID e uma ID de aplicativo. Salve a ID do aplicativo e o segredo em um local temporário para etapas posteriores.