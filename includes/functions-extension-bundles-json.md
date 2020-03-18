---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
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

As propriedades a seguir estão disponíveis em `extensionBundle`:

| Propriedade | Descrição |
| -------- | ----------- |
| id | O namespace dos pacotes de extensão do Microsoft Azure Functions. |
| version | A versão do pacote a ser instalado. O runtime do Functions sempre escolhe a versão máxima permitida definida pelo intervalo de versões. O valor de versão acima permite todas as versões de pacote da 1.0.0 até a 2.0.0, mas sem incluir a última. Para obter mais informações, confira a [notação de intervalo para especificar intervalos de versões](/nuget/reference/package-versioning#version-ranges). |

As versões do pacote são incrementadas conforme os itens que o compõem são alterados. Alterações na versão principal ocorrem quando os pacotes são incrementados por uma versão principal. Normalmente, alterações na versão principal do pacote coincidem com uma alteração na versão principal do runtime do Functions.  

O conjunto atual de extensões instaladas pelo pacote padrão é enumerado neste [arquivo extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
