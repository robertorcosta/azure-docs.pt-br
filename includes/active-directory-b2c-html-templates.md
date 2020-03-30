---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188998"
---
## <a name="sample-templates"></a>Modelos de exemplo
Encontre modelos de exemplo para personalização da interface do usuário aqui:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Este projeto contém os seguintes modelos:
- [Azul-do-Oceano](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Cinza ardósia](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Para usar a amostra:

1. Clone o repo em sua máquina local. Escolha uma `/ocean_blue` pasta `/slate_gray`de modelo ou .
1. Carregue todos os arquivos `/assets` a pasta de modelo e a pasta, para o armazenamento Blob, conforme descrito nas seções anteriores.
1. Em seguida, `\*.html` abra cada arquivo `/ocean_blue` `/slate_gray`na raiz de um ou , substitua todas as instâncias de URLs relativos pelos URLs dos arquivos css, imagens e fontes que você carregou na etapa 2. Por exemplo: 
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Para
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Salve `\*.html` os arquivos e carregue-os para o armazenamento Blob.
1. Agora modifique a diretiva, apontando para o seu arquivo HTML, como mencionado anteriormente.
1. Se você ver fontes, imagens ou CSS ausentes, verifique suas \*referências na diretiva de extensões e nos arquivos .html.
