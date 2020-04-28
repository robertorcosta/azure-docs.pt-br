---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "72038188"
---
A [Biblioteca do Gerenciador de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) fornece uma classe para analisar uma cadeia de conexão de um arquivo de configuração. A classe [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analisa as definições de configuração. Ele analisa as configurações para aplicativos cliente que são executados na área de trabalho, em um dispositivo móvel, em uma máquina virtual do Azure ou em um serviço de nuvem do Azure.

Para fazer referência `CloudConfigurationManager` ao pacote, adicione as `using` seguintes diretivas:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Aqui está um exemplo que mostra como recuperar uma cadeia de conexão de um arquivo de configuração:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

O uso do Gerenciador de Configurações do Azure é opcional. Você também pode usar uma API como a [classe ConfigurationManager](/dotnet/api/system.configuration.configurationmanager)do .NET Framework.
