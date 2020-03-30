---
title: Solicite um pacote de acesso - Gerenciamento de direitos azure AD
description: Saiba como usar o portal Meu Acesso para solicitar acesso a um pacote de acesso no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261743"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Solicite acesso a um pacote de acesso no gerenciamento de direitos ad do Azure

Com o gerenciamento de direitos ad do Azure, um pacote de acesso permite uma configuração única de recursos e políticas que administra automaticamente o acesso ao pacote de acesso. 

Um gerenciador de pacotes de acesso pode configurar políticas para exigir aprovação para que os usuários tenham acesso a pacotes de acesso. Um usuário que precisa ter acesso a um pacote de acesso pode enviar uma solicitação para obter acesso. Este artigo descreve como enviar uma solicitação de acesso.

## <a name="sign-in-to-the-my-access-portal"></a>Faça login no portal Meu Acesso

O primeiro passo é entrar no portal Meu Acesso, onde você pode solicitar acesso a um pacote de acesso.

**Papel pré-requisito:** Solicitante

1. Procure um e-mail ou uma mensagem do gerente de projeto ou de negócios com o que você está trabalhando. O e-mail deve incluir um link para o pacote de acesso ao que você precisará acessar. O link `myaccess`começa com , inclui uma dica de diretório, e termina com um ID do pacote de acesso.  (Para o governo dos `https://myaccess.microsoft.us` EUA, o domínio pode ser em vez disso.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Abra o link.

1. Faça login no portal Meu Acesso.

    Certifique-se de usar sua conta organizacional (trabalho ou escola). Se você não tiver certeza, consulte seu projeto ou gerente de negócios.

## <a name="request-an-access-package"></a>Solicite um pacote de acesso

Depois de encontrar o pacote de acesso no portal Meu Acesso, você pode enviar uma solicitação.

**Papel pré-requisito:** Solicitante

1. Encontre o pacote de acesso na lista.  Se necessário, você pode pesquisar digitando uma seqüência de pesquisa e, em seguida, selecionando o filtro **Nome,** **Catálogo**ou **Recursos.**

    ![Meu portal de acesso - Pesquisa de recursos](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Clique na marca de seleção para selecionar o pacote de acesso.

1. Clique em **Solicitar acesso** para abrir o painel Solicitar acesso.

    ![Portal Meus Acessos – Pacotes de acesso](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Se a caixa **de justificativa de Negócios** for exibida, digite uma justificativa para a necessidade de acesso.

1. Se **a solicitação para um período específico estiver** ativada, selecione **Sim** ou **Não**.

1. Se necessário, especifique a data de início e a data de término.

    ![Portal Meus Acessos – Solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Quando terminar, clique **em Enviar** para enviar sua solicitação.

1. Clique **em Histórico de solicitações** para ver uma lista de suas solicitações e o status.

    Se o pacote de acesso exigir aprovação, a solicitação está agora em estado de aprovação pendente.

### <a name="select-a-policy"></a>Selecione uma política

Se você solicitar acesso a um pacote de acesso que tenha várias políticas que se aplicam, você pode ser solicitado a selecionar uma diretiva. Por exemplo, um gerenciador de pacotes de acesso pode configurar um pacote de acesso com duas políticas para dois grupos de funcionários internos. A primeira política pode permitir o acesso por 60 dias e exigir aprovação. A segunda política pode permitir o acesso por 2 dias e não exigir aprovação. Se você encontrar este cenário, você deve selecionar a política que deseja usar.

![Meu portal de acesso - Solicitação de acesso - múltiplas políticas](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Reenviar uma solicitação

Quando você solicita acesso a um pacote de acesso, sua solicitação pode ser negada ou sua solicitação pode expirar se os aprovadores não responderem a tempo. Se você precisar de acesso, você pode tentar novamente e reenviar sua solicitação. O procedimento a seguir explica como reenviar uma solicitação de acesso:

**Papel pré-requisito:** Solicitante

1. Faça login no portal **Meu Acesso.**

1. Clique **em 'Solicitar histórico'** do menu de navegação à esquerda.

1. Encontre o pacote de acesso para o qual você está reenviando uma solicitação.

1. Clique na marca de seleção para selecionar o pacote de acesso.

1. Clique no link **'Exibir'** azul à direita do pacote de acesso selecionado.
    
    ![Selecione o pacote de acesso e o link de exibição](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Um painel será aberto à direita com o histórico de solicitação para o pacote de acesso.
    
    ![Selecione o botão reenviar](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Clique no botão **Reenviar** na parte inferior do painel.

## <a name="cancel-a-request"></a>Cancelar um pedido

Se você enviar uma solicitação de acesso e a solicitação ainda estiver no estado **de aprovação pendente,** você pode cancelar a solicitação.

**Papel pré-requisito:** Solicitante

1. No portal Meu Acesso, à esquerda, clique em **Histórico de Solicitação** para ver uma lista de suas solicitações e o status.

1. Clique no link **Exibir** para a solicitação que deseja cancelar.

1. Se a solicitação ainda estiver no estado **de aprovação pendente,** você pode clicar em **Cancelar** solicitação para cancelar a solicitação.

    ![Meu portal de acesso - Cancelamento de solicitação](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Clique em **Histórico de Solicitação** para confirmar que a solicitação foi cancelada.

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)
- [Solicitação de processos e notificações por e-mail](entitlement-management-process.md)
