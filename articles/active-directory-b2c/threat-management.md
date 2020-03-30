---
title: Gerenciar ameaças a recursos e dados
titleSuffix: Azure AD B2C
description: Saiba mais sobre técnicas de detecção e mitigação de ataques de negação de serviço e ataques de senha no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a438363b054361420222804dffac7973470e82e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183594"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerenciar ameaças a recursos e dados no Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) possui recursos incorporados que podem ajudá-lo a proteger contra ameaças aos seus recursos e dados. Essas ameaças incluem ataques de negação de serviço e ataques de senha. Os ataques de negação de serviço podem tornar os recursos não disponíveis para usuários pretendidos. Os ataques de senha ocasionam o acesso não autorizado aos recursos.

## <a name="denial-of-service-attacks"></a>Ataques de negação de serviço

Azure Active Directory B2C protege contra ataques de inundação SYN usando um cookie SYN. Azure Active Directory B2C também protege contra ataques de negação de serviço usando limites de taxas e conexões.

## <a name="password-attacks"></a>Ataques de senha

As senhas definidas pelos usuários devem ser de complexidade razoável. O Azure AD B2C possui técnicas de atenuação em vigor para ataques de senha. A mitigação inclui a detecção de ataques de senha de força bruta e ataques de senha de dicionário. Ao usar vários sinais, o Azure AD B2C analisa a integridade das solicitações. O Azure AD B2C foi projetado para diferenciar, de forma inteligente, os usuários pretendidos de hackers e botnets.

O Azure AD B2C usa uma estratégia sofisticada para bloquear contas. As contas são bloqueadas com base no IP da solicitação e nas senhas inseridas. A duração do bloqueio também aumenta com base na probabilidade de ser um ataque. Depois que uma senha é testada 10 vezes sem sucesso (o limite de tentativa padrão), um bloqueio de um minuto ocorre. A próxima vez que um login não for bem sucedido após o desbloqueio da conta (ou seja, depois que a conta tiver sido desbloqueada automaticamente pelo serviço assim que o período de bloqueio expirar), outro bloqueio de um minuto ocorre e continua para cada login mal sucedido. Digitar a mesma senha repetidamente não conta como vários logins malsucedidos.

Os primeiros 10 períodos de bloqueio têm um minuto de duração. Os próximos 10 períodos de bloqueio são ligeiramente mais longos e aumentam em duração após cada 10 períodos de bloqueio. O contador de bloqueio é redefinido para zero após um login bem-sucedido quando a conta não está bloqueada. Períodos de bloqueio podem durar até cinco horas.

## <a name="manage-password-protection-settings"></a>Gerenciar configurações de proteção por senha

Para gerenciar as configurações de proteção por senha, incluindo o limite de bloqueio:

1. Entre no [portal do Azure](https://portal.azure.com)
1. Use o filtro **de assinatura Directory +** no menu superior para selecionar o diretório que contém o inquilino Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Em **Segurança,** selecione **Métodos de autenticação (Visualização)** e selecione **Proteção de senha**.
1. Digite as configurações desejadas de proteção de senha e selecione **Salvar**.

    ![Página de proteção por senha do portal Azure nas configurações do Azure AD](./media/threat-management/portal-02-password-protection.png)
    <br />*Definindo o limite de bloqueio para 5 em **configurações de proteção de senha** *.

## <a name="view-locked-out-accounts"></a>Exibir contas bloqueadas

Para obter informações sobre contas bloqueadas, você pode verificar o [relatório de atividade de login](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)do Active Directory . Em **Status,** selecione **Falha**. Tentativas de login com falha com um `50053` código de erro **de login** indicam uma conta bloqueada:

![Seção do relatório de login do Azure AD mostrando conta bloqueada](./media/threat-management/portal-01-locked-account.png)

Para saber como visualizar o relatório de atividade de login no Azure Active Directory, consulte [Códigos de erro do relatório de atividade de login](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
