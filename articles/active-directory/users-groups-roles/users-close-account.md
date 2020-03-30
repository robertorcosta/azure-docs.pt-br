---
title: Fechar a conta do trabalho ou da escola em um diretório Ad azure não gerenciado
description: Como fechar sua conta de trabalho ou escola em um Diretório Ativo do Azure não gerenciado.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815716"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Feche sua conta de trabalho ou escola em um diretório não gerenciado

Se você é um usuário em uma organização não gerenciada do Azure Active Directory (Azure AD) e não precisa mais usar aplicativos dessa organização ou manter qualquer associação com ela, você pode fechar sua conta a qualquer momento. Um diretório não gerenciado não tem um administrador Global. Os usuários em um diretório não gerenciado podem fechar suas contas por conta própria, sem ter que entrar em contato com um administrador.

Os usuários em um diretório não gerenciado são frequentemente criados durante a inscrição de autoatendimento. Um exemplo pode ser um trabalhador da informação em uma organização que se inscreve em um serviço gratuito. Para obter mais informações sobre o autoatendimento, consulte O que é o autoatendimento do [Azure Active Directory?](directory-self-service-signup.md)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Antes de começar

Antes de fechar sua conta, você deve confirmar os seguintes itens:

* Certifique-se de que você é usuário de um diretório Ad Azure não gerenciado. Você não pode fechar sua conta se pertencer a um diretório gerenciado. Se você pertence a um diretório gerenciado e deseja fechar sua conta, você deve entrar em contato com o administrador. Para obter informações sobre como determinar se você pertence a um diretório não gerenciado, consulte [Excluir o usuário do Inquilino Não Gerenciado](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Salve todos os dados que você deseja manter. Para obter informações sobre como enviar uma solicitação de exportação, consulte [Acessar e exportar logs gerados pelo sistema para inquilinos não gerenciados](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Fechar sua conta é irreversível. Quando você fechar sua conta, todos os dados pessoais serão removidos. Você não terá mais acesso à sua conta e dados associados à sua conta.

## <a name="close-your-account"></a>Fechar sua conta

Para fechar uma conta de trabalho ou escola não gerenciada, siga estas etapas:

1. Faça login para [fechar sua conta,](https://go.microsoft.com/fwlink/?linkid=873123)usando a conta que deseja fechar.

1. Em **Minhas solicitações de dados,** selecione **Fechar conta**.

    ![Minhas solicitações de dados - Fechar conta](./media/users-close-account/close-account.png)

1. Revise a mensagem de confirmação e selecione **Sim**.

    ![Minhas solicitações de dados - Confirmar fechar](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Próximas etapas

- [O que é o autoatendimento inscrito para o Azure Active Directory?](directory-self-service-signup.md)
- [Excluir o usuário do locatário não gerenciado](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Acessando e exportando logs gerados pelo sistema para locatários não gerenciados](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
