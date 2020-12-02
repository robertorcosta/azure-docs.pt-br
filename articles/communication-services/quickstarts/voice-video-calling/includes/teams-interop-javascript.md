---
title: Guia de Início Rápido – Participar de uma reunião do Teams
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 820659c513674dc04e914c8f1094afab4f5a89e2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356453"
---
## <a name="prerequisites"></a>Pré-requisitos

- Um [aplicativo funcional de chamadas dos Serviços de Comunicação](../getting-started-with-calling.md).
- Uma [implantação do Teams](/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Habilitar a interoperabilidade do Teams

Atualmente, o recurso de interoperabilidade do Teams está em versão prévia privada. Para habilitar esse recurso no recurso dos Serviços de Comunicação, envie um email para [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) com:

1. A ID da assinatura do Azure que contém o recurso dos Serviços de Comunicação.
2. Sua ID de locatário do Teams. A maneira mais fácil de conseguir isso é [obter e compartilhar um link com o Teams](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f).

Você precisa ser membro da organização proprietária de ambas as entidades para usar esse recurso.

## <a name="add-the-teams-ui-controls"></a>Adicionar os controles de interface do usuário do Teams

Adicione uma nova caixa de texto e um botão ao HTML. A caixa de texto será usada para inserir o contexto da reunião do Teams, e o botão será usado para o usuário participar da reunião especificada:

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
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Habilitar os controles de interface do usuário do Teams

Agora podemos associar o botão **participar da Reunião do Teams** ao código que faz o usuário participar da reunião do Teams fornecida:

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Obter o contexto da reunião

O contexto do Teams pode ser recuperado com as APIs do Graph. Isso é detalhado na [documentação do Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).

Você também pode obter as informações da reunião necessárias na URL **Participar da Reunião** indicada no convite da própria reunião.

## <a name="run-the-code"></a>Executar o código

Os usuários do webpack podem usar o `webpack-dev-server` para compilar e executar seu aplicativo. Execute o seguinte comando para empacotar o host de aplicativos em um servidor Web local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o navegador e navegue até http://localhost:8080/. Você deve ver o seguinte:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Captura de tela do Aplicativo JavaScript concluído.":::

Insira o contexto do Teams na caixa de texto e clique em *Participar da Reunião do Teams* para participar da reunião do Teams no seu aplicativo dos Serviços de Comunicação.