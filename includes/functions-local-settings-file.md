---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205737"
---
## <a name="local-settings-file"></a>Arquivo de configurações local

O arquivo local.settings.json armazena configurações de aplicativos, strings de conexão e configurações usadas pelas ferramentas de desenvolvimento locais. As configurações no arquivo local.settings.json são usadas apenas quando você está executando projetos localmente. O arquivo de configurações locais tem essa estrutura:

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

Essas configurações são suportadas quando você executa projetos localmente:

| Configuração      | Descrição                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando esta configuração `true`é definida como , todos os valores são criptografados com uma chave de máquina local. Usado com `func settings` comandos. O valor padrão é `false`. |
| **`Values`** | Matriz de configurações de aplicativos e strings de conexão usadas quando um projeto está sendo executado localmente. Esses pares de valor de chave (string-string) correspondem às configurações [`AzureWebJobsStorage`]do aplicativo em seu aplicativo de função no Azure, como . Muitos gatilhos e vinculações têm uma propriedade que se `Connection` refere a uma configuração de aplicativo de string de conexão, como para o [gatilho de armazenamento Blob](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Para essas propriedades, você precisa de `Values` uma configuração de aplicativo definida na matriz. <br/>[`AzureWebJobsStorage`]é uma configuração de aplicativo necessária para gatilhos diferentes de HTTP. <br/>A versão 2.x e superior do tempo`FUNCTIONS_WORKER_RUNTIME`de execução functions requer a configuração [ ], que é gerada para o seu projeto por Ferramentas Principais. <br/> Quando você tem o [emulador de armazenamento Azure](../articles/storage/common/storage-use-emulator.md) instalado localmente e você configura [`AzureWebJobsStorage`] para `UseDevelopmentStorage=true`, Ferramentas principais usa o emulador. O emulador é útil durante o desenvolvimento, mas você deve testar com uma conexão de armazenamento real antes da implantação.<br/> Os valores devem ser strings e não objetos ou matrizes JSON. Definir nomes não pode incluir`:`um cólon`__`( ) ou um sublinhado duplo (). Esses personagens são reservados pelo tempo de execução.  |
| **`Host`** | As configurações nesta seção personalizem o processo de host Funções quando você executa projetos localmente. Essas configurações são separadas das configurações host.json, que também se aplicam quando você executa projetos no Azure. |
| **`LocalHttpPort`** | Define a porta padrão usada ao executar o host local do Functions (`func host start` e `func run`). A `--port` opção de linha de comando tem precedência sobre esta configuração. |
| **`CORS`** | Define as origens permitidas para [CORS (Compartilhamento de recurso entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgulas, sem espaços. Há suporte para o valor do caractere curinga (\*), que permite solicitações de qualquer origem. |
| **`CORSCredentials`** |  Quando definido `true`para `withCredentials` , permite solicitações. |
| **`ConnectionStrings`** | Uma coleção. Não use esta coleção para as seqüências de conexão usadas pelas amarras da função. Esta coleção é usada apenas por frameworks que `ConnectionStrings` normalmente obtêm strings de conexão da seção de um arquivo de configuração, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). As cadeias de caracteres de conexão neste objeto são adicionadas ao ambiente com o tipo de provedor de [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Os itens desta coleção não são publicados no Azure com outras configurações do aplicativo. Você deve adicionar explicitamente esses `Connection strings` valores à coleção de configurações do aplicativo de função. Se você estiver [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) criando um em seu código de função, você deve armazenar o valor da seqüência de conexão com suas outras conexões em **Configurações de aplicativo** no portal. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
