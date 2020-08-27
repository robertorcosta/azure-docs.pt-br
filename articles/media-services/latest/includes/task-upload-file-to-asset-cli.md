---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657007"
---
<!-- ### Upload files with the CLI -->

O comando a seguir carrega um arquivo individual.  

Altere os seguintes valores:

1. Altere `/path/to/file` para o caminho local do arquivo.  
1. Altere `MyContainer` para um ativo criado anteriormente usando a ID do ativo (não o nome).
1. Altere `MyBlob` para o nome que deseja usar para o arquivo carregado.
1. Altere `MyStorageAccountName` para o nome da conta de armazenamento que está sendo usada.
1. Altere `MyStorageAccountKey` para a chave de acesso da sua conta de armazenamento.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
