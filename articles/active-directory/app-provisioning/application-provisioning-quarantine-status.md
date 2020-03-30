---
title: Status de Provisionamento de Aplicações de Quarentena | Microsoft Docs
description: Quando você configurou um aplicativo para provisionamento automático do usuário, saiba o que significa um status de provisionamento de Quarentena e como limpá-lo.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154620"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisionamento de aplicativos em status de quarentena

O serviço de provisionamento Azure AD monitora a saúde de sua configuração e coloca aplicativos insalubres em um estado de "quarentena". Se a maioria ou todas as chamadas feitas contra o sistema de destino falharem consistentemente por causa de um erro, por exemplo, credenciais de administrador inválidas, o trabalho de provisionamento é marcado como em quarentena.

Enquanto em quarentena, a frequência de ciclos incrementais é gradualmente reduzida a uma vez por dia. O trabalho de provisionamento é removido da quarentena depois que todos os erros são corrigidos e o próximo ciclo de sincronização é iniciado. Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desativado (para de funcionar).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Como saberei se minha inscrição está em quarentena?

Existem três maneiras de verificar se um aplicativo está em quarentena:
  
- No portal Azure, navegue até o*nome*&gt; > do aplicativo **azure Active Directory** > **Enterprise** > &lt;**e** role até a barra de progresso na parte inferior.  

  ![Barra de status de provisionamento mostrando status de quarentena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- No portal Azure, navegue até os > **logs** de auditoria do diretório ativo do **Azure**> filtrar em **Atividade: Quarentena** e revisar o histórico de quarentena. Embora a exibição na barra de progresso como descrito acima mostre se o provisionamento está atualmente em quarentena, os registros de auditoria permitem que você veja o histórico de quarentena de um aplicativo. 

- Use a solicitação do Microsoft Graph [Obtenha sincronizaçãoTrabalho](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) para obter programáticamente o status do trabalho de provisionamento:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Verifique seu email. Quando um aplicativo é colocado em quarentena, um e-mail de notificação única é enviado. Se a razão da quarentena mudar, um e-mail atualizado é enviado mostrando o novo motivo para quarentena. Se você não ver um e-mail:

  - Certifique-se de ter especificado um **E-mail** de notificação válido na configuração de provisionamento para o aplicativo.
  - Certifique-se de que não há filtragem de spam na caixa de entrada de e-mail de notificação.
  - Certifique-se de não ter desinscrito dos e-mails.

## <a name="why-is-my-application-in-quarantine"></a>Por que minha aplicação está em quarentena?

Uma solicitação do Microsoft Graph para obter o status do trabalho de provisionamento mostra a seguinte razão para a quarentena:

- `EncounteredQuarantineException`indica que foram fornecidas credenciais inválidas. O serviço de provisionamento não consegue estabelecer uma conexão entre o sistema de origem e o sistema de destino.

- `EncounteredEscrowProportionThreshold`indica que o provisionamento excedeu o limite de custódia. Essa condição ocorre quando mais de 60% dos eventos de provisionamento falharam.

- `QuarantineOnDemand`significa que detectamos um problema com seu aplicativo e o definimos manualmente como quarentena.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Como faço para tirar minha inscrição da quarentena?

Primeiro, resolva o problema que fez com que o aplicativo fosse colocado em quarentena.

- Verifique as configurações de provisionamento do aplicativo para ter certeza de que você [inseriu credenciais admin válidas](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). O Azure AD deve ser capaz de estabelecer uma confiança com o aplicativo de destino. Certifique-se de que você inseriu credenciais válidas e que sua conta tenha as permissões necessárias.

- Revise os [registros de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para investigar melhor quais erros estão causando quarentena e resolva o erro. Acesse os logs de provisionamento no portal Azure indo para os logs de provisionamento de &gt; aplicativos **corporativos** **do Azure Active Directory** &gt; **(visualização)** na seção **Atividade.**

Depois de resolver o problema, reinicie o trabalho de provisionamento. Certas alterações nas configurações de provisionamento do aplicativo, como mapeamentos de atributos ou filtros de escopo, reiniciarão automaticamente o provisionamento para você. A barra de progresso na página de **provisionamento** do aplicativo indica quando o provisionamento foi iniciado pela última vez. Se você precisar reiniciar o trabalho de provisionamento manualmente, use um dos seguintes métodos:  

- Use o portal Azure para reiniciar o trabalho de provisionamento. Na página **provisionamento** do aplicativo em **Configurações,** selecione **Limpar estado e reiniciar a sincronização** e definir status de **provisionamento** para **On**. Esta ação reinicia totalmente o serviço de provisionamento, o que pode levar algum tempo. Um ciclo inicial completo será executado novamente, o que limpa os escrows, remove o aplicativo da quarentena e limpa quaisquer marcas d'água.

- Use o Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Você terá controle total sobre o que reiniciará. Você pode optar por limpar escrows (para reiniciar o contador de depósito que se acumula em direção ao status de quarentena), quarentena clara (para remover a aplicação da quarentena) ou marcas d'água claras. Envie a seguinte solicitação:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
