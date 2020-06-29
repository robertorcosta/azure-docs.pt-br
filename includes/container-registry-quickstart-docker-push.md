---
title: incluir arquivo
description: incluir arquivo
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b10bf18fde850223bda80a597f448747558113f1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752182"
---
## <a name="push-image-to-registry"></a>Efetuar push de imagem para registro

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se você ainda não tiver as imagens de contêiner locais, execute o comando [docker pull][docker-pull] a seguir para efetuar pull de uma imagem existente do Docker Hub. Neste exemplo, extraia a imagem `hello-world`.

```
docker pull hello-world
```

Para efetuar push de uma imagem para o Registro, você precisa marcá-la com o nome totalmente qualificado de seu servidor de logon do Registro. O nome do servidor de logon está no formato *\<registry-name\>.azurecr.io* (tudo em letras minúsculas), por exemplo, *mycontainerregistry007.azurecr.io*.

Marque a imagem usando o comando [docker tag][docker-tag]. Substitua o `<login-server>` pelo nome do servidor de logon da sua instância do ACR.

```
docker tag hello-world <login-server>/hello-world:v1
```

Por fim, use [docker push][docker-push] para efetuar push da imagem para a instância do registro. Substitua o `<login-server>` pelo nome do servidor de logon de sua instância do registro. Este exemplo cria o repositório **Olá, mundo**, que contém a imagem `hello-world:v1`.

```
docker push <login-server>/hello-world:v1
```

Depois de efetuar push da imagem no registro de contêiner, remova a imagem `hello-world:v1` de seu ambiente do Docker local. (Observe que esse comando [docker rmi][docker-rmi] não remove a imagem do repositório **hello-world** no registro de contêiner do Azure.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

