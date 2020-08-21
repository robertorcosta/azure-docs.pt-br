---
title: Exportar um certificado do Azure Key Vault
description: Exportar um certificado do Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588885"
---
# <a name="export-certificate-from-azure-key-vault"></a>Exportar um certificado do Azure Key Vault

O Azure Key Vault permite que você provisione, gerencie e implante facilmente certificados digitais para sua rede e habilite comunicações seguras para aplicativos. Para obter mais informações gerais sobre certificados, confira [Certificados do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

## <a name="about-azure-key-vault-certificate"></a>Sobre os certificados do Azure Key Vault

### <a name="composition-of-certificate"></a>Composição de um certificado
Quando um certificado de Cofre de Chaves é criado, uma chave endereçável e o segredo também são criados com o mesmo nome. A chave de Cofre de Chaves permite operações de chave e o segredo do Cofre de Chaves permite a recuperação do valor do certificado como um segredo. Um certificado do Cofre de Chaves também contém metadados do certificado x509 público. [Leia mais](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Chaves exportáveis ou não exportáveis
Quando um certificado de Key Vault é criado, ele pode ser recuperado do segredo endereçável com a chave privada no formato PFX ou PEM. A política usada para criar o certificado deve indicar que a chave é exportável. Se a política indicar não exportável, a chave privada não é uma parte do valor quando for recuperada como um segredo.

Há suporte para dois tipos de chave – RSA ou RSA HSM com certificados. Exportável só é permitido com RSA, RSA HSM não tem suporte. [Leia mais](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

O certificado armazenado no Azure Key Vault pode ser exportado por meio da CLI do Azure, do PowerShell ou do portal.

> [!NOTE]
> É importante observar que você só precisará da senha de um certificado ao importá-lo no cofre de chaves. O Key Vault não salvará a senha associada e, portanto, quando você exportar o certificado, a senha estará em branco.

## <a name="exporting-certificate-using-cli"></a>Como exportar o certificado usando a CLI
O comando a seguir permitirá que você baixe a **parte pública** de um certificado do Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Para obter exemplos e definições de parâmetros, [acesse aqui](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



Caso deseje baixar o certificado inteiro, ou seja, **as partes pública e privada da composição**, faça isso baixando o certificado como um segredo.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
Para obter definições de parâmetro, [acesse aqui](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>Como exportar o certificado usando o PowerShell

Esse comando obtém o certificado chamado TestCert01 do cofre de chaves chamado ContosoKV01. Para baixar o certificado como um arquivo PFX, execute o comando a seguir. Esses comandos acessam a SecretId e salvam o conteúdo como um arquivo PFX.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Isso exportará toda a cadeia de certificados com a chave privada, e o certificado será protegido por senha.
Para obter mais informações sobre parâmetros e o comando ```Get-AzKeyVaultCertificate```, veja o [Exemplo 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)

## <a name="exporting-certificate-using-portal"></a>Como exportar o certificado usando o portal

No portal, ao criar/importar um certificado na folha Certificado, você receberá a notificação de que o certificado foi criado com êxito. Ao selecionar o certificado, clique na versão atual para ver a opção de download.


Ao clicar no botão "Baixar no formato CER" ou "Baixar no formato PFX/PEM", você poderá baixar o certificado.


![Download de certificado](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>Como exportar o Certificado do Serviço de Aplicativo do cofre de chaves

Os Certificados do Serviço de Aplicativo do Azure proporcionam uma forma conveniente de comprar certificados SSL e atribuí-los aos Aplicativos Azure diretamente no portal. Esses certificados também podem ser exportados do portal como arquivos PFX a serem usados em outro lugar.
Depois de importados, os certificados do serviço de aplicativo podem ser **localizados nos segredos**.

Para obter as etapas necessárias para exportar os certificados do serviço de aplicativo, [leia aqui](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>Leia mais
* [Vários tipos de arquivo de certificado e definições](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)