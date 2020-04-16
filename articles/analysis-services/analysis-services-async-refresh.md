---
title: Atualização assíncrona para modelos do Azure Analysis Services | Microsoft Docs
description: Descreve como usar a API Rest do Azure Analysis Services para codificar a atualização assíncrona dos dados do modelo.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5f6cec8b7fd1169a4f04649fcaf7bb7ada33833
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406281"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Atualização assíncrona com a API REST

Usando qualquer linguagem de programação que suporte chamadas REST, você pode executar operações assíncronas de atualização de dados em seus modelos tabulares do Azure Analysis Services. Isso inclui a sincronização de réplicas somente leitura para expansão de consulta. 

As operações de atualização de dados podem levar algum tempo, dependendo de uma série de fatores, incluindo volume de dados, nível de otimização usando partições, etc. Essas operações têm sido tradicionalmente invocadas com métodos existentes, como o uso de [TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabular Object Model), [PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) cmdlets ou [TMSL](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (Tabular Model Scripting Language). No entanto, esses métodos geralmente exigem conexões HTTP não confiáveis de execução longa.

A API REST do Azure Analysis Services permite que as operações de atualização de dados sejam realizadas de forma assíncrona. Ao usar a API REST, as conexões HTTP de execução longa de aplicativos cliente não são necessárias. Também há outros recursos internos para a confiabilidade, como repetições automáticas e confirmações em lote.

## <a name="base-url"></a>URL base

A URL base segue este formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Por exemplo, considere um modelo chamado `myserver`AdventureWorks em um servidor chamado , localizado na região do West US Azure. O nome do servidor é:

```
asazure://westus.asazure.windows.net/myserver 
```

A URL base para esse nome do servidor será:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Ao usar a URL base, os recursos e as operações poderão ser acrescentados com base nos seguintes parâmetros: 

![Atualização assíncrona](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Qualquer coisa que termine em **s** é uma coleção.
- Qualquer coisa que termine com **()** é uma função.
- Qualquer outra coisa será um recurso/objeto.

Por exemplo, você pode usar o verbo POST na coleção Refreshes para executar uma operação de atualização:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token do Azure Active Directory (OAuth 2) válido no cabeçalho Authorization e devem atender aos seguintes requisitos:

- O token deve ser um token de usuário ou uma entidade de serviço de aplicativo.
- O token deve ter a audiência correta definida como `https://*.asazure.windows.net`.
- O usuário ou o aplicativo deve ter permissões suficientes no servidor ou no modelo para fazer a chamada solicitada. O nível de permissão é determinado pelas funções no modelo ou pelo grupo de administradores no servidor.

    > [!IMPORTANT]
    > Atualmente, permissões da função do **administrador do servidor** são necessárias.

## <a name="post-refreshes"></a>POST /refreshes

Para executar uma operação de atualização, use o verbo POST na coleção /refreshes para adicionar um novo item de atualização à coleção. O cabeçalho Location na resposta inclui a ID de atualização. O aplicativo cliente poderá se desconectar e verificar o status mais tarde, se necessário, porque ele é assíncrono.

Apenas uma operação de atualização por vez é aceita para um modelo. Se houver uma operação de atualização atualmente em execução e outra operação for enviada, o código de status HTTP 409 Conflito será retornado.

O corpo pode ser semelhante ao seguinte:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parâmetros

Não é necessário especificar parâmetros. O padrão será aplicado.

| Nome             | Type  | Descrição  |Padrão  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | O tipo de processamento a ser executado. Os tipos são alinhados com os tipos de [comandos de atualização](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl) da TMSL: full, clearValues, calculate, dataOnly, automatic e defragment. Não há suporte para a adição de tipo.      |   automático      |
| `CommitMode`     | Enum  | Determina se os objetos serão confirmados em lotes ou somente na conclusão. Os modos incluem: default, transactional, partialBatch.  |  transacional       |
| `MaxParallelism` | Int   | Esse valor determina o número máximo de threads nos quais executar comandos de processamento em paralelo. Esse valor é alinhado com a propriedade MaxParallelism, que pode ser definida no [comando Sequence](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl) da TMSL ou com o uso de outros métodos.       | 10        |
| `RetryCount`     | Int   | Indica o número de vezes que a operação será repetida antes de falhar.      |     0    |
| `Objects`        | Array | Uma matriz de objetos a serem processados. Cada objeto inclui: "table" ao processar a tabela inteira ou "table" e "partition" ao processar uma partição. Se nenhum objeto for especificado, todo o modelo será atualizado. |   Processar todo o modelo      |

O CommitMode é igual ao partialBatch. Ele é usado ao fazer uma carga inicial de grandes conjuntos de dados, que pode levar horas. Se a operação de atualização falhar depois de confirmar com êxito um ou mais lotes, os lotes confirmados com êxito permanecerão confirmados (ela não reverterá lotes confirmadas com êxito).

> [!NOTE]
> No em que esse texto foi escrito, o tamanho do lote era o valor de MaxParallelism, mas esse valor poderá ser alterado.

### <a name="status-values"></a>Valores de status

|Valor de status  |Descrição  |
|---------|---------|
|`notStarted`    |   A operação ainda não começou.      |
|`inProgress`     |   Operação em andamento.      |
|`timedOut`     |    A operação foi cronometrada com base no tempo de saída especificado pelo usuário.     |
|`cancelled`     |   Operação cancelada pelo usuário ou sistema.      |
|`failed`     |   Falha na operação.      |
|`succeeded`      |   A operação foi bem sucedida.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Para verificar o status de uma operação de atualização, use o verbo GET na ID da atualização. Aqui está um exemplo do corpo da resposta. Se a operação estiver `inProgress` em andamento, é devolvida em estado.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshes

Para obter uma lista com o histórico das operações de atualização de um modelo, use o verbo GET na coleção /refreshes. Aqui está um exemplo do corpo da resposta. 

> [!NOTE]
> No momento em que esse texto foi escrito, os últimos 30 dias de operações de atualização eram armazenados e retornados, mas esse número poderá ser alterado.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Para cancelar uma operação de atualização em andamento, use o verbo DELETE na ID da atualização.

## <a name="post-sync"></a>POST /sync

Tendo realizado operações de atualização, pode ser necessário sincronizar os novos dados com réplicas para a escala de consulta. Para executar uma operação de sincronização para um modelo, use o verbo POST na função /sync. O cabeçalho Location na resposta inclui a ID da operação de sincronização.

## <a name="get-sync-status"></a>GET /sync status

Para verificar o status de uma operação de sincronização, use o verbo GET passando a ID da operação como um parâmetro. Aqui está um exemplo do corpo da resposta:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Os valores para `syncstate`:

- 0: replicando. Os arquivos do banco de dados estão sendo replicados para uma pasta de destino.
- 1: reidratação. O banco de dados está sendo reidratado em instâncias de servidor somente leitura.
- 2: concluída. A operação de sincronização foi concluída com êxito.
- 3: falha. A operação de sincronização falhou.
- 4: finalizando. A operação de sincronização foi concluída, mas está executando etapas de limpeza.

## <a name="code-sample"></a>Exemplo de código

Aqui está um exemplo de código em C# para você começar, [RestApiSample on GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Para usar o exemplo de código

1.    Clone ou baixe o repositório. Abra a solução RestApiSample.
2.    Localize a linha **client.BaseAddress = …** e forneça sua [URL base](#base-url).

A amostra de código usa autenticação [principal do serviço.](#service-principal)

### <a name="service-principal"></a>Entidade de serviço

Consulte [Criar entidade de serviço - portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md) e [Adicionar uma entidade de serviço à função de administrador de servidor](analysis-services-addservprinc-admins.md) para obter mais informações sobre como configurar uma entidade de serviço e atribuir as permissões necessárias no Azure AS. Ao concluir as etapas, execute as seguintes etapas adicionais:

1.    Na amostra de código, encontre **a autoridade da corda = ...**, substitua-a **comum** pelo ID do inquilino da sua organização.
2.    Comente/remova a marca de comentário para que a classe ClientCredential seja usada para instanciar o objeto de credencial. Verifique se os valores \<App ID> e \<App Key> podem ser acessados de forma segura ou use autenticação baseada em certificado para as entidades de serviço.
3.    Execute o exemplo.


## <a name="see-also"></a>Confira também

[Amostras](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


