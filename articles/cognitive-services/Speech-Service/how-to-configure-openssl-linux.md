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
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683158"
---
# <a name="configure-openssl-for-linux"></a>Configurar o OpenSSL para Linux

Ao usar qualquer versão do Speech SDK antes do 1.9.0, [o OpenSSL](https://www.openssl.org) é configurado dinamicamente para a versão do sistema host. Nas versões posteriores do Speech SDK, o OpenSSL (versão [1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) está estáticamente ligado à biblioteca central do Speech SDK.

Para garantir a conectividade, verifique se os certificados OpenSSL foram instalados em seu sistema. Execute um comando:
```bash
openssl version -d
```

A saída nos sistemas baseados no Ubuntu/Debian deve ser:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Verifique se `certs` há subdiretório em OPENSSLDIR. No exemplo acima, seria `/usr/lib/ssl/certs`.

* Se houver `/usr/lib/ssl/certs` e contiver muitos arquivos `.crt` `.pem` de certificados individuais (com ou extensão), não há necessidade de outras ações.

* Se O OPENSSLDIR `/usr/lib/ssl` é algo diferente e/ou há um único arquivo de pacote de certificados em vez de vários arquivos individuais, você precisa definir uma variável de ambiente SSL apropriada para indicar onde os certificados podem ser encontrados.

## <a name="examples"></a>Exemplos

- OPENSSLDIR `/opt/ssl`é . Há `certs` subdiretório com `.crt` muitos `.pem` ou arquivos.
Defina `SSL_CERT_DIR` a variável `/opt/ssl/certs` de ambiente a ponto antes de executar um programa que usa o Speech SDK. Por exemplo:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OpensSLDIR `/etc/pki/tls` é (como em sistemas baseados em RHEL/CentOS). Há `certs` subdiretório com um arquivo de `ca-bundle.crt`pacote de certificados, por exemplo .
Defina `SSL_CERT_FILE` a variável de ambiente para apontar para esse arquivo antes de executar um programa que usa o Speech SDK. Por exemplo:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Também vale a pena notar que algumas distribuições do Linux não possuem uma variável de ambiente TMP ou TMPDIR definida. Isso fará com que o Speech SDK baixe a Lista de Revogação de Certificados (CRL) todas as vezes, em vez de cachear o CRL em disco para reutilização até que eles expirem. Para melhorar o desempenho inicial da conexão, você pode [criar uma variável de ambiente chamada TMPDIR e defini-la no caminho do diretório temporário escolhido.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sobre o SDK de Fala](speech-sdk.md)
