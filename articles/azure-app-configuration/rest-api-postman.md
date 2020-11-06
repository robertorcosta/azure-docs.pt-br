---
title: API REST de Azure Active Directory-teste usando o postmaster
description: Usar o postmaster para testar a API REST de configuração de Azure App
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 9690678fc7b794c694e588a7993cb131d8264a72
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423901"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Testar a API REST de configuração de Azure App usando o postmaster

Para testar a API REST usando o [postmaster](https://www.getpostman.com/), você precisará incluir os cabeçalhos HTTP necessários para [autenticação](./rest-api-authentication-hmac.md) em suas solicitações. Veja como configurar o postmaster para testar a API REST, gerando automaticamente os cabeçalhos de autenticação:

1. Criar uma nova [solicitação](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)
1. Adicione a `signRequest` função do [exemplo de autenticação JavaScript](./rest-api-authentication-hmac.md#javascript) ao [script de pré-solicitação](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) para a solicitação
1. Adicione o código a seguir ao final do script de pré-solicitação. Atualizar a chave de acesso conforme indicado pelo comentário TODO

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. Enviar a solicitação
