---
title: Bloqueando protocolos de autenticação legado no Azure AD
description: Saiba como e por que as organizações devem bloquear protocolos de autenticação legado
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55ce0233fdefb8360376e94c0baafabe4c62ced7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309203"
---
# <a name="blocking-legacy-authentication"></a>Bloqueando a autenticação do legado
 
Para fornecer aos usuários acesso fácil aos aplicativos na nuvem, o Azure AD (Azure Active Directory) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação herdada. Autenticação legado é um termo que se refere a uma solicitação de autenticação feita por:

- Clientes antigos do Office que não usam autenticação moderna (por exemplo, cliente Office 2010)
- Qualquer cliente que use protocolos de e-mail legados como IMAP/SMTP/POP3

Hoje, a maioria de todas as tentativas de login comprometedoras vem da autenticação do legado. A autenticação do legado não suporta autenticação multifatorial (MFA). Mesmo que você tenha uma política MFA habilitada em seu diretório, um ator ruim pode autenticar usando um protocolo legado e contornar o MFA. A melhor maneira de proteger sua conta contra solicitações de autenticação maliciosas feitas por protocolos legados é bloquear essas tentativas completamente.

## <a name="identify-legacy-authentication-use"></a>Identificar o uso de autenticação legado

Antes de bloquear a autenticação do legado em seu diretório, você precisa primeiro entender se seus usuários têm aplicativos que usam autenticação legado e como isso afeta seu diretório geral. Os logs de login do Azure AD podem ser usados para entender se você estiver usando autenticação legado.

1. Navegue até o > **portal Azure** **Azure Active Directory** > **Sign-ins**.
1. Adicione a coluna **Client App** se ela não for mostrada clicando no **Aplicativo Cliente** **colunas** >.
1. Filter by **Client App**  > verificar todas as opções **de Clientes de Autenticação Legado apresentadas.**
1. Filtrar por **sucesso de** > **status**. 
1. Expanda seu intervalo de datas, se necessário, usando o filtro **Data.**

A filtragem só mostrará as tentativas de login bem-sucedidas feitas pelos protocolos de autenticação legado selecionados. Clicar em cada tentativa de login individual mostrará detalhes adicionais. A coluna Client App ou o campo Client App na guia Informações Básicas após selecionar uma linha individual de dados indicará qual protocolo de autenticação legado foi usado. Esses logs indicarão quais usuários ainda estão dependendo da autenticação do legado e quais aplicativos estão usando protocolos legados para fazer solicitações de autenticação. Para usuários que não aparecem nesses logs e são confirmados que não estão usando autenticação herdada, implemente uma política de Acesso Condicional ou habilite a política De linha de base: bloqueie a autenticação legado apenas para esses usuários.

## <a name="moving-away-from-legacy-authentication"></a>Afastando-se da autenticação do legado 

Uma vez que você tenha uma ideia melhor de quem está usando a autenticação do legado em seu diretório e quais aplicativos dependem dele, o próximo passo é atualizar seus usuários para usar a autenticação moderna. A autenticação moderna é um método de gerenciamento de identidade que oferece autenticação e autorização mais seguras do usuário. Se você tiver uma política MFA em vigor em seu diretório, a autenticação moderna garante que o usuário seja solicitado para MFA quando necessário. É a alternativa mais segura aos protocolos de autenticação legado.

Esta seção fornece uma visão geral passo a passo sobre como atualizar seu ambiente para a autenticação moderna. Leia as etapas abaixo antes de ativar uma política de bloqueio de autenticação legado em sua organização.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passo 1: Habilite a autenticação moderna em seu diretório

O primeiro passo para habilitar a autenticação moderna é garantir que seu diretório suporte a autenticação moderna. A autenticação moderna é habilitada por padrão para diretórios criados em ou após 1 º de agosto de 2017. Se o diretório foi criado antes desta data, você precisará ativar manualmente a autenticação moderna para o seu diretório usando as seguintes etapas:

1. Verifique se seu diretório já suporta autenticação `Get-CsOAuthConfiguration` moderna executando a partir do [módulo Skype for Business Online PowerShell](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se o seu `OAuthServers` comando retornar uma propriedade vazia, então a Autenticação Moderna será desativada. Atualize a configuração para `Set-CsOAuthConfiguration`habilitar a autenticação moderna usando . Se `OAuthServers` sua propriedade contém uma entrada, você está pronto para ir.

Certifique-se de completar este passo antes de seguir em frente. É fundamental que suas configurações de diretório sejam alteradas primeiro porque ditam qual protocolo será usado por todos os clientes do Office. Mesmo que você esteja usando clientes do Office que suportam autenticação moderna, eles serão padrão para usar protocolos legados se a autenticação moderna estiver desativada em seu diretório.

### <a name="step-2-office-applications"></a>Passo 2: Aplicações do escritório

Depois de habilitar a autenticação moderna em seu diretório, você pode começar a atualizar aplicativos, permitindo autenticação moderna para clientes do Office. Os clientes do Office 2016 ou posteriores suportam autenticação moderna por padrão. Não são necessários passos extras.

Se você estiver usando clientes Windows do Office 2013 ou mais velhos, recomendamos atualizar para o Office 2016 ou posterior. Mesmo depois de completar a etapa anterior de habilitar a autenticação moderna em seu diretório, os aplicativos do Office mais antigos continuarão a usar protocolos de autenticação legados. Se você estiver usando clientes do Office 2013 e não puder atualizar imediatamente para o Office 2016 ou posterior, siga os passos do artigo a seguir para [habilitar autenticação moderna para o Office 2013 em dispositivos Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Para ajudar a proteger sua conta enquanto estiver usando a autenticação do legado, recomendamos o uso de senhas fortes em todo o seu diretório. Confira [a proteção](../authentication/concept-password-ban-bad.md) por senha do Azure AD para banir senhas fracas em seu diretório.

O Office 2010 não suporta autenticação moderna. Você precisará atualizar todos os usuários com o Office 2010 para uma versão mais recente do Office. Recomendamos atualizar para o Office 2016 ou posterior, pois bloqueia a autenticação do legado por padrão.

Se você estiver usando macOS, recomendamos atualizar para o Office para Mac 2016 ou posterior. Se você estiver usando o cliente de e-mail nativo, você precisará ter a versão 10.14 do macOS ou posterior em todos os dispositivos.

### <a name="step-3-exchange-and-sharepoint"></a>Passo 3: Exchange e SharePoint

Para que os clientes do Outlook baseados no Windows usem autenticação moderna, o Exchange Online também deve ser habilitado para autenticação moderna. Se a autenticação moderna for desativada para o Exchange Online, os clientes do Outlook baseados no Windows que suportam autenticação moderna (Outlook 2013 ou posterior) usarão autenticação básica para se conectar às caixas de correio do Exchange Online.

O SharePoint Online está habilitado para o padrão de autenticação moderno. Para diretórios criados após 1º de agosto de 2017, a autenticação moderna é habilitada por padrão no Exchange Online. No entanto, se você já havia desativado a autenticação moderna anteriormente ou está usando um diretório criado antes desta data, siga os passos do artigo a seguir para Habilitar a [autenticação moderna no Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Passo 4: Skype para negócios

Para evitar solicitações de autenticação herdada feitas pelo Skype for Business, é necessário habilitar a autenticação moderna para Skype for Business Online. Para diretórios criados após 1º de agosto de 2017, a autenticação moderna para Skype for Business é habilitada por padrão.

Sugerimos que você faça a transição para o Microsoft Teams, que suporta autenticação moderna por padrão. No entanto, se você não puder migrar neste momento, você precisará habilitar a autenticação moderna para Skype for Business Online para que os clientes do Skype for Business comecem a usar a autenticação moderna. Siga os passos deste artigo [Skype for Business topologies suportadas com Autenticação Moderna,](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)para permitir autenticação moderna para Skype for Business.

Além de permitir a autenticação moderna para skype for Business Online, recomendamos permitir a autenticação moderna para o Exchange Online ao permitir a autenticação moderna para skype for business. Esse processo ajudará a sincronizar o estado de autenticação moderna no Exchange Online e no Skype for Business on-line e impedirá vários pedidos de login para clientes do Skype for Business.

### <a name="step-5-using-mobile-devices"></a>Passo 5: Usando dispositivos móveis

Os aplicativos em seu dispositivo móvel também precisam bloquear a autenticação legada. Recomendamos o uso do Outlook for Mobile. O Outlook for Mobile suporta autenticação moderna por padrão e satisfará outras políticas de proteção de linha de base do MFA.

Para usar o cliente de e-mail nativo do iOS, você precisará estar executando a versão 11.0 do iOS ou posterior para garantir que o cliente de e-mail tenha sido atualizado para bloquear a autenticação do legado.

### <a name="step-6-on-premises-clients"></a>Passo 6: Clientes no local

Se você é um cliente híbrido usando o Exchange Server no local e o Skype for Business no local, ambos os serviços precisarão ser atualizados para permitir a autenticação moderna. Ao usar a autenticação moderna em um ambiente híbrido, você ainda está autenticando os usuários no local. A história de autorizar seu acesso a recursos (arquivos ou e-mails) muda.

Antes de começar a habilitar a autenticação moderna no local, certifique-se de que você cumpriu os pré-requisitos. Agora você está pronto para habilitar a autenticação moderna no local.

Etapas para habilitar a autenticação moderna podem ser encontradas nos seguintes artigos:

* [Como configurar o Exchange Server no local para usar a Autenticação Híbrida Moderna](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Como usar a Autenticação Moderna (ADAL) com o Skype for Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Próximas etapas

- [Como configurar o Exchange Server no local para usar a Autenticação Híbrida Moderna](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Como usar a Autenticação Moderna (ADAL) com o Skype for Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Bloquear a autenticação herdada](../conditional-access/block-legacy-authentication.md)
