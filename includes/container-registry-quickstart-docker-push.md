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
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67172478"
---
## <a name="push-image-to-registry"></a>Efetuar push de imagem para registro

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se você ainda não tiver as imagens de contêiner locais, execute o comando [docker pull][docker-pull] a seguir para efetuar pull de uma imagem existente do Docker Hub. Neste exemplo, extraia a imagem `hello-world`.

```
docker pull hello-world
```

Antes de poder enviar uma imagem por push no registro, você deve marcá-lo com o nome totalmente qualificado do seu servidor de logon ACR. O nome do servidor de logon está no formato *\<nome-do-registro\>.azurecr.io* (tudo em letras minúsculas), por exemplo, *mycontainerregistry007.azurecr.io*.

Marque a imagem usando o comando [docker tag][docker-tag]. Substitua o `<acrLoginServer>` pelo nome do servidor de logon da sua instância do ACR.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Por fim, use [docker push][docker-push] para enviar a imagem por push para a instância do ACR. Substitua o `<acrLoginServer>` pelo nome do servidor de logon da sua instância do ACR. Este exemplo cria o repositório **Olá, mundo**, que contém a imagem `hello-world:v1`.

```
docker push <acrLoginServer>/hello-world:v1
```

Depois de efetuar push da imagem no registro de contêiner, remova a imagem `hello-world:v1` de seu ambiente do Docker local. (Observe que esse comando [docker rmi][docker-rmi] não remove a imagem do repositório **hello-world** no registro de contêiner do Azure.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

