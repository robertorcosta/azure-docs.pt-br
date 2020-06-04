---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "67704258"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Use a CLI do Docker para autenticar o registro de contêiner privado

É possível autenticar com o registro de contêiner privado para Contêineres de Serviços Cognitivos, mas o método recomendado por meio da linha de comando é usando a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Use o [ `docker login` comando](https://docs.docker.com/engine/reference/commandline/login/), conforme mostrado no exemplo a seguir, para fazer logon no `containerpreview.azurecr.io` , o registro de contêiner privado para contêineres de serviços cognitivas. Substitua * \< username \> * pelo nome de usuário e * \< senha \> * pela senha fornecida nas credenciais que você recebeu da equipe de serviços cognitivas do Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se você tiver protegido suas credenciais em um arquivo de texto, poderá concatenar o conteúdo desse arquivo de texto, usando o comando `cat` para o comando `docker login`, conforme mostrado no exemplo a seguir. Substitua * \< passwordfile \> * pelo caminho e nome do arquivo de texto que contém a * \< \> senha e o nome de* usuário pelo que é fornecido em suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
