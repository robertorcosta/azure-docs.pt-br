---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696050"
---
Esta seção explica como aplicar uma restrição de contexto de segurança (SCC). Para a versão de visualização, elas liberam as restrições de segurança. 

1. Baixe a restrição de contexto de segurança personalizada (SCC). Use um dos seguintes: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Bruto](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` O comando a seguir baixa o Arc-data-SCC. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Crie SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Aplique o SCC à conta de serviço.

   > [!NOTE]
   > Use o mesmo namespace aqui e no `azdata arc dc create` comando a seguir. O exemplo é `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```