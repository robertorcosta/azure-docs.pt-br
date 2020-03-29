---
title: Recursos seguros com OZure MFA e ADFS - Azure Active Directory
description: Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS na nuvem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00200436784eca970f736c4a7f2afebd652c9577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155206"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Protegendo os recursos de nuvem usando a Autenticação Multifator do Azure e o AD FS

Se sua organização for federada com o Azure Active Directory, use a Autenticação Multifator do Azure ou os Serviços de Federação do Active Directory (AD FS) para proteger os recursos que são acessados pelo Azure AD. Use os procedimentos a seguir para proteger os recursos do Azure Active Directory com Autenticação Multifator do Azure ou os Serviços de Federação do Active Directory.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger recursos do Azure AD usando o AD FS

Para proteger seus recursos de nuvem, configure uma regra de declaração para que os Serviços de Federação do Active Directory emitem a declaração multipleauthn quando um usuário executa a verificação em duas etapas com êxito. Essa declaração é passada para o Azure AD. Siga este procedimento para percorrer as etapas:

1. Abra o gerenciamento do AD FS.
2. À esquerda, selecione **Relações de Confiança com Terceira Parte Confiável**.
3. Clique com o botão direito do mouse na **plataforma de identidade do Microsoft Office 365** e selecione Editar regras de **reivindicação**.

   ![Console ADFS - Trusting Party Trusts](./media/howto-mfa-adfs/trustedip1.png)

4. Em Regras de transformação de emissão, clique **em Adicionar regra**.

   ![Edição de regras de transformação de emissão](./media/howto-mfa-adfs/trustedip2.png)

5. No Assistente Adicionar Regra de Declaração de Transformação, selecione **Passar ou filtrar uma Declaração de Entrada** na lista e clique em **Avançar**.

   ![Assistente para Adicionar Regra de Declaração de Transformação](./media/howto-mfa-adfs/trustedip3.png)

6. Dê um nome para a regra. 
7. Selecione **Referências de Métodos de Autenticação** como o tipo de declaração Entrada.
8. Selecione **Passar por todos os valores de sinistro**.
    ![Assistente para Adicionar Regra de Declaração de Transformação](./media/howto-mfa-adfs/configurewizard.png)
9. Clique em **concluir**. Feche o Console de gerenciamento do AD FS.

## <a name="trusted-ips-for-federated-users"></a>IPs confiáveis para usuários federados

IPs confiáveis permitem aos administradores ignorar a verificação em duas etapas para endereços IP específicos ou para usuários federados que têm as solicitações originadas em seu próprios intranet. As seções a seguir descrevem como configurar IPs confiáveis da Autenticação Multifator do Azure com usuários federados e desviar a verificação em duas etapas quando uma solicitação se originar de dentro de uma intranet de usuários federados. Isso é conseguido por meio da configuração do AD FS para usar uma passagem ou filtrar um modelo de declaração de entrada com o tipo de declaração Dentro da rede corporativa.

Este exemplo usa o Office 365 para a relação de confiança com terceira parte confiável.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurar as regras de declarações do AD FS

A primeira coisa que precisamos fazer é configurar as declarações do AD FS. Criamos duas regras declarações: uma para o tipo de declaração Dentro da rede corporativa e um adicional para manter nossos usuários conectados.

1. Abra o gerenciamento do AD FS.
2. À esquerda, selecione **Relações de Confiança com Terceira Parte Confiável**.
3. Clique com o botão direito do mouse na **plataforma de identidade do Microsoft Office 365** e selecione Editar regras de **reivindicação...** 
   Console ADFS - Editar regras de ![reivindicação](./media/howto-mfa-adfs/trustedip1.png)
4. Em Regras de transformação de emissão, clique **em Adicionar regra.** 
   Adicionando uma regra de ![reivindicação](./media/howto-mfa-adfs/trustedip2.png)
5. No Assistente Adicionar Regra de Declaração de Transformação, selecione **Passar ou filtrar uma Declaração de Entrada** na lista e clique em **Avançar**.
   ![Assistente para Adicionar Regra de Declaração de Transformação](./media/howto-mfa-adfs/trustedip3.png)
6. Na caixa ao lado do nome da regra de declaração, nomeie a regra. Por exemplo: InsideCorpNet.
7. Na lista suspensa, ao lado do tipo de declaração de entrada, selecione **Dentro da rede corporativa**.
   ![Adicionando a reivindicação da Rede Corporativa](./media/howto-mfa-adfs/trustedip4.png)
8. Clique em **concluir**.
9. Em Regras de transformação de emissão, clique **em Adicionar regra**.
10. No Assistente Adicionar Regra de Declaração de Transformação, selecione **Enviar Declarações Usando uma Regra Personalizada** da lista suspensa e clique em **Avançar**.
11. Na caixa em Nome da regra De sinistro: *digite Manter os usuários assinados*.
12. Na caixa de regra Personalizada, digite:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Crie reivindicação personalizada para manter os usuários logados](./media/howto-mfa-adfs/trustedip5.png)
13. Clique em **concluir**.
14. Clique em **Aplicar**.
15. Clique em **Ok**.
16. Feche o gerenciamento do AD FS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurar IPs confiáveis da Autenticação Multifator do Azure com usuários federados

Agora que as declarações estão prontas, podemos configurar IPs confiáveis.

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione locais**Security** > **nomeados** > **para**o acesso condicionado do diretório ativo do >  **Azure**.
3. A partir da lâmina **de acesso condicional - nomeado,** selecione Configurar **IPs confiáveis mfa**

   ![Azure AD Conditional Access nomeado locais Configurar IPs confiáveis MFA](./media/howto-mfa-adfs/trustedip6.png)

4. Na página Configurações de Serviço, em **IPs confiáveis**, selecione **Ignorar autenticação multifator para solicitações de usuários federados na minha intranet**.  
5. Clique **em salvar**.

É isso! Neste ponto, os usuários federados do Office 365 devem somente ter que usar MFA quando uma declaração for originada fora da intranet corporativa.
