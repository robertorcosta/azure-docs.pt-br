---
title: Rolar um certificado de cluster de malha de serviço do Azure
description: Saiba como rolar um certificado de cluster service fabric identificado pelo nome comum do certificado.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451972"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Sobrepor manualmente um certificado de cluster do Service Fabric
Quando um certificado de cluster do Service Fabric está perto de expirar, é preciso atualizar o certificado.  A sobreposição do certificado é simples se o cluster foi [configurado para usar certificados com base em nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (em vez de impressão digital).  Obtenha um novo certificado de uma autoridade de certificação com uma nova data de expiração.  Não há suporte para certificados autoassinados em clusters de produção do Service Fabric, para a inclusão dos certificados gerados durante o fluxo de trabalho de criação do Cluster no portal do Azure. O novo certificado deve ter o mesmo nome comum do certificado mais antigo. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O cluster do Service Fabric usará automaticamente o certificado declarado com um adicional na data de término futura quando houver mais de um certificado válido instalado no host. Uma melhor prática é usar um modelo do Resource Manager para provisionar recursos do Azure. Para um ambiente de não produção, o seguinte script poderá ser usado para carregar um novo certificado para um cofre de chaves e, em seguida, instalar o certificado no conjunto de dimensionamento de máquinas virtuais: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Calcula os segredos do conjunto de dimensionamento de máquinas virtuais que não dão suporte à mesma ID de recurso para dois segredos separados, pois cada segredo é um recurso exclusivo com controle de versão. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [segurança de clusters](service-fabric-cluster-security.md).
* [Atualizar e gerenciar certificados do cluster](service-fabric-cluster-security-update-certs-azure.md)
