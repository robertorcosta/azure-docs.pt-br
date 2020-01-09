---
title: Configurar um certificado de criptografia em clusters do Linux
description: Saiba como configurar um certificado de criptografia e criptografar segredos em clusters Linux.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 350718e4ce890fcbfaa7f2b10cc4c47dfac4da90
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614699"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Configurar um certificado de criptografia e criptografar segredos em clusters Linux
Este artigo mostra como configurar um certificado de criptografia e criptografar segredos em clusters Linux. Para clusters do Windows, consulte [configurar um certificado de criptografia e criptografar segredos em clusters do Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de codificação de dados
Um certificado de codificação de dados é usado estritamente para criptografia e descriptografia de [parâmetros][parameters-link] nas configurações. xml e [variáveis de ambiente][environment-variables-link] do serviço no Service manifest. XML de um serviço. Ele não é usado para autenticação ou assinatura do texto codificado. O certificado deve atender aos seguintes requisitos:

* O certificado deve conter uma chave privada.
* O uso da chave de certificado deve incluir a Codificação de Dados (10) e não deve incluir a Autenticação de Servidor ou de Cliente.

  Por exemplo, os comandos a seguir podem ser usados para gerar o certificado necessário usando OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalar o certificado em seu cluster
O certificado precisa ser instalado em cada nó no cluster em `/var/lib/sfcerts`. A conta de usuário na qual o serviço está em execução (sfuser por padrão) **deve ter acesso de leitura** ao certificado instalado (ou seja, `/var/lib/sfcerts/TestCert.pem` no exemplo atual).

## <a name="encrypt-secrets"></a>Criptografar segredos
O snippet a seguir pode ser usado para criptografar um segredo. Esse snippet só criptografa o valor; ele **não** assina o texto da codificação. **Você precisa usar** o mesmo certificado de codificação instalado no seu cluster para produzir texto codificado para valores do segredo.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
A cadeia de caracteres codificada de base 64 resultante que é gerada para encrypted.txt contém tanto o texto cifrado secreto como informações sobre o certificado usado para criptografá-lo. Você pode verificar sua validade descriptografando-a com OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Próximos passos
Saiba como [especificar segredos criptografados em um aplicativo.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
