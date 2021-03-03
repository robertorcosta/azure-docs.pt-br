---
title: Atividade dos métodos de autenticação-Azure Active Directory
description: Visão geral dos métodos de autenticação registrados e usados pela sua organização para entrar e executar a redefinição de senha.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6566ee7e744411fc3b7005938f95181e5c5ec94d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645115"
---
# <a name="authentication-methods-activity"></a>Atividade de métodos de autenticação 

O painel de atividade novos métodos de autenticação permite que os administradores monitorem o registro e o uso do método de autenticação em sua organização. Essa funcionalidade de relatório fornece à sua organização os meios para entender quais métodos estão sendo registrados e como eles estão sendo usados.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Permissões e licenças

As funções a seguir podem acessar o uso e as informações:

- Leitor de Relatórios
- Leitor de segurança
- Administrador de Segurança
- Administrador Global

 Uma licença Azure AD Premium P1 ou P2 é necessária para acessar o uso e as informações. As informações de licenciamento de autenticação multifator do Azure AD e SSPR (redefinição de senha de autoatendimento) podem ser encontradas no [site de preços Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como ele funciona

Para acessar o uso e as informações do método de autenticação:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Clique **Azure Active Directory**  >    >  **atividade métodos de autenticação** de segurança  >  .
1. Há duas guias no relatório: **registro** e **uso**.

   ![Visão geral da atividade dos métodos de autenticação](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Detalhes do registro

Você pode acessar a [**guia registro**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) para mostrar o número de usuários com capacidade de autenticação multifator, autenticação passowordless e redefinição de senha de autoatendimento. 

Clique em **usuários com capacidade de autenticação multifator do Azure**, **usuários capazes de autenticação sem senha** ou **usuários capazes de redefinição de senha de autoatendimento** ou insights para filtrar previamente uma lista de detalhes de registro do usuário.

- **Os usuários com capacidade de autenticação multifator do Azure** mostram a divisão de usuários capazes de MFA no Azure AD. Os usuários serão considerados capacitados se estiverem registrados para um método de autenticação forte e habilitados pela política para usar esse método para executar a MFA. Esse número não reflete os usuários registrados para MFA fora do Azure AD. 
- **Os usuários que têm a autenticação sem senha** mostram a divisão dos usuários capazes de se conectarem com uma senha. Isso inclui os usuários registrados para FIDO2, Windows Hello para empresas e entrada por telefone sem senha com o aplicativo Microsoft Authenticator. 
- **Os usuários com capacidade de redefinição de senha de autoatendimento** mostram a divisão de usuários capazes de redefinição de senha de autoatendimento. Os usuários são considerados capazes de SSPR se estão registrados para métodos suficientes para satisfazer a política de SSPR de uma organização e estão habilitados para executar o SSPR. 

  ![Captura de tela de usuários capazes de registrar](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Os usuários registrados por método de autenticação** mostram quantos usuários estão registrados para cada método de autenticação. Clique em um método de autenticação para ver quais usuários estão registrados para esse método. 

![Captura de tela de usuários registrados](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Registro recente por método de autenticação** mostra o número de registros do método de autenticação com êxito e com falha por método de autenticação. Clique em um método de autenticação para ver eventos de registro recentes para esse método.

![Captura de tela de registrado recentemente](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Detalhes de uso

O relatório **uso** mostra quais métodos de autenticação os usuários estão usando para entrar e redefinir suas senhas.

![Captura de tela da página de uso](media/how-to-authentication-methods-usage-insights/usage-page.png)

As **entradas por requisito de autenticação** mostram o número de entradas interativas de usuário bem-sucedidas que eram necessárias para executar o fator único versus a autenticação multifator no Azure AD. Isso não reflete as entradas em que a MFA foi imposta por um provedor de MFA de terceiros.

![Captura de tela de entradas por requisito de autenticação](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

As **entradas por método de autenticação** mostram o número de entradas interativas do usuário (êxito e falha) por método de autenticação usado. Ele não inclui entradas em que o requisito de autenticação foi atendido por uma declaração no token.

![Captura de tela de entradas por método](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Número de redefinições de senha e desbloqueios de conta** mostra o número de alterações de senha e redefinições de senha com êxito (autoatendimento e administrador) ao longo do tempo.

![Captura de tela de redefinições e desbloqueios](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Redefinições de senha por método de autenticação** mostra o número de autenticações bem-sucedidas e com falha durante o método de autenticação do fluxo de redefinição de senha.

![Captura de tela de redefinições por método](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Detalhes de registro do usuário 

Usando os controles na parte superior da lista, você pode pesquisar um usuário e filtrar a lista de usuários com base nas colunas mostradas.

O relatório detalhes do registro mostra as seguintes informações para cada usuário:

- Nome UPN
- Name
- Compatível com MFA (capaz, sem capacidade)
- Com capacidade de senha (capaz, sem capacidade)
- SSPR registrado (registrado, não registrado)
- SSPR habilitado (habilitado, não habilitado)
- Compatível com SSPR (capaz, sem capacidade) 
- Métodos registrados (email, telefone celular, telefone celular alternativo, telefone comercial, Microsoft Authenticator Push, senha de um software uma vez, FIDO2, chave de segurança, perguntas de segurança)

  ![Captura de tela dos detalhes de registro do usuário](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Eventos de registro e redefinição 

**Eventos de registro e redefinição** mostram os eventos de registro e redefinição das últimas 24 horas, dos últimos sete dias ou dos últimos 30 dias, incluindo:

- Data
- Nome de usuário
- Usuário 
- Recurso (registro, redefinição)
- Método usado (notificação do aplicativo, código do aplicativo, chamada telefônica, chamada do Office, chamada móvel alternativa, SMS, email, perguntas de segurança)
- Status (êxito, falha)
- Motivo da falha (explicação)

  ![Captura de tela da página de uso](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Limitações

Os registros de passagem de acesso temporário (toque) não são refletidos na guia registro do relatório porque eles só são válidos por um curto período de tempo.

## <a name="next-steps"></a>Próximas etapas

- [Trabalhando com a API de relatório de uso dos métodos de autenticação](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolhendo métodos de autenticação para sua organização](concept-authentication-methods.md)
- [Experiência de registro combinada](concept-registration-mfa-sspr-combined.md)