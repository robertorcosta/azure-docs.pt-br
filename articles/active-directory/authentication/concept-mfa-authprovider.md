---
title: Azure Multi-Factor Auth Providers - Azure Active Directory
description: Quando você deve usar um Provedor de Autenticação com o Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a275e5ab394b54960a2340848152741762b28f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269376"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Quando usar um Provedor de Autenticação Multifator do Microsoft Azure

A autenticação em duas etapas está disponível por padrão para os administradores globais que têm o Azure Active Directory e os usuários do Office 365. No entanto, se você quiser aproveitar os [recursos avançados](howto-mfa-mfasettings.md), então, deverá adquirir a versão completa da Autenticação Multifator do Azure (MFA).

Um Provedor de Autenticação Multifator do Azure é usado para aproveitar os recursos fornecidos pela Autenticação Multifator do Microsoft Azure para usuários que **não têm licenças**.

> [!NOTE]
> A partir de 1º de setembro de 2018, novos provedores de autenticação não poderão mais ser criados. Os provedores de auth existentes podem continuar a ser usados e atualizados, mas a migração não é mais possível. A autenticação multifator continuará disponível como um recurso nas licenças do Microsoft Azure Active Directory Premium.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Limitações relacionadas ao SDK do Azure MFA

Observe que o SDK foi descontinuado e continuará funcionando até 14 de novembro de 2018. Após essa data, as chamadas para o SDK falharão.

## <a name="what-is-an-mfa-provider"></a>O que é um Provedor MFA?

Há dois tipos de provedores de autenticação e a diferença está em como a assinatura do Azure é cobrada. A opção por autenticação calcula o número de autenticações executadas em seu locatário em um mês. Essa será a melhor opção se você tiver um número de usuários que se autenticam apenas ocasionalmente. A opção por usuário calcula o número de pessoas no seu locatário que executam a verificação em duas etapas em um mês. Essa é a melhor opção se você tiver alguns usuários com licenças, mas precisar estender MFA para mais usuários, além de seus limites de licença.

## <a name="manage-your-mfa-provider"></a>Gerenciar seu `Provedor MFA

Não é possível alterar o modelo de uso (por usuário habilitado ou por autenticação) após a criação de um provedor de MFA.

Se você comprou licenças suficientes para cobrir todos os usuários que estão habilitados para MFA, é possível excluir o provedor MFA completamente.

Se o seu provedor de MFA não estiver vinculado a um locatário do Azure AD, ou você vincula o novo provedor de MFA a um locatário diferente do Azure AD, as opções de definições de usuário e de configuração não serão transferidas. Além disso, os servidores MFA existentes do Azure precisarão ser reativados usando as credenciais de ativação geradas por meio do provedor de MFA.

### <a name="removing-an-authentication-provider"></a>Removendo um provedor de autenticação

> [!CAUTION]
> Não há confirmação ao excluir um provedor de autenticação. Selecionar **Excluir** é um processo permanente.

Os provedores de autenticação podem ser encontrados > no portal >  **Azure****Azure Active Directory** > **Security** > **MFA****Providers**. Clique em provedores listados para ver detalhes e configurações associados a esse provedor.

Antes de remover um provedor de autenticação, observe todas as configurações personalizadas configuradas no provedor. Decida quais configurações precisam ser migradas para configurações Gerais de MFA do seu provedor e complete a migração dessas configurações. 

Os servidores MFA do Azure vinculados aos provedores precisarão ser reativados usando credenciais**geradas**nas configurações do**Servidor MFA** > **de Segurança** > ativa do Diretório > Ativo **do Azure** > **Azure Active Directory**. Antes de reativar, os seguintes arquivos `\Program Files\Multi-Factor Authentication Server\Data\` devem ser excluídos do diretório em servidores MFA do Azure em seu ambiente:

- caCert
- cert
- groupCACert
- groupKey
- groupName
- Licensekey
- Pkey

![Exclua um provedor auth do portal Azure](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Quando você confirmou que todas as configurações foram migradas, > você pode navegar até o portal**Azure Azure Active Directory** > **Security** > **MFA** > **Providers** e selecionar as elipses **...** e selecionar **Excluir**. **Azure portal**

> [!WARNING]
> A exclusão de um provedor de autenticação excluirá todas as informações de emissão de relatórios associadas a esse provedor. Você pode querer salvar relatórios de atividade antes de excluir seu provedor.

> [!NOTE]
> Usuários com versões mais antigas do aplicativo Microsoft Authenticator e do Azure MFA Server podem precisar recadastrar seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

[Definir as configurações da Autenticação Multifator](howto-mfa-mfasettings.md)
