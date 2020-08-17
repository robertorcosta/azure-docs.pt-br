---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 6fd8c3c5839d4cc897caa2dff70af87980e547eb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206728"
---
## <a name="local-settings-file"></a>Arquivo de configurações local

O arquivo local.settings.json armazena as configurações do aplicativo, as cadeias de conexão e as configurações usadas pelas ferramentas de desenvolvimento locais. As configurações no arquivo local.settings.json são usadas somente quando você executa projetos localmente. O arquivo de configurações local tem esta estrutura:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
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

| Configuração      | Descrição                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando essa configuração é definida como `true`, todos os valores são criptografados com uma chave do computador local. Usado com `func settings` comandos. O valor padrão é `false`. |
| **`Values`** | Matriz de configurações de aplicativos e cadeias de conexão usadas quando um projeto é executado localmente. Esses pares chave-valor (cadeia de caracteres– cadeia de caracteres) correspondem às configurações de aplicativos no aplicativo de funções no Azure, como [`AzureWebJobsStorage`]. Muitos gatilhos e associações têm uma propriedade que se refere a uma configuração de aplicativo de cadeia de conexão, como `Connection` para o [gatilho do Armazenamento de blobs](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Para essas propriedades, você precisa de uma configuração de aplicativo definida na matriz `Values`. <br/>O [`AzureWebJobsStorage`] é uma configuração de aplicativo necessária para gatilhos diferentes de HTTP. <br/>A versão 2.x e superior do tempo de execução do Functions requer a configuração [`FUNCTIONS_WORKER_RUNTIME`], que é gerada para o projeto pelo Core Tools. <br/> Quando você tiver o [emulador de armazenamento do Azure](../articles/storage/common/storage-use-emulator.md) instalado localmente e configurar [`AzureWebJobsStorage`] em `UseDevelopmentStorage=true`, o Core Tools usará o emulador. O emulador é útil durante o desenvolvimento, mas você deve testar com uma conexão de armazenamento real antes da implantação.<br/> Os valores devem ser cadeias de caracteres e não matrizes ou objetos JSON. Os nomes de configuração não podem incluir dois-pontos (`:`) ou um sublinhado duplo (`__`). Esses caracteres são reservados pelo tempo de execução. <br/>Para desabilitar uma função durante a execução local, adicione `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` à coleção, em que `<FUNCTION_NAME>` é o nome da função. Para saber mais, confira [Como desabilitar funções no Azure Functions](../articles/azure-functions/disable-function.md#localsettingsjson)  |
| **`Host`** | As configurações nesta seção personalizam o processo de host do Functions quando você executa projetos localmente. Essas configurações são separadas das configurações de host.json, que também se aplicam quando você executa projetos no Azure. |
| **`LocalHttpPort`** | Define a porta padrão usada ao executar o host local do Functions (`func host start` e `func run`). A opção de linha de comando `--port` tem precedência sobre essa configuração. |
| **`CORS`** | Define as origens permitidas para [CORS (Compartilhamento de recurso entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgulas, sem espaços. Há suporte para o valor do caractere curinga (\*), que permite solicitações de qualquer origem. |
| **`CORSCredentials`** |  Quando definido como `true`, `withCredentials` solicitações são permitidas. |
| **`ConnectionStrings`** | Uma coleção. Não use essa coleção para as cadeias de conexão usadas por suas associações de função. Ela só é usada por estruturas que devem obter cadeias de conexão da seção `ConnectionStrings` de um arquivo de configuração, como o [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). As cadeias de caracteres de conexão neste objeto são adicionadas ao ambiente com o tipo de provedor de [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Os itens nesta coleção não são publicados no Azure com outras configurações de aplicativo. É necessário adicionar explicitamente esses valores à coleção `Connection strings` das configurações do aplicativo de funções. Se estiver criando um [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) no código de função, você deverá armazenar o valor da cadeia de conexão com as outras conexões nas **Configurações de Aplicativos** no portal. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
