---
title: Bloquear autenticação herdada-Azure Active Directory
description: Saiba como melhorar sua postura de segurança bloqueando a autenticação herdada usando o acesso condicional do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957aa77e18ea8f910f258d1dc59de0d093b0eab6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476637"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como bloquear a autenticação herdada no Azure AD com acesso condicional   

Para fornecer aos usuários acesso fácil aos aplicativos na nuvem, o Azure AD (Azure Active Directory) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação herdada. No entanto, os protocolos herdados não dão suporte à MFA (autenticação multifator). Em muitos ambientes, a MFA é um requisito comum para lidar com roubo de identidade. 

Alex Weinert, diretor de segurança de identidade na Microsoft, em sua postagem de blog de 12 de março de 2020 [novas ferramentas para bloquear a autenticação herdada em sua organização](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) enfatiza o motivo pelo qual as organizações devem bloquear a autenticação herdada e as ferramentas adicionais que a Microsoft fornece para realizar essa tarefa:

> Para que o MFA seja eficaz, você também precisa bloquear a autenticação herdada. Isso ocorre porque os protocolos de autenticação herdados como POP, SMTP, IMAP e MAPI não podem impor o MFA, tornando-os pontos de entrada preferenciais para os adversários que atacam sua organização...
> 
>... Os números na autenticação herdada de uma análise do tráfego do Azure Active Directory (AD do Azure) são Stark:
> 
> - Mais de 99% dos ataques de irrigação de senha usam protocolos de autenticação herdados
> - Mais de 97 por cento da credencial que atacam os ataques usam autenticação herdada
> - Contas do Azure AD em organizações que desabilitaram a experiência de autenticação herdada de 67% menos comprometimentos do que aquelas em que a autenticação herdada está habilitada
>

Se o seu ambiente estiver pronto para bloquear a autenticação herdada para melhorar a proteção do locatário, você poderá atingir esse objetivo com o acesso condicional. Este artigo explica como você pode configurar políticas de acesso condicional que bloqueiam a autenticação herdada para seu locatário.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- Os [conceitos básicos](overview.md) do acesso condicional do Azure AD 
- As [práticas recomendadas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure

## <a name="scenario-description"></a>Descrição do cenário

O Azure AD dá suporte para vários dos protocolos de autenticação e autorização mais amplamente utilizados, incluindo a autenticação herdada. Autenticação herdada refere-se a protocolos que usam autenticação básica. Normalmente, esses protocolos não podem impor nenhum tipo de autenticação de segundo fator. Exemplos de aplicativos baseados em autenticação herdada são:

- Aplicativos mais antigos do Microsoft Office
- Aplicativos que usam protocolos de email como POP, IMAP e SMTP

Autenticação de fator único (por exemplo, nome de usuário e senha) atualmente não é suficiente. Senhas são ruins porque são fáceis de adivinhar e nós (humanos) dificilmente escolhemos boas senhas. Senhas também são vulneráveis a uma variedade de ataques, como pulverização de senha e phishing. Uma das medidas mais fáceis que você pode tomar para proteção contra ameaças de senha é implementar MFA. Com MFA, mesmo se um invasor possuir a senha de um usuário, somente a senha não será suficiente para autenticar e acessar os dados com êxito.

Como é possível impedir que aplicativos usando autenticação herdada acessem os recursos do locatário? A recomendação é simplesmente bloqueá-los com uma política de acesso condicional. Se necessário, você permite que apenas determinados usuários e locais de rede específicos usem aplicativos baseados em autenticação herdada.

As políticas de Acesso Condicional são impostas após a conclusão da autenticação multifator. Portanto, o Acesso Condicional não funciona como uma primeira linha de defesa para cenários como ataques de DoS (ataque de negação de serviço), mas pode utilizar os sinais desses eventos (por exemplo, o nível de risco de entrada, a localização da solicitação e assim por diante) para determinar o acesso.

## <a name="implementation"></a>Implementação

Esta seção explica como configurar uma política de acesso condicional para bloquear a autenticação herdada. 

### <a name="legacy-authentication-protocols"></a>Protocolos de autenticação herdados

As opções a seguir são consideradas protocolos de autenticação herdados

- SMTP autenticado-usado pelo cliente POP e IMAP para enviar mensagens de email.
- Descoberta automática-usada pelos clientes do Outlook e do EAS para localizar e conectar-se às caixas de correio no Exchange Online.
- Exchange Online PowerShell-usado para se conectar ao Exchange Online com o PowerShell remoto. Se você bloquear a autenticação básica para o Exchange Online PowerShell, será necessário usar o módulo do PowerShell do Exchange Online para se conectar. Para obter instruções, consulte [conectar-se ao Exchange Online PowerShell usando a autenticação multifator](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Serviços Web do Exchange (EWS)-uma interface de programação usada pelo Outlook, Outlook para Mac e aplicativos de terceiros.
- IMAP4 – usado por clientes de email IMAP.
- MAPI sobre HTTP (MAPI/HTTP) – usado pelo Outlook 2010 e posterior.
- OAB (catálogo de endereços offline) – uma cópia das coleções de listas de endereços que são baixadas e usadas pelo Outlook.
- Outlook Anywhere (RPC sobre HTTP) – usado pelo Outlook 2016 e anterior.
- Serviço do Outlook – usado pelo aplicativo de email e calendário para Windows 10.
- POP3-usado por clientes de email POP.
- Serviços Web de relatórios-usados para recuperar dados de relatório no Exchange Online.
- Outros clientes-outros protocolos identificados como utilizando a autenticação herdada.

Para obter mais informações sobre esses protocolos e serviços de autenticação, consulte [relatórios de atividade de entrada no portal de Azure Active Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identificar o uso de autenticação herdada

Antes de poder bloquear a autenticação herdada em seu diretório, primeiro você precisa entender se os usuários têm aplicativos que usam autenticação herdada e como ele afeta o diretório geral. Os logs de entrada do Azure AD podem ser usados para entender se você está usando a autenticação herdada.

1. Navegue até o **portal do Azure** > **Azure Active Directory** > **entradas**.
1. Adicione a coluna aplicativo cliente se ela não for exibida clicando em **colunas** > **aplicativo cliente**.
1. **Adicionar** > **aplicativo cliente** de filtros > selecione todos os protocolos de autenticação herdados e clique em **aplicar**.

A filtragem mostrará apenas as tentativas de entrada feitas por protocolos de autenticação herdados. Clicar em cada tentativa de entrada individual mostrará detalhes adicionais. O campo **aplicativo cliente** na guia **informações básicas** indicará qual protocolo de autenticação herdado foi usado.

Esses logs indicarão quais usuários ainda estão dependendo da autenticação herdada e quais aplicativos estão usando protocolos herdados para fazer solicitações de autenticação. Para usuários que não aparecem nesses logs e são confirmados para não usar a autenticação herdada, implemente uma política de acesso condicional somente para esses usuários.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação herdada 

Em uma política de acesso condicional, você pode definir uma condição que esteja vinculada aos aplicativos cliente que são usados para acessar seus recursos. A condição aplicativos cliente permite que você restrinja o escopo a aplicativos usando a autenticação herdada selecionando **clientes do Exchange ActiveSync** e **outros clientes** em **aplicativos móveis e clientes de desktop**.

![Outros clientes](./media/block-legacy-authentication/01.png)

Para bloquear o acesso a esses aplicativos, é necessário selecionar **Bloquear acesso**.

![Acesso bloqueado](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selecione usuários e aplicativos na nuvem

Se você quiser bloquear a autenticação herdada para sua organização, provavelmente pressupõe que é possível fazer isso, selecionando:

- todos os usuários
- Todos os aplicativos em nuvem
- Acesso bloqueado

![Atribuições](./media/block-legacy-authentication/03.png)

O Azure tem um recurso de segurança que impede a criação de uma política como essa, pois essa configuração viola as [práticas recomendadas](best-practices.md) para políticas de acesso condicional.
 
![Sem suporte para configuração de política](./media/block-legacy-authentication/04.png)

O recurso de segurança é necessário porque *bloqueia todos os usuários e todos os aplicativos na nuvem* e tem o potencial de impedir que toda a organização faça autenticação no locatário. É necessário excluir pelo menos um usuário para satisfazer o requisito mínimo de melhor prática. Também é possível excluir uma função de diretório.

![Sem suporte para configuração de política](./media/block-legacy-authentication/05.png)

Você pode satisfazer esse recurso de segurança, excluindo um usuário da sua política. O ideal, é definir algumas [contas administrativas de acesso para emergência no Azure AD](../users-groups-roles/directory-emergency-access.md) e excluí-las da política.

Usar o [modo somente de relatório](concept-conditional-access-report-only.md) ao habilitar sua política para bloquear a autenticação herdada fornece à sua organização uma oportunidade de monitorar qual será o impacto da política.

## <a name="policy-deployment"></a>Implantação de política

Antes de colocar a política em produção, atente-se para:
 
- **Contas de serviço** - Identifique as contas de usuário que são usadas como contas de serviço ou por dispositivos, como telefones de sala de conferência. Certifique-se de que essas contas têm senhas fortes e adicione-as a um grupo excluído.
- **Relatórios de entradas** - Analise o relatório de entrada e procure **outro tráfego de cliente**. Identifique o uso principal e investigue por que ele está em uso. Normalmente, o tráfego é gerado por clientes mais antigos do Office que não usam autenticação moderna ou alguns aplicativos de email de terceiros. Faça um plano para afastar o uso desses aplicativos ou, se o impacto for baixo, notifique os usuários de que eles não podem mais usar esses aplicativos.
 
Para obter mais informações, consulte [Como implantar uma nova política?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>O que você deve saber

O bloqueio de acesso usando **outros clientes** também bloqueia o Exchange Online PowerShell e o Dynamics 365 usando a autenticação básica.

A configuração de uma política para **Outros clientes** bloqueia determinados clientes, como SPConnect, para toda a organização. Esse bloqueio acontece porque os clientes mais antigos são autenticados de maneiras inesperadas. O problema não se aplica aos principais aplicativos do Office, como os antigos clientes do Office.

Pode levar até 24 horas para que a política entre em vigor.

Você pode selecionar todos os controles de concessão disponíveis para a condição **outros clientes** ; no entanto, a experiência do usuário final é sempre o mesmo acesso bloqueado.

Se você bloquear a autenticação herdada usando a condição **outros clientes** , também poderá definir a plataforma do dispositivo e a condição de local. Por exemplo, se você quiser bloquear apenas a autenticação herdada para dispositivos móveis, defina a condição **plataformas de dispositivo** selecionando:

- Android
- iOS
- Windows Phone

![Sem suporte para configuração de política](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Próximas etapas

- [Determinar o impacto usando o modo somente relatório de acesso condicional](howto-conditional-access-report-only.md)
- Se você ainda não estiver familiarizado com a configuração de políticas de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md) para obter um exemplo.
- Para obter mais informações sobre o suporte à autenticação moderna, consulte [como funciona a autenticação moderna para aplicativos cliente do office 2013 e do office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
