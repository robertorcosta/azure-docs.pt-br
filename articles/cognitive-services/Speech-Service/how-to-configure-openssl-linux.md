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
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331137"
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
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR é `/etc/pki/tls` (como em sistemas baseados em RHEL/CentOS). Há `certs` subdiretório com um arquivo de pacote de certificado, por exemplo `ca-bundle.crt`.
Defina a variável de ambiente `SSL_CERT_FILE` para apontar para esse arquivo antes de executar um programa que usa o SDK de fala. Por exemplo:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

> [!div class="nextstepaction"]
> [Sobre o SDK de fala](speech-sdk.md)
