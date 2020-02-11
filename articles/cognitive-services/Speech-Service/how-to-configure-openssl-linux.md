---
title: Como configurar o OpenSSL para Linux
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o OpenSSL para Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: ff8772f7c3c3213c010b0bdbd0d0aa8897404bac
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119990"
---
# <a name="configure-openssl-for-linux"></a>Configurar o OpenSSL para Linux

Ao usar qualquer versão do SDK de fala anterior ao 1.9.0, o [OpenSSL](https://www.openssl.org) é configurado dinamicamente para a versão do sistema host. Em versões posteriores do SDK do Speech, o OpenSSL (versão [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) é vinculado estaticamente à biblioteca principal do SDK de fala.

Para garantir a conectividade, verifique se os certificados OpenSSL foram instalados no sistema. Execute um comando:
```bash
openssl version -d
```

A saída em sistemas baseados no Ubuntu/Debian deve ser:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Verifique se há `certs` subdiretório em OPENSSLDIR. No exemplo acima, seria `/usr/lib/ssl/certs`.

* Se houver `/usr/lib/ssl/certs` e ele contiver muitos arquivos de certificado individuais (com `.crt` ou `.pem` extensão), não haverá necessidade de mais ações.

* Se OPENSSLDIR for algo mais que `/usr/lib/ssl` e/ou houver um único arquivo de pacote de certificado em vez de vários arquivos individuais, você precisará definir uma variável de ambiente SSL apropriada para indicar onde os certificados podem ser encontrados.

## <a name="examples"></a>Exemplos

- OPENSSLDIR é `/opt/ssl`. Há `certs` subdiretório com muitos arquivos `.crt` ou `.pem`.
Defina a variável de ambiente `SSL_CERT_DIR` para apontar para `/opt/ssl/certs` antes de executar um programa que usa o SDK de fala. Por exemplo:
```bash
SSL_CERT_DIR=/opt/ssl/certs ./helloworld
```

- OPENSSLDIR é `/etc/pki/tls`. Há um arquivo de pacote de certificado, por exemplo `ca-bundle.pem` ou `ca-bundle.crt`.
Defina a variável de ambiente `SSL_CERT_FILE` para apontar para `/etc/pki/tls/ca-bundle.pem` antes de executar um programa que usa o SDK de fala. Por exemplo:
```bash
SSL_CERT_FILE=/etc/pki/tls/ca-bundle.pem ./helloworld
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sobre o SDK de fala](speech-sdk.md)
