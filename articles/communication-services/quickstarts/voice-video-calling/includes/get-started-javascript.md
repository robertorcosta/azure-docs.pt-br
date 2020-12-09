---
title: Guia de início rápido – Adicionar uma chamada VOIP a um aplicativo Web usando os Serviços de Comunicação do Azure
description: Neste tutorial, você aprenderá a usar a biblioteca de clientes de Chamada dos Serviços de Comunicação do Azure para JavaScript
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d35e51d097c2d5e0b66c23efa27ae70c065d547c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584504"
---
Neste guia de início rápido, você aprenderá como iniciar uma chamada usando a biblioteca de clientes de Chamada dos Serviços de Comunicação do Azure para JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
- Um recurso ativo dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um Token de Acesso do Usuário para criar uma instância do cliente de chamada. Saiba como [criar e gerenciar os tokens de acesso do usuário](../../access-tokens.md).

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Execute `npm init -y` para criar um arquivo **package.json** com as configurações padrão.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalar o pacote

Use o comando `npm install` para instalar a biblioteca de clientes de Chamada dos Serviços de Comunicação do Azure para JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

As seguintes versões do webpack são recomendadas para este início rápido:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Este guia de início rápido usa o webpack para agrupar os ativos do aplicativo. Execute o seguinte comando para instalar os pacotes npm webpack, webpack-cli e webpack-dev-server e listá-los como dependências de desenvolvimento no **package.json**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Crie um arquivo **index.html** no diretório raiz do projeto. Usaremos esse arquivo para configurar um layout básico que permitirá que o usuário faça uma chamada para um Bot de Comunicações do Azure.

Eis o código:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Crie um arquivo no diretório raiz do projeto chamado **client.js** para conter a lógica do aplicativo deste guia de início rápido. Adicione o código a seguir para importar o cliente de chamada e obter referências aos elementos DOM para que possamos anexar a nossa lógica de negócios. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e as interfaces administram alguns dos principais recursos da biblioteca de clientes de Chamada dos Serviços de Comunicação do Azure:

| Name                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | O CallClient é o ponto de entrada principal para a biblioteca de clientes de Chamada.                                                                       |
| CallAgent                        | O CallAgent é usado para iniciar e gerenciar chamadas.                                                                                            |
| AzureCommunicationUserCredential | A classe AzureCommunicationUserCredential implementa a interface CommunicationUserCredential, que é usada para criar uma instância do CallAgent. |


## <a name="authenticate-the-client"></a>Autenticar o cliente

Você precisa substituir `<USER_ACCESS_TOKEN>` por um token de acesso de usuário válido para o seu recurso. Confira a documentação do [token de acesso do usuário](../../access-tokens.md) se você ainda não tiver um token disponível. Usando o `CallClient`, inicialize uma instância `CallAgent` com um `CommunicationUserCredential` que nos permitirá realizar e receber chamadas. Adicione o seguinte código ao **client.js**:

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>Iniciar uma chamada

Adicione um manipulador de eventos para iniciar uma chamada quando o `callButton` for clicado:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Encerrar uma chamada

Adicione um ouvinte de eventos para encerrar a chamada atual quando o `hangUpButton` for clicado:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

A propriedade `forEveryone` encerra a chamada para todos os participantes da chamada.

## <a name="run-the-code"></a>Executar o código

Use `webpack-dev-server` para criar e executar o seu aplicativo. Execute o seguinte comando para agrupar o host de aplicativos em um servidor Web local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o navegador e navegue até http://localhost:8080/. Você deve ver o seguinte:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Captura de tela do Aplicativo JavaScript concluído.":::

Você pode fazer uma chamada VOIP de saída fornecendo uma ID de usuário no campo de texto e clicando no botão **Iniciar Chamada**. Chamar `8:echo123` conecta você a um bot de eco. Isso é ótimo para começar e verificar se os dispositivos de áudio estão funcionando.
