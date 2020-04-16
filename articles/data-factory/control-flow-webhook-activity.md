---
title: Atividade do Webhook na Fábrica de Dados Azure
description: A atividade do webhook não continua a execução do pipeline até validar o conjunto de dados anexado com certos critérios especificados pelo usuário.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417871"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade do Webhook na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma atividade de webhook pode controlar a execução de pipelines através de seu código personalizado. Com a atividade do webhook, o código dos clientes pode chamar um ponto final e passar-o uma URL de retorno de chamada. A execução do pipeline aguarda a invocação de retorno de chamada antes de prosseguir para a próxima atividade.

## <a name="syntax"></a>Sintaxe

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
**name** | O nome da atividade do webhook. | String | Sim |
**type** | Deve ser definido como "WebHook". | String | Sim |
**Método** | O método REST API para o ponto final alvo. | Cadeia de caracteres. O tipo suportado é "POST". | Sim |
**url** | O ponto final e o caminho do alvo. | Uma seqüência ou uma expressão com o **valor de tipo resultante** de uma string. | Sim |
**Cabeçalhos** | Cabeçalhos que são enviados para a solicitação. Aqui está um exemplo que define o idioma `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`e digite uma solicitação: . | Uma seqüência ou uma expressão com o **valor de tipo resultante** de uma string. | Sim. Um `Content-Type` cabeçalho como `"headers":{ "Content-Type":"application/json"}` é necessário. |
**Corpo** | Representa o conteúdo enviado para o ponto de extremidade. | JSON válido ou uma expressão com o **valor de tipo de resultado** de JSON. Consulte [Solicitar esquema de carga](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) útil para o esquema da carga útil da solicitação. | Sim |
**Autenticação** | O método de autenticação usado para chamar o ponto final. Os tipos suportados são "Básico" e "ClientCertificate". Para obter mais informações, consulte [Autenticação](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication). Se a autenticação não for necessária, exclua esta propriedade. | Uma seqüência ou uma expressão com o **valor de tipo resultante** de uma string. | Não |
**Timeout** | Quanto tempo a atividade espera que o retorno de chamada especificado pelo **callBackUri** seja invocado. O valor padrão é de 10 minutos ("00:10:00"). Os valores têm o formato *timespan d*. *hh:**mm:**ss*. | String | Não |
**Relatório status no retorno de chamada** | Permite que um usuário reporte o status de falha de uma atividade de webhook. | Boolean | Não |

## <a name="authentication"></a>Autenticação

Uma atividade de webhook suporta os seguintes tipos de autenticação.

### <a name="none"></a>Nenhum

Se a autenticação não for necessária, não inclua a propriedade **de autenticação.**

### <a name="basic"></a>Basic

Especifique o nome de usuário e a senha a serem usados com autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado do cliente

Especifique o conteúdo codificado pelo Base64 de um arquivo PFX e uma senha.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade gerenciada

Use a identidade gerenciada da fábrica de dados para especificar o recurso URI para o qual o token de acesso é solicitado. Para chamar a API de Gerenciamento de Recursos do Azure, use `https://management.azure.com/`. Para obter mais informações sobre como as identidades gerenciadas funcionam, consulte as [identidades gerenciadas para a visão geral dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se sua fábrica de dados estiver configurada com um repositório Git, você deve armazenar suas credenciais no Azure Key Vault para usar autenticação básica ou de certificado de cliente. A Azure Data Factory não armazena senhas no Git.

## <a name="additional-notes"></a>Observações adicionais

Data Factory passa a chamada de propriedade **adicionalBackUri** no corpo enviado para o ponto final da URL. A Data Factory espera que este URI seja invocado antes do valor de tempo de tempo especificado. Se o URI não for invocado, a atividade falhará com o status "TimedOut".

A atividade do webhook falha quando a chamada para o ponto final personalizado falha. Qualquer mensagem de erro pode ser adicionada ao corpo de retorno de chamada e usada em uma atividade posterior.

Para cada chamada de API REST, o cliente adia se o ponto final não responder em um minuto. Esse comportamento é a melhor prática padrão http. Para corrigir esse problema, implemente um padrão 202. No caso atual, o ponto final retorna 202 (Aceito) e as pesquisas de clientes.

O intervalo de um minuto na solicitação não tem nada a ver com o tempo de atividade. Este último é usado para aguardar o retorno de chamada especificado pelo **callbackUri**.

O corpo repassado para o URI de retorno de chamada deve ser JSON válido. Defina o cabeçalho `Content-Type` como `application/json`.

Quando você usa o status do Relatório na propriedade **de retorno de chamada,** você deve adicionar o seguinte código ao corpo quando fizer o retorno de chamada:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Próximas etapas

Veja as seguintes atividades de fluxo de controle suportadas pela Data Factory:

- [Atividade Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
