---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: marsma
ms.openlocfilehash: 264604bfdf0c514e6464854f431addbc9d2dcdef
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373133"
---
## <a name="sample-templates"></a>Modelos de exemplo
Encontre modelos de exemplo para personalização da interface do usuário aqui:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Este projeto contém os seguintes modelos:
- [Azul-marinho](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Cinza-acinzentado](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Para usar o exemplo:

1. Clone o repositório em seu computador local. Escolha uma pasta de modelo `/ocean_blue` ou `/slate_gray`.
1. Carregue todos os arquivos na pasta modelo e na pasta `/assets`, no armazenamento de BLOBs, conforme descrito nas seções anteriores.
1. Em seguida, abra cada arquivo de `\*.html` na raiz de `/ocean_blue` ou `/slate_gray`, substitua todas as instâncias de URLs relativas pelas URLs do CSS, imagens e arquivos de fontes que você carregou na etapa 2. Por exemplo:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Para 
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Salve os arquivos de `\*.html` e carregue-os no armazenamento de BLOBs.
1. Agora, modifique a política, apontando para o arquivo HTML, conforme mencionado anteriormente.
1. Se você vir fontes, imagens ou CSS ausentes, verifique suas referências na política de extensões e os arquivos \*. html.
