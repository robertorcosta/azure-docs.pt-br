---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205737"
---
## <a name="local-settings-file"></a>Arquivo de configurações local

O arquivo local. Settings. JSON armazena as configurações do aplicativo, cadeias de conexão e configurações usadas pelas ferramentas de desenvolvimento local. As configurações no arquivo local. Settings. JSON são usadas somente quando você está executando projetos localmente. O arquivo de configurações local tem esta estrutura:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Essas configurações têm suporte quando você executa projetos localmente:

| Configuração      | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando essa configuração é definida como `true`, todos os valores são criptografados com uma chave do computador local. Usado com `func settings` comandos. O valor padrão é `false`. |
| **`Values`** | Matriz de configurações de aplicativo e cadeias de conexão usadas quando um projeto é executado localmente. Esses pares de chave-valor (cadeia de caracteres de cadeia de caracteres) correspondem às configurações do aplicativo em seu aplicativo de funções no Azure, como [`AzureWebJobsStorage`]. Muitos gatilhos e associações têm uma propriedade que se refere a uma configuração de aplicativo de cadeia de conexão, como `Connection` para o [gatilho do armazenamento de BLOBs](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Para essas propriedades, você precisa de uma configuração de aplicativo definida na matriz de `Values`. <br/>[`AzureWebJobsStorage`] é uma configuração de aplicativo necessária para gatilhos diferentes de http. <br/>A versão 2. x e superior do tempo de execução do Functions requer a configuração [`FUNCTIONS_WORKER_RUNTIME`], que é gerada para seu projeto por ferramentas principais. <br/> Quando você tiver o [emulador de armazenamento do Azure](../articles/storage/common/storage-use-emulator.md) instalado localmente e definir [`AzureWebJobsStorage`] como `UseDevelopmentStorage=true`, as ferramentas principais usarão o emulador. O emulador é útil durante o desenvolvimento, mas você deve testar com uma conexão de armazenamento real antes da implantação.<br/> Os valores devem ser cadeias de caracteres e não objetos JSON ou matrizes. Os nomes de configuração não podem incluir dois-pontos (`:`) ou um sublinhado duplo (`__`). Esses caracteres são reservados pelo tempo de execução.  |
| **`Host`** | As configurações nesta seção personalizam o processo de host do Functions quando você executa projetos localmente. Essas configurações são separadas das configurações de host. JSON, que também se aplicam quando você executa projetos no Azure. |
| **`LocalHttpPort`** | Define a porta padrão usada ao executar o host local do Functions (`func host start` e `func run`). A opção de linha de comando `--port` tem precedência sobre essa configuração. |
| **`CORS`** | Define as origens permitidas para [CORS (Compartilhamento de recurso entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgulas, sem espaços. Há suporte para o valor do caractere curinga (\*), que permite solicitações de qualquer origem. |
| **`CORSCredentials`** |  Quando definido como `true`, permite `withCredentials` solicitações. |
| **`ConnectionStrings`** | Uma coleção. Não use essa coleção para as cadeias de conexão usadas por suas associações de função. Essa coleção é usada somente por estruturas que normalmente recebem cadeias de conexão da seção `ConnectionStrings` de um arquivo de configuração, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). As cadeias de caracteres de conexão neste objeto são adicionadas ao ambiente com o tipo de provedor de [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Os itens nesta coleção não são publicados no Azure com outras configurações de aplicativo. Você deve adicionar esses valores explicitamente à coleção de `Connection strings` de suas configurações do aplicativo de funções. Se você estiver criando um [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) no seu código de função, deverá armazenar o valor da cadeia de conexão com as outras conexões nas **configurações do aplicativo** no Portal. |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
