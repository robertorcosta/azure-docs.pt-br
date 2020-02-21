---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: marsma
ms.openlocfilehash: e5201dfee83ec5360e55533e923e2b55c24c09d9
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492923"
---
#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Na página Visão geral do **aplicativo registrado** , selecione **configurações**.
1. Em **acesso à API**, selecione **permissões necessárias**.
1. Selecione **Microsoft Graph**.
1. Em **permissões do aplicativo**, marque a caixa de seleção da permissão para conceder ao seu aplicativo de gerenciamento. Por exemplo:
    * **Ler todos os dados do log de auditoria**: Selecione essa permissão para ler os logs de auditoria do diretório.
    * **Ler e gravar dados do diretório**: Selecione essa permissão para cenários de migração de usuário ou gerenciamento de usuários.
    * **Ler e gravar as políticas de estrutura de confiança de sua organização**: Selecione essa permissão para cenários de CI/CD (integração contínua/entrega contínua). Por exemplo, implantação de política personalizada com Azure Pipelines.
1. Clique em **Salvar**.
1. Selecione **Conceder permissões** e, em seguida, selecione **Sim**. Pode levar alguns minutos para que as permissões se propaguem totalmente.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **APIs da Microsoft** e, em seguida, selecione **Microsoft Graph**.
1. Selecione **Permissões de aplicativo**.
1. Expanda o grupo de permissões apropriado e marque a caixa de seleção da permissão para conceder ao seu aplicativo de gerenciamento. Por exemplo:
    * **Auditlog** > **auditlog. Read. All**: para ler os logs de auditoria do diretório.
    * **Directory** > **Directory. ReadWrite. All**: para cenários de migração de usuário ou de gerenciamento de usuário.
    * **Política** > **Policy. ReadWrite. TrustFramework**: para cenários de CI/CD (integração contínua/entrega contínua). Por exemplo, implantação de política personalizada com Azure Pipelines.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)** .
1. Selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Aceitar**.
1. Selecione **Atualizar**e, em seguida, verifique se "concedido para..." aparece em **status**. Pode levar alguns minutos para que as permissões sejam propagadas.
