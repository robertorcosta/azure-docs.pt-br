---
title: Configurar um certificado de criptografia em clusters do Windows
description: Saiba como configurar um certificado de criptografia e criptografar segredos em clusters Windows.
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d9413a37be221adc375836719dc1f467a5571fa0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610175"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Configurar um certificado de criptografia e criptografar segredos em clusters Windows
Este artigo mostra como configurar um certificado de criptografia e criptografar segredos em clusters Windows. Para clusters do Linux, consulte [configurar um certificado de criptografia e criptografar segredos em clusters do Linux.][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como uma maneira de obter certificados instalados em clusters Service Fabric no Azure. Se não estiver implantando no Azure, você não precisará usar o cofre de chaves para gerenciar segredos em aplicativos do Service Fabric. No entanto, o *uso* de segredos em um aplicativo é independente de plataforma de nuvem para permitir que os aplicativos sejam implantados em um cluster hospedado em qualquer lugar. 

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de codificação de dados
Um certificado de codificação de dados é usado estritamente para criptografia e descriptografia de [parâmetros][parameters-link] nas configurações. xml e [variáveis de ambiente][environment-variables-link] do serviço no Service manifest. XML de um serviço. Ele não é usado para autenticação ou assinatura do texto codificado. O certificado deve atender aos seguintes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O uso da chave de certificado deve incluir a Codificação de Dados (10) e não deve incluir a Autenticação de Servidor ou de Cliente. 
  
  Por exemplo, ao criar um certificado autoassinado usando o PowerShell, o sinalizador `KeyUsage` deverá ser definido como `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalar o certificado em seu cluster
Esse certificado deve ser instalado em cada nó no cluster. Consulte [como criar um cluster usando Azure Resource Manager][service-fabric-cluster-creation-via-arm] para obter instruções de instalação. 

## <a name="encrypt-application-secrets"></a>Criptografar segredos do aplicativo
O comando do PowerShell a seguir é usado para criptografar um segredo. Esse comando só criptografa o valor; ele **não** assina o texto da criptografia. Você deve usar o mesmo certificado de codificação instalado no seu cluster para produzir texto cifrado para valores do segredo:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

A cadeia de caracteres codificada de base 64 resultante contém tanto o texto cifrado secreto como informações sobre o certificado usado para criptografá-lo.

## <a name="next-steps"></a>Próximos passos
Saiba como [especificar segredos criptografados em um aplicativo.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
