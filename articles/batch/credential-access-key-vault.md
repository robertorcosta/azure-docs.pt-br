---
title: Acesse com segurança o Key Vault com lote - Lote Azure
description: Aprenda a acessar programáticamente suas credenciais do Key Vault usando o Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192295"
---
# <a name="securely-access-key-vault-with-batch"></a>Acessar com segurança o Key Vault com o Lote

Neste artigo, você aprenderá como configurar nós de lote para acessar com segurança as credenciais armazenadas no Azure Key Vault. Não adianta colocar suas credenciais de admin no Key Vault e, em seguida, credenciais de codificação difícil para acessar key vault a partir de um script. A solução é usar um certificado que concede aos seus nós batch acesso ao Key Vault. Com alguns passos, podemos implementar armazenamento de chaves seguras para batch.

Para autenticar o Azure Key Vault a partir de um nó Batch, você precisa:

- Uma credencial do Azure Active Directory (Azure AD)
- Um certificado
- Uma conta em lote
- Uma piscina de lote com pelo menos um nó

## <a name="obtain-a-certificate"></a>Obter um certificado

Se você ainda não tem um certificado, a maneira mais fácil de obter `makecert` um é gerar um certificado auto-assinado usando a ferramenta de linha de comando.

Você normalmente `makecert` pode encontrar `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`neste caminho: . Abra um prompt de comando `makecert` como administrador e navegue para usar o seguinte exemplo.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Em seguida, `makecert` use a ferramenta para `batchcertificate.cer` criar `batchcertificate.pvk`arquivos de certificado auto-assinados chamados e . O nome comum (CN) usado não é importante para este aplicativo, mas é útil para torná-lo algo que diga para que o certificado é usado.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

O lote `.pfx` requer um arquivo. Use a ferramenta [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) `.pvk` para converter `makecert` os `.cer` arquivos `.pfx` criados por um único arquivo.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

O acesso ao Key Vault é concedido a um **usuário** ou a um **diretor de serviço.** Para acessar o Key Vault de forma programática, use um diretor de serviço com o certificado que criamos na etapa anterior.

Para obter mais informações sobre os principais serviços do Azure, consulte [Os principais objetos de aplicação e serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> O diretor de serviço deve estar no mesmo inquilino azure AD que o Key Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

Os URLs para o aplicativo não são importantes, pois estamos usando-os apenas para acesso ao Key Vault.

## <a name="grant-rights-to-key-vault"></a>Conceder direitos ao Key Vault

O diretor de serviço criado na etapa anterior precisa de permissão para recuperar os segredos do Key Vault. A permissão pode ser concedida através do portal Azure ou com o comando PowerShell abaixo.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Atribuir um certificado a uma conta batch

Crie um pool de lotes e, em seguida, vá para a guia certificado no pool e atribua o certificado que você criou. O certificado está agora em todos os nós de lote.

Em seguida, precisamos atribuir o certificado à conta Batch. Atribuir o certificado à conta nos permite atribuí-lo aos pools e, em seguida, aos nós. A maneira mais fácil de fazer isso é ir para sua conta batch no portal, navegar para **Certificados**e selecionar **Adicionar**. Faça `.pfx` upload do arquivo que geramos no [Obter um certificado](#obtain-a-certificate) e forneça a senha. Uma vez concluído, o certificado é adicionado à lista e você pode verificar a impressão digital.

Agora, quando você cria um pool de lotes, você pode navegar até **Certificados** dentro do pool e atribuir o certificado que você criou para esse pool. Ao fazê-lo, certifique-se de selecionar **LocalMachine** para a localização da loja. O certificado é carregado em todos os nós de lote na piscina.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

Se você planeja acessar o Key Vault usando scripts PowerShell em seus nós, então você precisa da biblioteca Azure PowerShell instalada. Existem algumas maneiras de fazer isso, se seus nós tiverem o Windows Management Framework (WMF) 5 instalado, então você pode usar o comando install-module para baixá-lo. Se você estiver usando nós que não têm WMF 5, a maneira mais fácil de instalá-lo é empacotar o arquivo Azure PowerShell `.msi` com seus arquivos Batch e, em seguida, chamar o instalador como a primeira parte do seu script de inicialização Batch. Veja este exemplo para obter detalhes:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Acessar Key Vault

Agora estamos todos configurados para acessar Key Vault em scripts em execução em nós de lote. Para acessar o Key Vault a partir de um script, tudo o que você precisa é que seu script seja autenticado contra o Azure AD usando o certificado. Para fazer isso no PowerShell, use os seguintes comandos de exemplo. Especifique o GUID apropriado para **impressão digital,** **ID do aplicativo** (o ID do seu serviço principal) e **ID do inquilino** (o inquilino onde seu diretor de serviço existe).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Uma vez autenticado, acesse KeyVault como você normalmente faria.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Estas são as credenciais para usar em seu script.
