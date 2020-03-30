---
title: Aprovar acesso just-in-time
description: Descreve como os consumidores de aplicativos gerenciados do Azure aprovam solicitações de acesso just-in-time a um aplicativo gerenciado.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651014"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configure e aprove o acesso just-in-time para aplicativos gerenciados do Azure

Como consumidor de um aplicativo gerenciado, você pode não estar confortável dando ao editor acesso permanente ao grupo de recursos gerenciados. Para lhe dar maior controle sobre a concessão de acesso a recursos gerenciados, o Azure Managed Applications fornece um recurso chamado acesso just-in-time (JIT), que está atualmente em pré-visualização. Ele permite que você aprove quando e por quanto tempo o editor tem acesso ao grupo de recursos. O editor pode fazer atualizações necessárias durante esse tempo, mas quando esse tempo acabar, o acesso do editor expira.

O fluxo de trabalho para a concessão de acesso é:

1. O editor adiciona um aplicativo gerenciado ao mercado e especifica que o acesso jit está disponível.

1. Durante a implantação, você habilita o acesso jit para sua instância do aplicativo gerenciado.

1. Após a implantação, você pode alterar as configurações para acesso Ao JIT.

1. O editor envia um pedido de acesso.

1. Você aprova o pedido.

Este artigo foca nas ações que os consumidores tomam para permitir o acesso ao JIT e aprovar solicitações. Para saber mais sobre a publicação de um aplicativo gerenciado com acesso ao JIT, consulte [Solicitar acesso just-in-time em Aplicativos Gerenciados do Azure](request-just-in-time-access.md).

> [!NOTE]
> Para usar o acesso just-in-time, você deve ter uma [licença P2 do Azure Active Directory](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Habilitar durante a implantação

1. Faça login no [portal Azure](https://portal.azure.com).

1. Encontre uma entrada no mercado para um aplicativo gerenciado com O JIT ativado. Selecione **Criar**.

1. Ao fornecer valores para o novo aplicativo gerenciado, a etapa **Configuração JIT** permite que você habilite ou desative o acesso JIT para o aplicativo gerenciado. Selecione **Sim** para **Ativar o Acesso JIT**. Essa opção é selecionada por padrão para aplicativos gerenciados definidos com JIT ativado no mercado.

   ![Configurar acesso](./media/approve-just-in-time-access/configure-jit-access.png)

   Você só pode habilitar o acesso ao JIT durante a implantação. Se você selecionar **Não,** o editor obtém acesso permanente ao grupo de recursos gerenciados. Você não pode habilitar o acesso jit mais tarde.

1. Para alterar as configurações de aprovação padrão, **selecione Personalizar configuração JIT**.

   ![Personalizar o acesso](./media/approve-just-in-time-access/customize-jit-access.png)

   Por padrão, um aplicativo gerenciado com O JIT ativado tem as seguintes configurações:

   * Modo de aprovação – automático
   * Duração máxima de acesso – 8 horas
   * Aprovadores – nenhum

   Quando a modalidade de aprovação é definida como **automática,** os aprovadores recebem uma notificação para cada solicitação, mas a solicitação é automaticamente aprovada. Quando definidos como **manual,** os aprovadores recebem uma notificação para cada solicitação, e um deles deve aprová-lo.

   A duração máxima de ativação especifica o tempo máximo que um editor pode solicitar para acesso ao grupo de recursos gerenciado.

   A lista de aprovadores é dos usuários do Azure Active Directory que podem aprovar as solicitações de acesso ao JIT. Para adicionar um aprovador, selecione **Adicionar aprovador** e pesquise pelo usuário.

   Depois de atualizar a configuração, **selecione Salvar**.

## <a name="update-after-deployment"></a>Atualização após a implantação

Você pode alterar os valores de como as solicitações são aprovadas. No entanto, se você não habilitou o acesso ao JIT durante a implantação, você não poderá habilitá-lo mais tarde.

Para alterar as configurações de um aplicativo gerenciado implantado:

1. No portal, selecione o aplicativo gerenciar.

1. Selecione **Configuração JIT** e altere as configurações conforme necessário.

   ![Alterar configurações de acesso](./media/approve-just-in-time-access/change-settings.png)

1. Ao terminar, escolha **Salvar**.

## <a name="approve-requests"></a>Aprovar solicitações

Quando o editor solicita acesso, você é notificado da solicitação. Você pode aprovar solicitações de acesso JIT diretamente através do aplicativo gerenciado ou em todos os aplicativos gerenciados através do serviço de Gerenciamento de Identidade Privilegiada AD Azure. Para usar o acesso just-in-time, você deve ter uma [licença P2 do Azure Active Directory](../../active-directory/privileged-identity-management/subscription-requirements.md).

Para aprovar solicitações através do aplicativo gerenciado:

1. Selecione **o JIT Access** para o aplicativo gerenciado e selecione **Solicitar solicitações**.

   ![Aprovar solicitações](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selecione a solicitação para aprovar.

   ![Selecionar solicitação](./media/approve-just-in-time-access/select-request.png)

1. No formulário, forneça o motivo da aprovação e selecione **Aprovar**.

Para aprovar solicitações através do Azure AD Privileged Identity Management:

1. Selecione **Todos os serviços** e comece a procurar **pelo Azure AD Privileged Identity Management**. Selecione-o entre as opções disponíveis.

   ![Busca por serviço](./media/approve-just-in-time-access/search.png)

1. Selecione **Solicitar solicitações**.

   ![Selecione solicitações de aprovação](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selecione **aplicativos gerenciados pelo Azure**e selecione a solicitação para aprovar.

   ![Selecionar solicitações](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a publicação de um aplicativo gerenciado com acesso ao JIT, consulte [Solicitar acesso just-in-time em Aplicativos Gerenciados do Azure](request-just-in-time-access.md).
