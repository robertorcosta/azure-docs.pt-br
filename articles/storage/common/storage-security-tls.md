---
title: Habilitar TLS seguro com .NET
titleSuffix: Azure Storage
description: Saiba como habilitar o TLS 1,2 usando a biblioteca de cliente .NET para o armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "75371795"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Habilitar TLS seguro para cliente de Armazenamento do Microsoft Azure

Os protocolos TLS e SSL são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores. Foram localizados SSL 1.0, 2.0 e 3.0 vulneráveis. Eles foram proibidos pelo RFC. O TLS 1.0 torna-se não seguro por usar codificação de Bloco (DES CBC e RC2 CBC) e criptografia de Fluxo (RC4) não seguras. O conselho PCI também recomendou a migração para versões superiores do TLS. Para obter mais detalhes, consulte [Protocolo TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

O Armazenamento do Microsoft Azure encerrou o SSL 3.0 desde 2015 e usa o TLS 1.2 em pontos de extremidade HTTPs públicos, mas o TLS 1.0 e o TLS 1.1 ainda têm suporte para compatibilidade com versões anteriores.

Para garantir uma conexão segura e compatível com o Armazenamento do Microsoft Azure, é necessário habilitar o TLS 1.2 ou versão mais recente no lado do cliente antes de enviar solicitações para operar o serviço de armazenamento do Azure.

## <a name="enable-tls-12-in-net-client"></a>Habilitar TLS 1.2 no cliente .NET

Para o cliente negociar TLS 1.2, SO e a versão do .NET Framework, ambos precisam dar suporte ao TLS 1.2. Consulte mais detalhes em [Suporte para TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

O exemplo a seguir mostra como habilitar TLS 1.2 no cliente .NET.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Habilitar TLS 1.2 no cliente do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

O exemplo a seguir mostra como habilitar TLS 1.2 no cliente do PowerShell.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>Verificar a conexão TLS 1.2

É possível usar o Fiddler para verificar se o TLS 1.2 realmente é utilizado. Abra o Fiddler para iniciar a captura do tráfego de rede do cliente e execute o exemplo acima. Em seguida, você poderá localizar a versão do TLS na conexão que o exemplo executa.

A captura de tela a seguir é um exemplo para a verificação.

![captura de tela da verificação de versão do TLS no Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Confira também

* [Protocolo TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Conformidade com PCI no TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Habilitar TLS no cliente de Java](https://www.java.com/en/configure_crypto.html)
