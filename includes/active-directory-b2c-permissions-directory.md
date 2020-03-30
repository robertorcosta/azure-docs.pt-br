---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184327"
---
#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Na página de visão geral do **aplicativo registrado,** selecione **Configurações**.
1. Em **Acesso à API,** selecione **permissões necessárias**.
1. Selecione **Microsoft Graph**.
1. Em **Permissões de Inscrição,** selecione a caixa de seleção da permissão para conceder ao seu aplicativo de gerenciamento. Por exemplo: 
    * **Leia todos os dados do registro de auditoria**: Selecione esta permissão para ler os registros de auditoria do diretório.
    * **Ler e gravar dados do diretório**: Selecione essa permissão para migração de usuários ou cenários de gerenciamento de usuários.
    * **Leia e escreva as políticas de estrutura de confiança da sua organização**: Selecione essa permissão para cenários de integração contínua/entrega contínua (CI/CD). Por exemplo, implantação de políticas personalizadas com pipelines Azure.
1. Selecione **Salvar**.
1. Selecione **Conceder permissões** e, em seguida, selecione **Sim**. Pode levar alguns minutos para as permissões se propagarem completamente.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **APIs** da Microsoft e selecione **O Gráfico microsoft**.
1. Selecione **Permissões de aplicativo**.
1. Expanda o grupo de permissão apropriado e selecione a caixa de seleção da permissão para conceder ao seu aplicativo de gerenciamento. Por exemplo: 
    * **AuditLog** > **AuditLog.Read.All**: Para ler os registros de auditoria do diretório.
    * **Directory**Diretório.ReadWrite.All : Para migração de usuários ou cenários de gerenciamento de usuários.**Directory.ReadWrite.All** > 
    * **Política** > **Policy.ReadWrite.TrustFramework**: Para cenários de integração contínua/entrega contínua (CI/CD). Por exemplo, implantação de políticas personalizadas com pipelines Azure.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)**.
1. Selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Aceitar**.
1. Selecione **Atualizar**e, em seguida, verifique se "Concedido para ..." aparece em **Status**. Pode levar alguns minutos para que as permissões sejam propagadas.
