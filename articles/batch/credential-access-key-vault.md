---
title: Acessar com segurança o Key Vault com o Lote
description: Saiba como acessar programaticamente suas credenciais de Key Vault usando o lote do Microsoft Azure.
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 6ea248dd51ae6786b0e987dc31ca83b29277cd16
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961499"
---
# <a name="securely-access-key-vault-with-batch"></a>Acessar com segurança o Key Vault com o Lote

Neste artigo, você aprenderá a configurar os nós do lote para acessar com segurança as credenciais armazenadas no Azure Key Vault. Não há nenhum ponto em colocar suas credenciais de administrador em Key Vault e, em seguida, codificar as credenciais para acessar Key Vault de um script. A solução é usar um certificado que concede aos nós do lote acesso ao Key Vault. Com algumas etapas, podemos implementar o armazenamento de chaves seguro para o lote.

Para autenticar o Azure Key Vault em um nó de Lote, você precisará de:

- Uma credencial do Azure Active Directory (Azure AD)
- Um certificado
- Uma conta do lote
- Um pool do lote com pelo menos um nó

## <a name="obtain-a-certificate"></a>Obter um certificado

Se você ainda não tiver um certificado, a maneira mais fácil de obter um é gerar um certificado autoassinado usando a ferramenta de linha de comando `makecert`.

Normalmente, você pode encontrar `makecert` neste caminho: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Abra um prompt de comando como administrador e navegue até `makecert` usando o seguinte exemplo.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Em seguida, use a ferramenta `makecert` para criar arquivos de certificado autoassinados chamados `batchcertificate.cer` e `batchcertificate.pvk`. O CN (nome comum) usado não é importante para esse aplicativo, mas é útil fazer algo que informe qual a utilidade do certificado.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

O lote requer um arquivo `.pfx`. Use a ferramenta [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) para converter os arquivos `.cer` e `.pvk` criados pelo `makecert` em um único arquivo `.pfx`.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

O acesso a Key Vault é concedido a um **usuário** ou a uma **entidade de serviço**. Para acessar o Key Vault de forma programática, use uma entidade de serviço com o certificado que criamos na etapa anterior.

Para obter mais informações sobre entidades de serviço do Azure, consulte [Objetos de aplicativo e de entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> A entidade de serviço deve estar no mesmo locatário do Microsoft Azure Active Directory que o Key Vault.

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

As URLs para o aplicativo não são importantes, pois estamos apenas usando-as para acesso do Key Vault.

## <a name="grant-rights-to-key-vault"></a>Conceder direitos para Key Vault

A entidade de serviço criada na etapa anterior precisa de permissão para recuperar os segredos de Key Vault. A permissão pode ser concedida por meio do portal do Azure ou com o comando do PowerShell abaixo.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Atribuir um certificado de uma conta de Lote

Crie um pool do Lote, vá para a guia certificado no pool e atribua o certificado que você criou. O certificado agora está em todos os nós do Lote.

Em seguida, precisamos atribuir o certificado à conta do lote. A atribuição do certificado à conta nos permite atribuí-lo aos pools e, em seguida, aos nós. A maneira mais fácil de fazer isso é acessar sua conta do Lote no portal, navegar até **Certificados** e selecionar **Adicionar**. Carregue o arquivo `.pfx` que geramos em [Obter um certificado](#obtain-a-certificate) e forneça a senha. Depois de concluído, o certificado é adicionado à lista e você pode verificar a impressão digital.

Agora, ao criar um pool do lote, você pode navegar até **Certificados** dentro do pool e atribuir o certificado que você criou a esse pool. Ao fazer isso, certifique-se de selecionar **LocalMachine** para o local de armazenamento. O certificado é carregado em todos os nós do Lote no pool.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

Se você planeja acessar Key Vault usando scripts do PowerShell em seus nós, precisará da biblioteca Azure PowerShell instalada. Há algumas maneiras de fazer isso, se os seus nós tiverem o WMF (Windows Management Framework) 5 instalado, você poderá usar o comando install-module para baixá-lo. Se você estiver usando nós que não têm o WMF 5, a maneira mais fácil de instalá-lo é agrupar o arquivo `.msi` do Azure PowerShell com seus arquivos em lotes e, em seguida, chamar o instalador como a primeira parte do script de inicialização do lote. Consulte este exemplo para obter detalhes:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Acessar Key Vault

Agora, está tudo configurado para acessar o Key Vault em scripts em execução em nós do Lote. Para acessar o Key Vault de um script, tudo o que você precisa é que seu script seja autenticado no Microsoft Azure Active Directory usando o certificado. Para fazer isso no PowerShell, use os comandos de exemplo a seguir. Especifique o GUID apropriado para **impressão digital**, **ID do aplicativo** (a ID da entidade de serviço) e a **ID do locatário** (o locatário onde a entidade de serviço existe).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Depois de autenticado, acesse o KeyVault como você faria normalmente.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Essas são as credenciais a serem usadas no seu script.
