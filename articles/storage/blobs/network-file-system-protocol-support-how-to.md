---
title: Montar o armazenamento de BLOBs do Azure no Linux usando o protocolo NFS 3,0 (versão prévia) | Microsoft Docs
description: Saiba como montar um contêiner no armazenamento de blobs de uma VM (máquina virtual) do Azure baseada em Linux ou um sistema Linux que é executado localmente usando o protocolo NFS 3,0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 8f4ef046221ae50d2b05525d6cea2d268282551c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096112"
---
# <a name="mount-blob-storage-on-linux-using-the-network-file-system-nfs-30-protocol-preview"></a>Montar o armazenamento de BLOBs no Linux usando o protocolo NFS (sistema de arquivos de rede) 3,0 (versão prévia)

Você pode montar um contêiner no armazenamento de blobs de uma VM (máquina virtual) do Azure baseada em Linux ou um sistema Linux que é executado localmente usando o protocolo NFS 3,0. Este artigo fornece orientações passo a passo. Para saber mais sobre o suporte ao protocolo NFS 3,0 no armazenamento de BLOBs, consulte [suporte ao protocolo NFS (Network File System) 3,0 no armazenamento de BLOBs do Azure (versão prévia)](network-file-system-protocol-support.md).

> [!NOTE]
> O suporte ao protocolo NFS 3,0 no armazenamento de BLOBs do Azure está em visualização pública e está disponível nas seguintes regiões: leste dos EUA, Centro dos EUA e Canadá central.

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Etapa 1: registrar o recurso de protocolo NFS 3,0 com sua assinatura

1. Abra uma janela de comando do PowerShell. 

2. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

3. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

4. Registre o `AllowNFSV3` recurso usando o comando a seguir.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registre o `PremiumHns` recurso usando também o comando a seguir.

   ```powershell
   Register-AzProviderFeature -FeatureName PremiumHns -ProviderNamespace Microsoft.Storage  
   ```

6. Registre o provedor de recursos usando o comando a seguir.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Etapa 2: verificar se o recurso está registrado 

A aprovação de registro pode levar até uma hora. Para verificar se o registro foi concluído, use os comandos a seguir.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName PremiumHns  
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Etapa 3: criar uma rede virtual do Azure (VNet)

Sua conta de armazenamento deve estar contida em uma VNet. Uma VNet permite que os clientes se conectem com segurança à sua conta de armazenamento. Para saber mais sobre VNet e como criar uma, consulte a documentação da [rede virtual](https://docs.microsoft.com/azure/virtual-network/).

> [!NOTE]
> Os clientes na mesma VNet podem montar contêineres em sua conta. Você também pode montar um contêiner de um cliente que é executado em uma rede local, mas você precisará primeiro conectar sua rede local à sua VNet. Consulte [conexões de rede com suporte](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Etapa 4: configurar a segurança de rede

A única maneira de proteger os dados em sua conta é usando uma VNet e outras configurações de segurança de rede. Qualquer outra ferramenta usada para proteger dados, incluindo autorização de chave de conta, segurança de Azure Active Directory (AD) e listas de controle de acesso (ACLs), ainda não tem suporte em contas que têm o suporte ao protocolo NFS 3,0 habilitado neles. 

Para proteger os dados em sua conta, consulte estas recomendações: [recomendações de segurança de rede para o armazenamento de BLOBs](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Etapa 5: criar e configurar uma conta de armazenamento

Para montar um contêiner usando o NFS 3,0, você deve criar uma conta de armazenamento **depois** de registrar o recurso com sua assinatura. Você não pode habilitar contas que existiam antes de registrar o recurso. 

Na versão de visualização desse recurso, o protocolo NFS 3,0 tem suporte apenas em contas [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) .

Ao configurar a conta, escolha estes valores:

|Configuração | Valor|
|----|---|
|Location|Uma das seguintes regiões: leste dos EUA, Centro dos EUA e Canadá central |
|Desempenho|Premium|
|Tipo de conta|BlockBlobStorage|
|Replicação|Armazenamento com redundância local (LRS)|
|Método de conectividade|Ponto de extremidade público (redes selecionadas) ou ponto de extremidade privado|
|Transferência segura é necessária|Desabilitado|
|Namespace hierárquico|habilitado|
|NFS V3|habilitado|

Você pode aceitar os valores padrão para todas as outras configurações. 

## <a name="step-6-create-a-container"></a>Etapa 6: Criar um contêiner

Crie um contêiner em sua conta de armazenamento usando qualquer uma dessas ferramentas ou SDKs:

|Ferramentas|SDKs|
|---|---|
|[Gerenciador de Armazenamento do Azure](data-lake-storage-explorer.md#create-a-container)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[CLI do Azure](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](https://docs.microsoft.com/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Etapa 7: montar o contêiner

1. Em um sistema Linux, crie um diretório.

   ```
   mkdir -p /mnt/test
   ```

2. Monte um contêiner usando o comando a seguir.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Substitua o `<storage-account-name>` espaço reservado que aparece nesse comando pelo nome da sua conta de armazenamento.  

   - Substitua o `<container-name>` espaço reservado pelo nome do seu contêiner.

## <a name="resolve-common-issues"></a>Resolver problemas comuns

|Problema/erro | Resolução|
|---|---|
|`Access denied by server while mounting`|Verifique se o cliente está sendo executado em uma sub-rede com suporte. Consulte os [locais de rede com suporte](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Verifique se o contêiner que você está montando foi criado depois de verificar se o recurso foi registrado. Consulte [etapa 2: verificar se o recurso está registrado](#step-2-verify-that-the-feature-is-registered). Além disso, certifique-se de digitar o comando mount e os parâmetros diretamente no terminal. Se você copiar e colar qualquer parte deste comando no terminal de outro aplicativo, os caracteres ocultos nas informações coladas poderão causar a exibição desse erro.|
|Os arquivos que foram carregados usando as ferramentas não NFS 3,0 não são visíveis no diretório. | Desmonte o contêiner e, em seguida, monte o contêiner novamente. |

## <a name="see-also"></a>Confira também

[Suporte ao protocolo NFS (sistema de arquivos de rede) 3,0 no armazenamento de BLOBs do Azure (versão prévia)](network-file-system-protocol-support.md)







