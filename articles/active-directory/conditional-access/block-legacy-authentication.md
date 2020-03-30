---
title: Autenticação do legado de blocos - Diretório Ativo do Azure
description: Saiba como melhorar sua postura de segurança bloqueando a autenticação legado usando o Azure AD Conditional Access.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76dd07a59a9fa7c0d6231a766ff4090c11f9f5bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331921"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como: Bloquear autenticação de legado no Azure AD com acesso condicional   

Para fornecer aos usuários acesso fácil aos aplicativos na nuvem, o Azure AD (Azure Active Directory) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação herdada. No entanto, os protocolos legados não suportam a autenticação multifatorial (MFA). Em muitos ambientes, a MFA é um requisito comum para lidar com roubo de identidade. 

Alex Weinert, diretor de segurança de identidade da Microsoft, em seu post no blog em 12 de março de 2020 [Novas ferramentas para bloquear a autenticação legada em sua organização](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) enfatizam por que as organizações devem bloquear a autenticação do legado e quais ferramentas adicionais a Microsoft fornece para realizar essa tarefa:

> Para que o MFA seja eficaz, você também precisa bloquear a autenticação do legado. Isso porque protocolos de autenticação legado como POP, SMTP, IMAP e MAPI não podem impor o MFA, tornando-os pontos de entrada preferidos para adversários que atacam sua organização...
> 
>... Os números sobre autenticação legado a partir de uma análise do tráfego do Azure Active Directory (Azure AD) são gritantes:
> 
> - Mais de 99% dos ataques de spray de senha usam protocolos de autenticação legado
> - Mais de 97% dos ataques de recheio de credenciais usam autenticação herdada
> - As contas Azure AD em organizações que desativaram a autenticação do legado experimentam 67% menos compromissos do que aquelas em que a autenticação do legado é habilitada
>

Se o seu ambiente estiver pronto para bloquear a autenticação legada para melhorar a proteção do seu inquilino, você pode alcançar esse objetivo com o Acesso Condicional. Este artigo explica como você pode configurar políticas de acesso condicional que bloqueiam a autenticação de legado para o seu inquilino.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- Os [conceitos básicos](overview.md) do Azure AD Conditional Access 
- As [melhores práticas](best-practices.md) para configurar políticas de Acesso Condicional no portal Azure

## <a name="scenario-description"></a>Descrição do cenário

O Azure AD dá suporte para vários dos protocolos de autenticação e autorização mais amplamente utilizados, incluindo a autenticação herdada. Autenticação herdada refere-se a protocolos que usam autenticação básica. Normalmente, esses protocolos não podem impor nenhum tipo de autenticação de segundo fator. Exemplos de aplicativos baseados em autenticação herdada são:

- Aplicativos mais antigos do Microsoft Office
- Aplicativos que usam protocolos de email como POP, IMAP e SMTP

Autenticação de fator único (por exemplo, nome de usuário e senha) atualmente não é suficiente. Senhas são ruins porque são fáceis de adivinhar e nós (humanos) dificilmente escolhemos boas senhas. Senhas também são vulneráveis a uma variedade de ataques, como pulverização de senha e phishing. Uma das medidas mais fáceis que você pode tomar para proteção contra ameaças de senha é implementar MFA. Com MFA, mesmo se um invasor possuir a senha de um usuário, somente a senha não será suficiente para autenticar e acessar os dados com êxito.

Como é possível impedir que aplicativos usando autenticação herdada acessem os recursos do locatário? A recomendação é bloqueá-los com uma política de Acesso Condicional. Se necessário, você permite que apenas determinados usuários e locais de rede específicos usem aplicativos baseados em autenticação herdada.

As políticas de Acesso Condicional são impostas após a conclusão da autenticação multifator. Portanto, o Acesso Condicional não funciona como uma primeira linha de defesa para cenários como ataques de DoS (ataque de negação de serviço), mas pode utilizar os sinais desses eventos (por exemplo, o nível de risco de entrada, a localização da solicitação e assim por diante) para determinar o acesso.

## <a name="implementation"></a>Implementação

Esta seção explica como configurar uma política de acesso condicional para bloquear a autenticação do legado. 

### <a name="legacy-authentication-protocols"></a>Protocolos de autenticação herdados

As seguintes opções são consideradas protocolos de autenticação legado

- SMTP autenticado - Usado por clientes POP e IMAP para enviar mensagens de e-mail.
- Autodiscover - Usado pelos clientes Outlook e EAS para encontrar e conectar-se às caixas de correio no Exchange Online.
- Exchange Online PowerShell - Usado para se conectar ao Exchange Online com powershell remoto. Se você bloquear a autenticação básica para o Exchange Online PowerShell, você precisará usar o Módulo PowerShell Online do Exchange para se conectar. Para obter instruções, consulte [Conecte-se ao Exchange Online PowerShell usando autenticação multifatorial](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS) - Uma interface de programação usada pelo Outlook, Outlook para Mac e aplicativos de terceiros.
- IMAP4 - Usado por clientes de e-mail do IMAP.
- MAPI sobre HTTP (MAPI/HTTP) - Usado pelo Outlook 2010 e posterior.
- Livro de endereços offline (OAB) - Uma cópia das coleções de listas de endereços que são baixadas e usadas pelo Outlook.
- Outlook Anywhere (RPC over HTTP) - Usado pelo Outlook 2016 e anterior.
- Serviço Outlook - Usado pelo aplicativo Mail and Calendar para Windows 10.
- POP3 - Usado por clientes de e-mail POP.
- Relatórios de Serviços Web - Usado para recuperar dados de relatórios no Exchange Online.
- Outros clientes - Outros protocolos identificados como utilizando autenticação herdada.

Para obter mais informações sobre esses protocolos e serviços de autenticação, consulte [relatórios de atividades de login no portal do Azure Active Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identificar o uso de autenticação legado

Antes de bloquear a autenticação do legado em seu diretório, você precisa primeiro entender se seus usuários têm aplicativos que usam autenticação legado e como isso afeta seu diretório geral. Os logs de login do Azure AD podem ser usados para entender se você estiver usando autenticação legado.

1. Navegue até o >  **portal Azure****Azure Active Directory** > **Sign-ins**.
1. Adicione a coluna Client App se ela não for mostrada clicando no**Aplicativo Cliente** **colunas** > .
1. **Adicionar filtros** > **O Client App** > selecionar todos os protocolos de autenticação legados e clicar em **Aplicar**.

A filtragem só mostrará as tentativas de login feitas por protocolos de autenticação legados. Clicar em cada tentativa de login individual mostrará detalhes adicionais. O campo **Client App** na guia **Informações Básicas** indicará qual protocolo de autenticação legado foi usado.

Esses logs indicarão quais usuários ainda estão dependendo da autenticação do legado e quais aplicativos estão usando protocolos legados para fazer solicitações de autenticação. Para usuários que não aparecem nesses registros e são confirmados que não estão usando autenticação legado, implemente uma política de Acesso Condicional apenas para esses usuários.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação herdada 

Em uma política de acesso condicional, você pode definir uma condição vinculada aos aplicativos clientes que são usados para acessar seus recursos. A condição de aplicativos cliente permite restringir o escopo a aplicativos usando autenticação herdada, selecionando **Outros clientes** para **Aplicativos móveis e clientes de desktop**.

![Outros clientes](./media/block-legacy-authentication/01.png)

Para bloquear o acesso a esses aplicativos, é necessário selecionar **Bloquear acesso**.

![Acesso bloqueado](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selecione usuários e aplicativos na nuvem

Se você quiser bloquear a autenticação herdada para sua organização, provavelmente pressupõe que é possível fazer isso, selecionando:

- todos os usuários
- Todos os aplicativos em nuvem
- Acesso bloqueado

![Atribuições](./media/block-legacy-authentication/03.png)

O Azure tem um recurso de segurança que impede que você crie uma política como essa porque essa configuração viola as [práticas recomendadas](best-practices.md) para políticas de Acesso Condicional.
 
![Sem suporte para configuração de política](./media/block-legacy-authentication/04.png)

O recurso de segurança é necessário porque *bloqueia todos os usuários e todos os aplicativos na nuvem* e tem o potencial de impedir que toda a organização faça autenticação no locatário. É necessário excluir pelo menos um usuário para satisfazer o requisito mínimo de melhor prática. Também é possível excluir uma função de diretório.

![Sem suporte para configuração de política](./media/block-legacy-authentication/05.png)

Você pode satisfazer esse recurso de segurança, excluindo um usuário da sua política. O ideal, é definir algumas [contas administrativas de acesso para emergência no Azure AD](../users-groups-roles/directory-emergency-access.md) e excluí-las da política.

Usar [o modo somente relatório](concept-conditional-access-report-only.md) ao habilitar sua política para bloquear a autenticação do legado fornece à sua organização uma oportunidade de monitorar qual seria o impacto da política.

## <a name="policy-deployment"></a>Implantação de política

Antes de colocar a política em produção, atente-se para:
 
- **Contas de serviço** - Identifique as contas de usuário que são usadas como contas de serviço ou por dispositivos, como telefones de sala de conferência. Certifique-se de que essas contas têm senhas fortes e adicione-as a um grupo excluído.
- **Relatórios de entradas** - Analise o relatório de entrada e procure **outro tráfego de cliente**. Identifique o uso principal e investigue por que ele está em uso. Normalmente, o tráfego é gerado por clientes mais antigos do Office que não usam autenticação moderna ou alguns aplicativos de email de terceiros. Faça um plano para afastar o uso desses aplicativos ou, se o impacto for baixo, notifique os usuários de que eles não podem mais usar esses aplicativos.
 
Para obter mais informações, consulte [Como implantar uma nova política?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>O que você deve saber

Bloqueando o acesso usando **Outros clientes** também bloqueia maquinado online do Exchange e do Dynamics 365 usando auth básico.

A configuração de uma política para **Outros clientes** bloqueia determinados clientes, como SPConnect, para toda a organização. Esse bloqueio acontece porque os clientes mais antigos são autenticados de maneiras inesperadas. O problema não se aplica aos principais aplicativos do Office, como os antigos clientes do Office.

Pode levar até 24 horas para que a política entre em vigor.

Você pode selecionar todos os controles de subvenção disponíveis para a condição **de Outros clientes;** no entanto, a experiência do usuário final é sempre a mesma - acesso bloqueado.

Se você bloquear a autenticação legado usando a condição **Outros clientes,** você também poderá definir a condição de localização e a plataforma do dispositivo. Por exemplo, se você quiser bloquear apenas a autenticação herdada para dispositivos móveis, defina a condição **plataformas de dispositivo** selecionando:

- Android
- iOS
- Windows Phone

![Sem suporte para configuração de política](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Próximas etapas

- [Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)
- Se você ainda não estiver familiarizado com a configuração de políticas de acesso condicional, consulte [exigir MFA para aplicativos específicos com o Azure Active Directory Conditional Access,](app-based-mfa.md) por exemplo.
- Para obter mais informações sobre o suporte de autenticação moderno, consulte [Como funciona a autenticação moderna para aplicativos clientes do Office 2013 e Office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
