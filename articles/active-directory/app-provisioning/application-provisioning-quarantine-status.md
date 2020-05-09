---
title: Status de provisionamento de aplicativo de quarentena | Microsoft Docs
description: Quando você configurou um aplicativo para provisionamento automático de usuário, saiba o que é um status de provisionamento de quarentena e como limpá-lo.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: c1e0039133b7f9a7ae827e348640f6379b7f10ac
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593923"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisionamento de aplicativo no status de quarentena

O serviço de provisionamento do Azure AD monitora a integridade de sua configuração e coloca aplicativos não íntegros em um estado de "quarentena". Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente devido a um erro, por exemplo, credenciais de administrador inválidas, o trabalho de provisionamento será marcado como em quarentena.

Durante a quarentena, a frequência de ciclos incrementais é reduzida gradualmente para uma vez por dia. O trabalho de provisionamento é removido da quarentena depois que todos os erros são corrigidos e o próximo ciclo de sincronização é iniciado. Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado (interromperá a execução).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Como fazer saber se meu aplicativo está em quarentena?

Há três maneiras de verificar se um aplicativo está em quarentena:
  
- Na portal do Azure, navegue até **Azure Active Directory** > **aplicativos** > &lt;*nome*&gt; > do aplicativo**provisionamento** e examine a barra de progresso de uma mensagem de quarentena.   

  ![Barra de status de provisionamento mostrando o status de quarentena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- No portal do Azure, navegue até **Azure Active Directory** > **logs de auditoria** > filtrar em **atividade: quarentena** e examine o histórico de quarentena. Embora a exibição na barra de progresso conforme descrito acima mostre se o provisionamento está em quarentena no momento, os logs de auditoria permitem que você veja o histórico de quarentena de um aplicativo. 

- Use a solicitação de Microsoft Graph [obter synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) para obter programaticamente o status do trabalho de provisionamento:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Verifique seu email. Quando um aplicativo é colocado em quarentena, um email de notificação de uso único é enviado. Se o motivo da quarentena for alterado, um email atualizado será enviado mostrando o novo motivo para quarentena. Se você não vir um email:

  - Verifique se você especificou um **email de notificação** válido na configuração de provisionamento para o aplicativo.
  - Verifique se não há nenhuma filtragem de spam na caixa de entrada de email de notificação.
  - Certifique-se de que você não cancelou a assinatura dos emails.

## <a name="why-is-my-application-in-quarantine"></a>Por que meu aplicativo está em quarentena?

|Descrição|Ação recomendada|
|---|---|
|**Problema de conformidade do scim:** Uma resposta HTTP/404 não encontrada foi retornada em vez da resposta HTTP/200 OK esperada. Nesse caso, o serviço de provisionamento do Azure AD fez uma solicitação para o aplicativo de destino e recebeu uma resposta inesperada.|Verifique a seção credenciais de administrador para ver se o aplicativo requer a especificação da URL do locatário e se a URL está correta. Se você não vir um problema, entre em contato com o desenvolvedor do aplicativo para garantir que seu serviço esteja em conformidade com o SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Credenciais inválidas:** Ao tentar autorizar o acesso ao aplicativo de destino, recebemos uma resposta do aplicativo de destino que indica que as credenciais fornecidas são inválidas.|Navegue até a seção credenciais de administrador da interface do usuário de configuração de provisionamento e autorize o acesso novamente com credenciais válidas. Se o aplicativo estiver na Galeria, examine o tutorial de configuração do aplicativo para obter as etapas adicionais necessárias.|
|**Funções duplicadas:** As funções importadas de determinados aplicativos, como Salesforce e zendesk, devem ser exclusivas. |Navegue até o [manifesto](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) do aplicativo no portal do Azure e remova a função duplicada.|

 Uma solicitação de Microsoft Graph para obter o status do trabalho de provisionamento mostra o seguinte motivo para quarentena:

- `EncounteredQuarantineException`indica que foram fornecidas credenciais inválidas. O serviço de provisionamento não pode estabelecer uma conexão entre o sistema de origem e o sistema de destino.

- `EncounteredEscrowProportionThreshold`indica que o provisionamento excedeu o limite de caução. Essa condição ocorre quando mais de 60% dos eventos de provisionamento falharam.

- `QuarantineOnDemand`significa que detectamos um problema com seu aplicativo e o definimos manualmente como quarentena.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Como fazer obter meu aplicativo fora de quarentena?

Primeiro, resolva o problema que fez com que o aplicativo fosse colocado em quarentena.

- Verifique as configurações de provisionamento do aplicativo para certificar-se de que você [inseriu credenciais de administrador válidas](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). O Azure AD deve ser capaz de estabelecer uma relação de confiança com o aplicativo de destino. Verifique se você inseriu credenciais válidas e se sua conta tem as permissões necessárias.

- Examine os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para investigar melhor quais erros estão causando a quarentena e resolver o erro. Acesse os logs de provisionamento no portal do Azure acessando **Azure Active Directory** &gt; **aplicativos** &gt; empresariais **logs de provisionamento (versão prévia)** na seção **atividade** .

Depois de resolver o problema, reinicie o trabalho de provisionamento. Determinadas alterações nas configurações de provisionamento do aplicativo, como mapeamentos de atributo ou filtros de escopo, reiniciarão automaticamente o provisionamento para você. A barra de progresso na página de **provisionamento** do aplicativo indica quando o provisionamento foi iniciado pela última vez. Se você precisar reiniciar o trabalho de provisionamento manualmente, use um dos seguintes métodos:  

- Use o portal do Azure para reiniciar o trabalho de provisionamento. Na página de **provisionamento** do aplicativo em **configurações**, selecione **limpar estado e reiniciar sincronização** e defina o **status de provisionamento** como **ativado**. Essa ação reinicia completamente o serviço de provisionamento, o que pode levar algum tempo. Um ciclo inicial completo será executado novamente, o que limpa as caução, remove o aplicativo da quarentena e limpa as marcas d' água.

- Use Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Você terá controle total sobre o que reinicia. Você pode optar por limpar as caução (para reiniciar o contador de caução que se acumula para o status de quarentena), limpar a quarentena (para remover o aplicativo da quarentena) ou limpar as marcas d' água. Envie a seguinte solicitação:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
