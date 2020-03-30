---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79382022"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

As seguintes propriedades `extensionBundle`estão disponíveis em :

| Propriedade | Descrição |
| -------- | ----------- |
| id | O namespace para pacotes de extensão Microsoft Azure Functions. |
| version | A versão do pacote para instalar. O tempo de execução functions sempre escolhe a versão máxima permitida definida pelo intervalo ou intervalo da versão. O valor da versão acima permite todas as versões do pacote de 1.0.0 até, mas sem incluir 2.0.0. Para obter mais informações, consulte a [notação de intervalo para especificar faixas de versão](/nuget/reference/package-versioning#version-ranges). |

As versões do bundle incrementam como pacotes no pacote de mudança. As principais alterações na versão ocorrem quando os pacotes no incremento do pacote por uma versão principal. As principais alterações de versão no pacote geralmente coincidem com uma alteração na versão principal do tempo de execução functions.  

O conjunto atual de extensões instaladas pelo pacote padrão é enumerado neste [arquivo extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
