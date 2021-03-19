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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577437"
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

Verifique se há `certs` subdiretório em OPENSSLDIR. No exemplo acima, seria `/usr/lib/ssl/certs` .

* Se houver `/usr/lib/ssl/certs` e ele contiver muitos arquivos de certificado individuais ( `.crt` com `.pem` extensão ou), não haverá necessidade de mais ações.

* Se OPENSSLDIR for algo mais que `/usr/lib/ssl` e/ou houver um único arquivo de pacote de certificado em vez de vários arquivos individuais, você precisará definir uma variável de ambiente SSL apropriada para indicar onde os certificados podem ser encontrados.

## <a name="examples"></a>Exemplos

- OPENSSLDIR é `/opt/ssl` . Há um `certs` subdiretório com muitos `.crt` arquivos ou `.pem` .
Defina a variável `SSL_CERT_DIR` de ambiente para apontar para `/opt/ssl/certs` antes de executar um programa que usa o SDK de fala. Por exemplo:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR é `/etc/pki/tls` (como em sistemas baseados em RHEL/CentOS). Há `certs` subdiretório com um arquivo de pacote de certificado, por exemplo `ca-bundle.crt` .
Defina a variável `SSL_CERT_FILE` de ambiente para apontar para esse arquivo antes de executar um programa que usa o SDK de fala. Por exemplo:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Verificações de revogação de certificado
Ao se conectar ao serviço de fala, o SDK de fala verificará se o certificado TLS usado pelo serviço de fala não foi revogado. Para conduzir essa verificação, o SDK de fala precisará de acesso aos pontos de distribuição da CRL para autoridades de certificação usadas pelo Azure. Uma lista de possíveis locais de download de CRL pode ser encontrada neste [documento](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes). Se um certificado tiver sido revogado ou a CRL não puder ser baixada, o SDK de fala abortará a conexão e gerará o evento cancelado.

No caso em que a rede na qual o SDK de fala está sendo usado está configurada de uma maneira que não permite acesso aos locais de download da CRL, a verificação da CRL pode ser desabilitada ou definida para não falhar se a CRL não puder ser recuperada. Essa configuração é feita por meio do objeto de configuração usado para criar um objeto de reconhecedor.

Para continuar com a conexão quando uma CRL não puder ser recuperada, defina a propriedade OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Quando definido como "true", será feita uma tentativa de recuperar a CRL e, se a recuperação for bem-sucedida, o certificado será verificado quanto à revogação, se a recuperação falhar, a conexão terá permissão para continuar.

Para desabilitar completamente as verificações de revogação de certificado, defina a propriedade OPENSSL_DISABLE_CRL_CHECK como "true".
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Também vale a pena observar que algumas distribuições do Linux não têm uma variável de ambiente TMP ou TMPDIR definida. Isso fará com que o SDK de fala Baixe a CRL (lista de certificados revogados) toda vez, em vez de armazenar em cache a CRL em disco para reutilização até que ela expire. Para melhorar o desempenho de conexão inicial, você pode [criar uma variável de ambiente chamada TMPDIR e defini-la como o caminho do diretório temporário escolhido.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sobre o SDK de Fala](speech-sdk.md)
