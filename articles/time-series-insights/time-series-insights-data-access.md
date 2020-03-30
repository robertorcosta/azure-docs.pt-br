---
title: Configure segurança para conceder acesso a dados - Azure Time Series Insights Preview | Microsoft Docs
description: Saiba como configurar políticas de segurança, permissões e gerenciar políticas de acesso a dados no ambiente Desvisão de Visualização de Séries tempois do Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 1c8f14bb1bca082a9d887e5d6d88aec213448c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254346"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso a dados a um ambiente

Este artigo aborda os dois tipos de políticas de acesso da Versão prévia do Azure Time Series Insights.

> [!TIP]
> Leia as etapas [de registro do](time-series-insights-authentication-and-authorization.md) aplicativo Azure Active Directory.

## <a name="sign-in-to-time-series-insights"></a>Faça login no Time Series Insights

1. Faça login no [portal Azure](https://portal.azure.com/).
1. Localize o seu ambiente do Time Series Insights. Insira `Time Series` na caixa **Pesquisar**. Selecione **Ambiente do Time Series** nos resultados da pesquisa.
1. Selecione o seu ambiente de Análise de Séries Temporais na lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas etapas para conceder acesso a dados para uma entidade de usuário.

1. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione **+ Adicionar**.

    [![Selecione e adicione uma Política de Acesso a Dados](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Escolha **Selecionar usuário**. Pesquise o nome de usuário ou endereço de email para localizar o usuário que deseja adicionar. Selecione **Selecionar** para confirmar a seleção.

    [![Selecione um usuário para adicionar](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Escolha **Selecionar função**. Escolha a função de acesso apropriada para o usuário:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![Confirme a função selecionada](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selecione **OK** na página **Selecionar Função do Usuário**.

    [![Selecione OK na página Selecionar função do usuário](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Verifique se a página **Políticas de Acesso a Dados** lista os usuários e suas funções.

    [![Verifique os usuários e funções corretos](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fornecer acesso a hóspedes de outro inquilino Azure AD

O `Guest` papel não é um papel de gestão. É um termo usado para uma conta Convidado de um locatário para outro. Depois que a conta Convidado é convidada no diretório do locatário, ela pode ter o mesmo controle de acesso aplicado como qualquer outra conta. Você pode conceder acesso de gerenciamento a um ambiente do Time Series Insights usando a folha de Controle de Acesso (IAM). Ou pode permitir acesso aos dados no ambiente por meio da folha Políticas de Acesso a Dados. Para obter mais informações sobre o acesso de convidado de locatário do Azure AD (Azure Active Directory), leia [Adicionar usuários de colaboração B2B do Azure Active Directory no portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Siga estas etapas para conceder acesso de convidado a um ambiente do Time Series Insights para um usuário do Azure AD de outro locatário.

1. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione **+ Convidar**.

    [![Selecione Polícias de Acesso a Dados e, em seguida, + Convidar](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Insira o endereço de email do usuário que deseja convidar. Este endereço de email deve estar associado com o Azure AD. Opcionalmente, você pode incluir uma mensagem pessoal com o convite.

    [![Digite o endereço de e-mail para encontrar o usuário selecionado](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Procure a bolha de confirmação que aparece na tela.

    [![Procure a bolha de confirmação para aparecer](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Escolha **Selecionar usuário**. Pesquise o endereço de email do usuário convidado para localizar o usuário que deseja adicionar. Em seguida, **Selecione** para confirmar a seleção.

    [![Selecione o usuário e confirme a seleção](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Escolha **Selecionar função**. Escolha a função de acesso apropriada para o usuário convidado:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![Confirme a escolha do papel](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selecione **OK** na página **Selecionar Função do Usuário**.

1. Confirme se a página **Políticas de Acesso a Dados** lista o usuário convidado e as funções de cada usuário convidado.

    [![Verifique se os usuários e funções estão corretamente atribuídos](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Agora, o usuário convidado receberá um e-mail de convite no endereço de e-mail especificado acima. O usuário convidado selecionará **Get Started** para confirmar sua aceitação e se conectar ao Azure Cloud.

    [![Seleções de convidados Comece a aceitar](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Após a seleção **de Iniciar,** o usuário convidado será apresentado com uma caixa de permissões associada à organização do administrador. Após a concessão da permissão selecionando **Aceitar,** eles serão assinados.

    [![Convidado revisa permissões e aceita](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. O administrador [compartilha a URL do ambiente](time-series-insights-parameterized-urls.md) com seu convidado.

1. Depois que o usuário convidado estiver conectado ao endereço de e-mail que você usou para convidá-lo, e eles aceitarem o convite, eles serão direcionados para o portal Azure. 

1. O hóspede agora pode acessar o ambiente compartilhado usando a URL do ambiente fornecida pelo administrador. Eles podem inserir essa URL em seu navegador para acesso imediato.

1. O inquilino do administrador será exibido ao usuário convidado depois de selecionar seu ícone de perfil no canto superior direito do explorador Time Series.

    [![Seleção de Avatar em insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Depois que o usuário convidado selecionar o inquilino do administrador, ele terá a capacidade de selecionar o ambiente de Insights de séries de tempo compartilhadas. 
    
    Eles agora têm todas as capacidades associadas ao papel que você lhes forneceu na **etapa 5**.

    [![Usuário convidado seleciona seu inquilino Do Azure a partir de drop-down](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Saiba [como adicionar uma fonte de evento do Hubs de Eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) ao ambiente do Azure Time Series Insights.

* Envie [eventos para a fonte do evento.](./time-series-insights-send-events.md)

* Exibir [seu ambiente no gerenciador da Versão prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).
