---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 5e3c4622131528fc2c40a1510aeea3092018d182
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474274"
---
### <a name="install-via-composer"></a>Instalar por meio do Composer
1. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Baixe **[composer.phar][composer-phar]** na raiz do projeto.
3. Abra um prompt de comando e execute o seguinte comando na raiz do projeto
   
    ```
    php composer.phar install
    ```

Como alternativa acesse a [biblioteca de clientes PHP do Armazenamento do Azure][php-sdk-github] no GitHub para clonar o código-fonte.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
