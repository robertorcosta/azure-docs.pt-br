---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878231"
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

As seguintes propriedades estão disponíveis em `extensionBundle`:

| Propriedade | Descrição |
| -------- | ----------- |
| id | O namespace para pacotes de extensão do Microsoft Azure functions. |
| Versão | A versão do pacote a ser instalado. O tempo de execução do Functions sempre escolhe a versão máxima permitida definida pelo intervalo de versão ou intervalo. O valor da versão acima permite todas as versões do pacote da 1.0.0 até, mas não incluindo 2.0.0. Para obter mais informações, consulte a [notação de intervalo para especificar intervalos de versão](/nuget/reference/package-versioning#version-ranges). |

As versões do pacote são incrementadas como pacotes na alteração do pacote. As alterações de versão principal ocorrem quando os pacotes no pacote são incrementados por uma versão principal. As alterações de versão principais no pacote geralmente coincidem com uma alteração na versão principal do tempo de execução do functions.  

O conjunto atual de extensões instaladas pelo pacote padrão é enumerado neste [arquivo Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
