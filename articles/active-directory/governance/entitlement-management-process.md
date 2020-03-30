---
title: Processo de solicitação & notificações - Gerenciamento de direitos do Azure AD
description: Saiba mais sobre o processo de solicitação de um pacote de acesso e quando as notificações de e-mail são enviadas no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128515"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Solicitação de processos e notificações por e-mail no gerenciamento de direitos do Azure AD

Quando um usuário envia uma solicitação a um pacote de acesso, um processo começa a fornecer essa solicitação de acesso. O gerenciamento de direitos do Azure AD envia notificações de e-mail para aprovadores e solicitantes quando ocorrem os principais eventos durante o processo. Este artigo descreve o processo de solicitação e as notificações de e-mail enviadas.

## <a name="request-process"></a>Processo de solicitação

Um usuário que precisa ter acesso a um pacote de acesso pode enviar uma solicitação de acesso. Dependendo da configuração da política, a solicitação pode exigir uma aprovação. Quando uma solicitação é aprovada, um processo começa a atribuir ao usuário acesso a cada recurso no pacote de acesso. O diagrama a seguir mostra uma visão geral do processo e dos diferentes estados:

![Diagrama do processo de aprovação](./media/entitlement-management-process/request-process.png)

| Estado | Descrição |
| --- | --- |
| Enviado | O usuário envia uma solicitação. |
| Aprovação pendente | Se a política de um pacote de acesso exigir aprovação, uma solicitação passa para aprovação pendente. |
| Expirado | Se nenhum aprovador aprovar um pedido dentro do tempo de validade da solicitação de aprovação, a solicitação expira. Para tentar novamente, o usuário terá que reenviar sua solicitação. |
| Negadas | Approver nega um pedido. |
| Aprovado | O aprovador aprova um pedido. |
| Fornecimento | O usuário **não** teve acesso a todos os recursos do pacote de acesso. Se este é um usuário externo, o usuário pode não ter acessado o diretório de recursos ainda. Eles também podem não ter aceitado o aviso de consentimento. |
| Entregue | O usuário foi atribuído acesso a todos os recursos do pacote de acesso. |
| Acesso estendido | Se as extensões forem permitidas na diretiva, o usuário prorrogou a atribuição. |
| Acesso expirado | O acesso do usuário ao pacote de acesso expirou. Para ter acesso novamente, o usuário terá que enviar uma solicitação. |

## <a name="email-notifications"></a>Notificações por email

Se você é um aprovador, você é enviado notificações de e-mail quando você precisa aprovar uma solicitação de acesso. Você também recebe notificações quando uma solicitação de acesso foi concluída. Você também envia notificações de e-mail que indicam o status da sua solicitação se você for um solicitante.

Os diagramas a seguir mostram quando essas notificações de e-mail são enviadas aos aprovadores ou ao solicitante. Consulte a [tabela de notificações de e-mail](entitlement-management-process.md#email-notifications-table) para encontrar o número correspondente às notificações de e-mail exibidas nos diagramas.

### <a name="first-approvers-and-alternate-approvers"></a>Primeiros aprovadores e aprovadores alternativos
O diagrama a seguir mostra a experiência dos primeiros aprovadores e aprovadores alternativos, e as notificações de e-mail que recebem durante o processo de solicitação:

![Fluxo de aprovadores em primeiro e suplente](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Solicitantes
O diagrama a seguir mostra a experiência dos solicitantes e as notificações de e-mail que recebem durante o processo de solicitação:

![Fluxo de processo do solicitante](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Aprovação em 2 estágios
O diagrama a seguir mostra a experiência dos aprovadores do estágio 1 e estágio 2 e as notificações de e-mail que recebem durante o processo de solicitação:

![Fluxo de processo de aprovação em 2 estágios](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabela de notificações por e-mail
A tabela a seguir fornece mais detalhes sobre cada uma dessas notificações de e-mail. Para gerenciar esses e-mails, você pode usar regras. Por exemplo, no Outlook, você pode criar regras para mover os e-mails para uma pasta se o assunto contiver palavras desta tabela:

| # | Assunto do email | Quando enviado | Enviado a |
| --- | --- | --- | --- |
| 1 | Ação necessária: Aprovar ou negar solicitação encaminhada por *[data]* | Este e-mail será enviado aos aprovadores alternativos da Fase 1 (após a solicitação ter sido escalonada) para tomar medidas. | Aprovadores suplentes estágio-1 |
| 2 | Ação necessária: Aprovar ou negar solicitação até *[data]* | Este e-mail será enviado ao primeiro aprovador, se o escalonamento for desativado, para tomar medidas. | Primeiro aprovador |
| 3 | Lembrete: Aprovar ou negar a solicitação por *[data]* para *[solicitante]* | Este e-mail de lembrete será enviado ao primeiro aprovador, se a escalação for desativada. O e-mail pede que eles tomem medidas se não o fizeram. | Primeiro aprovador |
| 4 | Aprovar ou negar o pedido por *[tempo]* na *[data]* | Este e-mail será enviado ao primeiro aprovador (se o escalonamento estiver habilitado) para tomar medidas. | Primeiro aprovador |
| 5 | Lembrete necessário à ação: Aprovar ou negar a solicitação por *[data]* para *[solicitante]* | Este e-mail de lembrete será enviado ao primeiro aprovador, se a escalação estiver ativada. O e-mail pede que eles tomem medidas se não o fizeram. | Primeiro aprovador |
| 6 | O pedido expirou para *[access_package]* | Este e-mail será enviado ao primeiro aprovador e aos aprovadores suplentes da fase 1 após o término da solicitação. | Primeiro aprovador, aprovadores suplentes estágio-1 |
| 7 | Pedido aprovado para *[solicitante]* para *[access_package]* | Este e-mail será enviado ao primeiro aprovador e aos aprovadores suplentes da fase 1 após a conclusão da solicitação. | Primeiro aprovador, aprovadores suplentes estágio-1 |
| 8 | Pedido aprovado para *[solicitante]* para *[access_package]* | Este e-mail será enviado ao primeiro aprovador e aos aprovadores suplentes da fase 1 de uma solicitação de 2 etapas quando a solicitação da fase 1 for aprovada. | Primeiro aprovador, aprovadores suplentes estágio-1 |
| 9 | Pedido negado ao *[access_package]* | Este e-mail será enviado ao solicitante quando sua solicitação for negada | Solicitante |
| 10 | Seu pedido expirou para *[access_package]* | Este e-mail será enviado ao solicitante ao final de uma única ou duas etapas. O e-mail notifica o solicitante de que a solicitação expirou. | Solicitante |
| 11 | Ação necessária: Aprovar ou negar solicitação até *[data]* | Este e-mail será enviado ao segundo aprovador, se a escalação for desativada, para tomar medidas. | Segundo aprovador |
| 12 | Lembrete necessário à ação: Aprovar ou negar a solicitação por *[data]* | Este e-mail de lembrete será enviado ao segundo aprovador, se a escalação for desativada. A notificação pede que eles tomem medidas se ainda não o fizeram. | Segundo aprovador |
| 13 | Ação requerida: Aprovar ou negar o pedido por *[data]* para *[solicitante]* | Este e-mail será enviado ao segundo aprovador, se o escalonamento estiver habilitado, para tomar medidas. | Segundo aprovador |
| 14 | Lembrete necessário à ação: Aprovar ou negar a solicitação por *[data]* para *[solicitante]* | Este e-mail de lembrete será enviado ao segundo aprovador, se a escalação estiver ativada. A notificação pede que eles tomem medidas se ainda não o fizeram. | Segundo aprovador |
| 15 | Ação necessária: Aprovar ou negar solicitação encaminhada por *[data]* | Este e-mail será enviado aos aprovadores alternativos da fase 2, se o escalonamento for habilitado, para agir. | Aprovadores suplentes estágio 2 |
| 16 | Pedido aprovado para *[solicitante]* para *[access_package]* | Este e-mail será enviado ao segundo aprovador e aos aprovadores suplentes da fase 2 após a aprovação do pedido. | Segundo aprovador, aprovadores suplentes do Estágio-2 |
| 17 | Um pedido expirou para *[access_package]* | Este e-mail será enviado ao segundo aprovador ou aprovador alternativo, após o término da solicitação. | Segundo aprovador, aprovadores suplentes estágio-2 |
| 18 | Agora você tem acesso a *[access_package]* | Este e-mail será enviado aos usuários finais para começar a usar seu acesso. | Solicitante |
| 19 | Estender o acesso para *[access_package]* até *[data]* | Este e-mail será enviado aos usuários finais antes que seu acesso expire. | Solicitante |
| 20 | O acesso terminou para *[access_package]* | Este e-mail será enviado aos usuários finais após o término do seu acesso. | Solicitante |

### <a name="access-request-emails"></a>E-mails de solicitação de acesso

Quando um solicitante enviar uma solicitação de acesso para um pacote de acesso configurado para exigir aprovação, todos os aprovadores adicionados à política receberão uma notificação por e-mail com detalhes da solicitação. Os detalhes no e-mail incluem: organização do nome do solicitante e justificativa de negócios; e a data de início e término de acesso solicitada (se fornecida). Os detalhes também incluirão quando a solicitação foi apresentada e quando a solicitação expirará.

O e-mail inclui um link em que os aprovadores podem clicar para ir ao Meu Acesso para aprovar ou negar a solicitação de acesso. Aqui está uma notificação de e-mail de exemplo que é enviada ao primeiro aprovador ou segundo aprovador (se a aprovação de 2 estágios estiver habilitada) para concluir uma solicitação de acesso:

![Aprovar solicitação para acessar o e-mail do pacote](./media/entitlement-management-shared/approver-request-email.png)

Os aprovados também podem receber um e-mail de lembrete. O e-mail pede ao aprovador que tome uma decisão sobre o pedido. Aqui está uma notificação de e-mail de exemplo que o aprovador recebe para lembrá-los de agir:

![E-mail de solicitação de acesso ao lembrete](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Aprovadores alternativos solicitam e-mails

Se a configuração de aprovadores alternativos estiver ativada e a solicitação ainda estiver pendente, ela será encaminhada. Os aprovadores alternativos receberão um e-mail para aprovar ou negar a solicitação. Você pode habilitar aprovadores alternativos no estágio 1 e no estágio-2. Aqui está um e-mail de exemplo da notificação que os aprovadores alternativos recebem:

![Aprovadores alternativos solicitam e-mail](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Tanto o aprovador quanto os aprovadores suplentes podem aprovar ou negar o pedido.

### <a name="approved-or-denied-emails"></a>E-mails aprovados ou negados

 Quando um aprovador recebe uma solicitação de acesso enviada por um solicitante, ele pode aprovar ou negar a solicitação de acesso. O aprovador precisa adicionar uma justificativa comercial para sua decisão. Aqui está um e-mail de exemplo enviado aos aprovadores e aprovadores alternativos após a aprovação de uma solicitação:

![Solicitação aprovada para acessar o e-mail do pacote](./media/entitlement-management-process/approver-request-email-approved.png)

Quando uma solicitação de acesso é aprovada e seu acesso é provisionado, uma notificação por e-mail é enviada ao solicitante para que eles agora tenham acesso ao pacote de acesso. Aqui está uma notificação de e-mail de exemplo que é enviada a um solicitante quando eles têm acesso a um pacote de acesso:

![E-mail de solicitação de solicitação de acesso aprovado](./media/entitlement-management-process/requestor-email-approved.png)

Quando uma solicitação de acesso é negada, uma notificação por e-mail é enviada ao solicitante. Aqui está uma notificação de e-mail de exemplo que é enviada a um solicitante quando sua solicitação de acesso é negada:

![Solicitação de solicitação de e-mail negado](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>E-mails de solicitação de acesso de aprovação em 2 estágios

Se a aprovação em duas etapas for habilitada, pelo menos dois aprovados devem aprovar a solicitação, uma de cada etapa, antes que o solicitante possa ter acesso.

Durante a fase 1, o primeiro aprovador receberá o e-mail de solicitação de acesso e tomará uma decisão. Se aprovarem a solicitação, todos os primeiros aprovadores e aprovadores suplentes na fase 1 (se o escalonamento for habilitado) receberão a notificação de que a fase 1 está completa. Aqui está um e-mail de exemplo da notificação enviada quando a fase 1 estiver completa:

![E-mail de solicitação de acesso em 2 estágios](./media/entitlement-management-process/approver-request-email-2stage.png)

Após os aprovadores do primeiro ou suplente aprovarem o pedido na fase 1, começa a fase 2. Durante a fase 2, o segundo aprovador receberá o e-mail de notificação de solicitação de acesso. Após o segundo aprovador ou suplente aprovador na fase 2 (se a escalada for habilitada) decidir aprovar ou negar a solicitação, os e-mails de notificação são enviados para o primeiro e segundo aprovadores, e todos os aprovadores alternativos na fase 1 e na fase 2, bem como no solicitante.

### <a name="expired-access-request-emails"></a>E-mails de solicitação de acesso expirado

Os pedidos de acesso podem expirar se nenhum aprovador tiver aprovado ou negado o pedido. 

Quando a solicitação atinge sua data de validade configurada e expira, ela não pode mais ser aprovada ou negada pelos aprovadores. Aqui está um e-mail de exemplo da notificação enviada a todos os primeiros, segundos (se a aprovação de 2 estágios estiver habilitada), e aprovadores alternativos:

![Aprovadores expirado e-mail de solicitação de acesso](./media/entitlement-management-process/approver-request-email-expired.png)

Uma notificação por e-mail também é enviada ao solicitante, notificando-os de que sua solicitação de acesso expirou, e que eles precisam reenviar a solicitação de acesso. O diagrama a seguir mostra a experiência do solicitante e as notificações de e-mail que recebem quando solicitam ampliar o acesso:

![Solicitanteou ampliar o fluxo do processo de acesso](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Aqui está uma notificação de e-mail de exemplo que é enviada a um solicitante quando sua solicitação de acesso expirou:

![Requestor expirou e-mail de solicitação de acesso](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Próximas etapas

- [Solicite acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)
