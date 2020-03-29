---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70274648"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configure uma variável de ambiente para autenticação

Os aplicativos precisam autenticar o acesso aos Serviços Cognitivos que utilizam. Para autenticar, recomendamos criar uma variável de ambiente para armazenar as chaves dos recursos do Azure. 

Depois de ter sua chave, escreva-a para uma nova variável de ambiente na máquina local executando o aplicativo. Para definir a variável de ambiente, abra uma janela de console e siga as instruções do seu sistema operacional. Substitua por `your-key` uma das chaves do seu recurso.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Depois de adicionar a variável de ambiente, talvez seja necessário reiniciar todos os programas em execução que precisarem ler a variável de ambiente, incluindo a janela do console. Por exemplo, se estiver usando o Visual Studio como seu editor, reinicie-o antes de executar o exemplo.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela de console para que as alterações entrem em vigor.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite seu .bash_profile e adicione a variável de ambiente:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela de console para que as alterações entrem em vigor.

***