---
title: Conecte-se ao Azure Media Services v3 API - Node.js
description: Este artigo demonstra como se conectar à API v3 do Media Services com o Node.js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896110"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Conecte-se aos Serviços de Mídia v3 API - Node.js

Este artigo mostra como se conectar ao Azure Media Services v3 node.js SDK usando o método de login principal do serviço.

## <a name="prerequisites"></a>Pré-requisitos

- Instale [node.js](https://nodejs.org/en/download/).
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Mídia.

> [!IMPORTANT]
> Revisar [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Criar pacote.json

1. Crie um arquivo package.json usando seu editor favorito.
1. Abra o arquivo e cole o seguinte código:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Os seguintes pacotes devem ser especificados:

|Pacote|Descrição|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Para ter certeza de que está usando o pacote mais recente do Azure Media Services, verifique a [instalação de npm instalar serviços de mídia de braço azul](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK de armazenamento. Usado ao carregar arquivos em ativos.|
|`ms-rest-azure`| Costumava fazer login.|

Você pode executar o seguinte comando para ter certeza de que está usando o pacote mais recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Conecte-se ao cliente Node.js

1. Crie um arquivo .js usando seu editor favorito.
1. Abra o arquivo e cole o seguinte código.
1. Defina os valores na seção "configuração de ponto final" para valores que você obteve de [APIs](access-api-cli-how-to.md)de acesso .

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Executar seu aplicativo

Abra um prompt de comando. Navegue até o diretório da amostra e execute os seguintes comandos:

```
npm install 
node index.js
```

## <a name="see-also"></a>Confira também

- [Conceitos dos Serviços de Mídia](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Próximas etapas

Explore a documentação de [referência do Node.js](/javascript/api/overview/azure/mediaservices/management) dos Serviços de Mídia e confira as [amostras](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que explicam como usar a API dos Serviços de Mídia com o Node.js.

