---
title: Métodos de autenticação de uso & insights - Azure Active Directory
description: Relatórios sobre redefinição de senha de autoatendimento do Azure AD e uso do método de autenticação de autenticação de vários fatores
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1730939de399cacd13c62988259904ba84ee78ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654140"
---
# <a name="authentication-methods-usage--insights-preview"></a>Métodos de autenticação de uso & insights (visualização)

O uso & insights permite que você entenda como os métodos de autenticação para recursos como a autenticação multifatorial do Azure e a redefinição de senha de autoatendimento estão funcionando em sua organização. Esse recurso de relatóriofornece à sua organização os meios para entender quais métodos estão sendo registrados e como eles estão sendo usados.

## <a name="permissions-and-licenses"></a>Permissões e licenças

As seguintes funções podem acessar o uso e insights:

- Administrador global
- Leitor de segurança
- Administrador de segurança
- Leitor de Relatórios

Nenhum licenciamento adicional é necessário para acessar o uso e os insights. As informações de licenciamento de conformidade com a autenticação multifatorial e redefinição de senha de autoatendimento (SSPR) do Azure podem ser encontradas no site de preços do [Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como ele funciona

Para acessar o uso do método de autenticação e insights:

1. Navegue até [o portal Azure](https://portal.azure.com).
1. Navegue até a senha do diretório ativo do >  **Azure****redefinir** > **informações de & de uso**.
1. A partir das visões **gerais de registro** ou **uso,** você pode optar por abrir os relatórios pré-filtrados para filtrar com base em suas necessidades.

![Visão geral do uso & insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Para acessar os insights de [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)uso & diretamente, vá para . Este link o levará à visão geral do registro.

Os Usuários cadastrados, usuários habilitados e usuários capazes de telhas mostram os seguintes dados cadastrais para seus usuários:

- Registrado: Um usuário é considerado registrado se ele (ou um admin) tiver registrado métodos de autenticação suficientes para atender à política de Autenticação SSPR ou Multifatorial da sua organização.
- Ativado: Um usuário é considerado habilitado se estiver no escopo da política SSPR. Se o SSPR estiver habilitado para um grupo, o usuário será considerado habilitado se estiver nesse grupo. Se o SSPR estiver habilitado para todos os usuários, todos os usuários do inquilino (excluindo convidados) serão considerados habilitados.
- Capaz: Um usuário é considerado capaz se ambos estiverem registrados e habilitados. Esse status significa que eles podem realizar SSPR a qualquer momento, se necessário.

Clicar em qualquer um desses azulejos ou os insights mostrados neles o levará a uma lista pré-filtrada de detalhes cadastrais.

O gráfico **Registros** na guia **Registro** mostra o número de registros do método de autenticação bem-sucedidos e falhos pelo método de autenticação. O gráfico **Redefinições** na guia **Uso** mostra o número de autenticações bem-sucedidas e com falha durante o fluxo de redefinição de senha pelo método de autenticação.

Clicar em qualquer um dos gráficos o levará a uma lista pré-filtrada de eventos de registro ou reset.

Usando o controle no canto superior e direito, você pode alterar o intervalo de data súbito para os dados de auditoria mostrados nos gráficos Registros e Redefinições para 24 horas, 7 dias ou 30 dias.

### <a name="registration-details"></a>Detalhes de registro

Clicar nos **Usuários cadastrados,** **Usuários habilitados**ou **Usuários capazes** de telhas ou insights o levarão aos dados cadastrais.

O relatório de detalhes cadastrais mostra as seguintes informações para cada usuário:

- Nome
- Nome de usuário
- Status de registro (Todos, Registrados, Não Registrados)
- Status ativado (tudo, ativado, não habilitado)
- Status capaz (Tudo, Capaz, Não Capaz)
- Métodos (notificação de aplicativos, código de aplicativo, telefonema, SMS, e-mail, perguntas de segurança)

Usando os controles no topo da lista, você pode procurar um usuário e filtrar a lista de usuários com base nas colunas mostradas.

### <a name="reset-details"></a>Redefinir detalhes

Clicar nos gráficos Registros ou Redefinições o levará aos detalhes de reset.

O relatório de detalhes de reset mostra eventos de registro e reset dos últimos 30 dias, incluindo:

- Nome
- Nome de usuário
- Recurso (Tudo, Registro, Reset)
- Método de autenticação (notificação de aplicativos, código de aplicativo, chamada telefônica, chamada do Escritório, SMS, e-mail, perguntas de segurança)
- Status (Tudo, Sucesso, Fracasso)

Usando os controles no topo da lista, você pode procurar um usuário e filtrar a lista de usuários com base nas colunas mostradas.

## <a name="limitations"></a>Limitações

Os dados mostrados nestes relatórios serão adiados em até 60 minutos. Existe um campo "Última atualização" no portal Azure para identificar o quão recentes são seus dados.

Os dados de uso e insights não são uma substituição para os relatórios de atividade de autenticação multifatorial do Azure ou informações contidas no relatório de login saque do Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [Trabalhando com a API de relatório de uso de métodos de autenticação](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolhendo métodos de autenticação para sua organização](concept-authentication-methods.md)
- [Experiência de registro combinada](concept-registration-mfa-sspr-combined.md)
