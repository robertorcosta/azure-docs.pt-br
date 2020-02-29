---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186127"
---
#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Selecione **Aplicativos** e, em seguida, selecione o aplicativo Web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Selecione **Acesso à API** e, em seguida, selecione **Adicionar**.
1. Na lista suspensa **Selecionar API**, selecione a API à qual o aplicativo Web deve receber acesso. Por exemplo, *webapi1*.
1. Na lista suspensa **Selecionar Escopos**, selecione os escopos que você definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Selecione **Registros de Aplicativo (versão prévia)** e, em seguida, selecione o aplicativo Web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **Minhas APIs**.
1. Selecione a API à qual o aplicativo Web deve receber acesso. Por exemplo, *webapi1*.
1. Em **Permissão**, expanda **demonstração** e, em seguida, selecione os escopos definidos anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)** .
1. Selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Aceitar**.
1. Selecione **Atualizar** e, em seguida, verifique se "Concedido para..." aparece em **Status** para ambos os escopos. Pode levar alguns minutos para que as permissões sejam propagadas.