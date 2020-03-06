---
title: Atividade de webhook no Azure Data Factory
description: A atividade de webhook não continua a execução do pipeline até que ele valide o conjunto de dado anexado com determinados critérios que o usuário especifica.
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
ms.openlocfilehash: 8c52bb21276071581a83fb3ee6a3a4a31ba0bb4a
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78400008"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade de webhook no Azure Data Factory
Você pode usar uma atividade de webhook para controlar a execução de pipelines por meio de seu código personalizado. Usando a atividade de webhook, os clientes podem chamar um ponto de extremidade e passar uma URL de retorno de chamada. A execução do pipeline aguarda a chamada do retorno de chamada antes de prosseguir para a próxima atividade.

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



Propriedade | Descrição | Allowed values | Obrigatório
-------- | ----------- | -------------- | --------
{1&gt;name&lt;1} | Nome da atividade de Web Hook | String | Sim |
type | Deve ser definido como **webhook**. | String | Sim |
{1&gt;método&lt;1} | Método da API REST para o ponto de extremidade de destino. | Cadeia de Caracteres. Tipos com suporte: ' POST ' | Sim |
url | Ponto de extremidade de destino e o caminho | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres). | Sim |
cabeçalhos | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: "headers": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"}. | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Sim, o cabeçalho Content-Type é necessário. "headers":{ "Content-Type":"application/json"} |
body | Representa o conteúdo enviado para o ponto de extremidade. | JSON válido (ou expressão com ResultType de JSON). Consulte o esquema da carga de solicitação na seção [Esquema de carga de solicitação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Sim |
autenticação | Método de autenticação usado para chamar o ponto de extremidade. Os tipos com suporte são "básico" ou "ClientCertificate". Para obter mais informações, consulte a seção [Autenticação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Se a autenticação não for necessária, exclua essa propriedade. | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não |
timeout | Quanto tempo a atividade aguardará até que &#39;o&#39; callBackUri seja invocado. Por quanto tempo a atividade aguardará o ' callBackUri ' ser invocado. O valor padrão é 10mins ("00:10:00"). O formato é TimeSpan, ou seja, d. hh: mm: SS | String | Não |
Relatar status no retorno de chamada | Permite que o usuário relate o status de falha da atividade de webhook que marcará a atividade como com falha | Boolean | Não |

## <a name="authentication"></a>Autenticação

Abaixo estão os tipos de autenticação com suporte na atividade de webhook.

### <a name="none"></a>Nenhum

Se a autenticação não for necessária, não inclua a propriedade "authentication".

### <a name="basic"></a>Basic

Especifique o nome de usuário e senha a serem usados com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado do cliente

Especifique o conteúdo codificado em base64 de um arquivo PFX e a senha.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade Gerenciada

Especifique o URI do recurso para o qual o token de acesso será solicitado usando a identidade gerenciada para o data factory. Para chamar a API de Gerenciamento de Recursos do Azure, use `https://management.azure.com/`. Para obter mais informações sobre como identidades gerenciadas funcionam, confira a [página de visão geral de identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se sua data factory estiver configurada com um repositório git, você deverá armazenar suas credenciais no Azure Key Vault para usar a autenticação básica ou de certificado do cliente. Azure Data Factory não armazena senhas no git.

## <a name="additional-notes"></a>Observações adicionais

Azure Data Factory passará uma propriedade adicional "callBackUri" no corpo para o ponto de extremidade da URL e esperará que esse URI seja invocado antes do valor de tempo limite especificado. Se o URI não for invocado, a atividade falhará com o status ' TimedOut '.

A própria atividade de webhook falha quando a chamada para o ponto de extremidade personalizado falha. Qualquer mensagem de erro pode ser adicionada ao corpo do retorno de chamada e usada em uma atividade subsequente.

Para cada chamada à API REST, o cliente atingirá o tempo limite se o ponto de extremidade não responder em 1 minuto. Essa é uma prática recomendada de http padrão. Para corrigir esse problema, você precisa implementar o padrão 202 nesse caso em que o ponto de extremidade retornará 202 (aceito) e o cliente será sondado.

O tempo limite de 1 minuto na solicitação não tem nada a ver com o tempo limite da atividade. Que será usado para aguardar o callbackUri.

O corpo passado de volta para o URI de retorno de chamada deve ser um JSON válido. Você deve definir o cabeçalho Content-Type como `application/json`.

Ao usar a opção "status do relatório no retorno de chamada", você deve adicionar o seguinte trecho ao corpo ao fazer o retorno de chamada:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade de Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
