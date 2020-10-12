---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556207"
---
<!-- List and set subscriptions -->

1. Obtenha a lista de assinaturas usando o comando [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Use `az account set` com a ID da assinatura ou nome para o qual você deseja alterar.

    ```
    az account set --subscription "My Demos"
    ```
