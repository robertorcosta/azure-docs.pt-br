---
title: Crie uma revisão de acesso de um pacote de acesso no gerenciamento de direitos Ad do Azure
description: Saiba como criar uma política de revisão de acesso para pacotes de acesso ao gerenciamento de direitos nas avaliações de acesso do Azure Active Directory (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608833"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Crie uma revisão de acesso de um pacote de acesso no gerenciamento de direitos Ad do Azure

Para reduzir o risco de acesso obsoleto, você deve habilitar revisões periódicas de usuários que têm atribuições ativas em um pacote de acesso no gerenciamento de direitos do Azure AD. Você pode habilitar avaliações quando criar um novo pacote de acesso ou editar um pacote de acesso existente. Este artigo descreve como habilitar revisões de acesso de pacotes de acesso.

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar avaliações de pacotes de acesso, você deve atender aos pré-requisitos para criar um pacote de acesso:
- Azure AD Premium P2
- Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

Para obter mais informações, veja [Requisitos de licença](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Crie uma revisão de acesso de um pacote de acesso

Você pode habilitar revisões de acesso [ao criar um novo pacote de acesso](entitlement-management-access-package-create.md) ou editar uma política de pacote de acesso [existente.](entitlement-management-access-package-lifecycle-policy.md) Siga estas etapas para permitir revisões de acesso de um pacote de acesso:

1. Abra a guia **Ciclo de vida** para um pacote de acesso e role para baixo para as **avaliações de acesso.**

1. Mova as **revisões de acesso exigir** para **Sim**.

    ![Adicione a revisão de acesso](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Especifique a data em que as revisões começarão ao **lado de Iniciar**.

1. Em seguida, defina a **freqüência de revisão** para **Anual,** **Bianual,** **Trimestral** ou **Mensal**.
Esta configuração determina com que frequência ocorrerão as revisões de acesso.

1. Defina a **Duração** para definir quantos dias cada revisão da série recorrente estará aberta para entrada dos revisores. Por exemplo, você pode agendar uma revisão anual que começa em 1º de janeiro e está aberta para revisão por 30 dias para que os revisores tenham até o final do mês para responder.

1. Ao lado **de Revisores,** selecione **Auto-revisão** se você deseja que os usuários realizem sua própria revisão de acesso ou selecione **Revisor específico** se quiser designar um revisor.

    ![Selecione Adicionar revisores](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Se você selecionou **Revisor específico,** especifique quais usuários farão a revisão de acesso:
    1. Selecione **Adicionar revisores**.
    1. No **painel Select reviewers,** procure e selecione os usuários que deseja ser um revisor.
    1. Quando você tiver selecionado seu revisor(s), clique no botão **Selecionar.**

    ![Especifique os revisores](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Clique **em Revisar + Criar** se você estiver criando um novo pacote de acesso ou **Atualizar** se estiver editando um pacote de acesso, na parte inferior da página.

## <a name="view-the-status-of-the-access-review"></a>Veja o status da revisão de acesso

Após a data de início, uma revisão de acesso será listada na seção **de revisões** de acesso. Siga estas etapas para visualizar o status de uma revisão de acesso:

1. Em **Governança de Identidade,** clique **em Pacotes de acesso** e selecione o pacote de acesso com o status de revisão de acesso que você gostaria de verificar.   

1. Uma vez que você esteja na visão geral do pacote de acesso, clique em **Acessar avaliações** no menu esquerdo.
    
    ![Selecione avaliações de acesso](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Aparecerá uma lista que contém todas as políticas que têm avaliações de acesso associadas a elas. Clique na revisão para ver seu relatório.

    ![Lista de avaliações de acesso](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Quando você vê o relatório, ele mostra o número de usuários revisados e as ações tomadas pelo revisor sobre eles.

    ![Exibir status de revisão](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Comentários de acesso notificações de e-mail
Você pode designar revisores, ou os usuários podem rever seu próprio acesso. Por padrão, o Azure AD enviará um e-mail para revisores ou auto-revisores logo após o início da revisão.

O e-mail incluirá instruções sobre como revisar o acesso aos pacotes de acesso. Se a revisão for para que os usuários revisem seu acesso, mostre-lhes as instruções sobre como realizar uma auto-revisão de seus pacotes de acesso.
  
Se você atribuiu os usuários convidados como revisores e eles não aceitaram o convite de convidado do Azure AD, eles não receberão e-mails das avaliações de acesso do Azure AD. Eles devem primeiro aceitar o convite e criar uma conta com o Azure AD antes que eles possam receber os e-mails. 

## <a name="next-steps"></a>Próximas etapas

- [Revisar o acesso de pacotes de acesso](entitlement-management-access-reviews-review-access.md)
- [Auto-revisão de pacotes de acesso](entitlement-management-access-reviews-self-review.md)
