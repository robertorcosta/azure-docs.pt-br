---
title: Gerenciar certificados de federação no Azure AD | Microsoft Docs
description: Saiba como personalizar a data de vencimento para seus certificados de federação e como renovar certificados que vencerão em breve.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159022"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gerenciar certificados para logon único federado no Azure Active Directory

Neste artigo, abordamos perguntas comuns e informações relacionadas aos certificados que o Azure Active Directory (Azure AD) cria para estabelecer o SSO (Single Signon) federado para seus aplicativos de software como serviço (SaaS). Adicione aplicativos pela galeria de aplicativos do Azure AD ou usando um modelo de aplicativo inexistente na galeria. Configure o aplicativo usando a opção de SSO federada.

Este artigo é relevante apenas para aplicativos que são configurados para usar o Azure AD SSO através da federação [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificado gerado automaticamente para aplicativos da galeria e inexistentes na galeria

Quando você adiciona um novo aplicativo da galeria e configura um login baseado em SAML (selecionando**SAML** **de login** > único na página de visão geral do aplicativo), o Azure AD gera um certificado para o aplicativo válido por três anos. Para baixar o certificado ativo como um arquivo de certificado de segurança **(.cer),** retorne a essa página **(assinatura baseada em SAML)** e selecione um link de download no título **do Certificado de Assinatura SAML.** Você pode escolher entre o certificado bruto (binário) ou o certificado Base64 (texto codificado base 64). Para aplicativos de galeria, esta seção também pode mostrar um link para baixar o certificado como metadados da federação XML (um arquivo **.xml),** dependendo da exigência do aplicativo.

![Opções de download de certificados de assinatura ativos SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Você também pode baixar um certificado ativo ou inativo selecionando o ícone **editar** do certificado de **assinatura SAML** (um lápis), que exibe a página **Certificado de Assinatura SAML.** Selecione a elipse (**...**) ao lado do certificado que deseja baixar e, em seguida, escolha qual formato de certificado deseja. Você tem a opção adicional de baixar o certificado no formato PEM (Privacy-enhanced mail). Este formato é idêntico ao Base64, mas com uma extensão de nome de arquivo **.pem,** que não é reconhecida no Windows como um formato de certificado.

![Opções de download de certificados de assinatura SAML (ativos e inativos)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizar a data de vencimento do seu certificado de federação e transferir para um novo certificado

Por padrão, o Azure configura um certificado para expirar após três anos quando ele é criado automaticamente durante a configuração de login único SAML. Porque você não pode alterar a data de um certificado depois de salvá-lo, você tem que:

1. Crie um novo certificado com a data desejada.
1. Guarde o novo certificado.
1. Baixe o novo certificado no formato correto.
1. Envie o novo certificado para o aplicativo.
1. Faça o novo certificado ativo no portal Diretório Ativo do Azure.

As duas seções a seguir ajudam você a executar essas etapas.

### <a name="create-a-new-certificate"></a>Crie um novo certificado

Primeiro, crie e salve um novo certificado com uma data de validade diferente:

1. Faça login no portal do Diretório Ativo do [Azure](https://aad.portal.azure.com/). A página **central do diretório ativo do Azure** é exibida.
1. No painel esquerdo, selecione **Aplicativos Empresariais**. Uma lista dos aplicativos corporativos em sua conta é exibida.
1. Selecione o aplicativo afetado. Uma página de visão geral do aplicativo é exibida.
1. No painel esquerdo da página de visão geral do aplicativo, selecione **'Apostação única ''''''''**
1. Se a **página Selecionar uma única** página do método de login for exibida, selecione **SAML**.
1. Na **página Configurar único de login com SAML - Preview,** encontre o título **do Certificado de Assinatura SAML** e selecione o ícone **Editar** (um lápis). A página **do Certificado de Assinatura SAML** é exibida, que exibe o status **(Ativo** ou **Inativo),** a data de validade e a impressão digital (uma seqüência de hash) de cada certificado.
1. Selecione **Novo Certificado**. Uma nova linha aparece abaixo da lista de certificados, onde a data de validade é padrão para exatamente três anos após a data atual. (Suas alterações ainda não foram salvas, então você ainda pode modificar a data de validade.)
1. Na nova linha de certificado, adoeça sobre a coluna de data de validade e selecione o ícone **Selecionar data** (um calendário). Um controle de calendário é exibido, exibindo os dias de um mês da data de validade atual da nova linha.
1. Use o controle de calendário para definir uma nova data. Você pode definir qualquer data entre a data atual e três anos após a data atual.
1. Selecione **Salvar**. O novo certificado agora aparece com um status de **Inativo**, a data de validade que você escolheu e uma impressão digital.
1. Selecione o **X** para retornar à **página Configurar único de inscrição com saml - Página de visualização.**

### <a name="upload-and-activate-a-certificate"></a>Carregue e ative um certificado

Em seguida, baixe o novo certificado no formato correto, carregue-o para o aplicativo e torne-o ativo no Azure Active Directory:

1. Veja as instruções adicionais de configuração de login SAML do aplicativo por:

   - Selecionando o link **do guia de configuração** para exibir em uma janela ou guia separada do navegador, ou
   - Indo para o título **de configuração** e selecionando **Exibir instruções passo a passo** para exibir em uma barra lateral.

1. Nas instruções, observe o formato de codificação necessário para o upload do certificado.
1. Siga as instruções na seção [de aplicativos gerados automaticamente para aplicativos de galeria e não-galeria](#auto-generated-certificate-for-gallery-and-non-gallery-applications) anteriormente. Esta etapa baixa o certificado no formato de codificação necessário para upload pelo aplicativo.
1. Quando você quiser passar para o novo certificado, volte para a página **do Certificado de Assinatura SAML** e, na linha de certificados recém-salva, selecione a ellipsis (**...**) e selecione Make **certificate active**. O status do novo certificado muda para **Ativo**e o certificado anteriormente ativo muda para um status de **Inativo**.
1. Continue seguindo as instruções de configuração de login SAML do aplicativo que você exibiu anteriormente, para que você possa carregar o certificado de assinatura SAML no formato correto de codificação.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Adicionar endereços de notificação de e-mail para expiração de certificados

O Azure AD enviará uma notificação por e-mail 60, 30 e 7 dias antes do certificado SAML expirar. Você pode adicionar mais de um endereço de e-mail para receber notificações. Para especificar o endereço de e-mail(es) deseja que as notificações sejam enviadas para:

1. Na página do **Certificado de Assinatura SAML,** acesse o título de **endereços de e-mail** de notificação. Por padrão, esse título usa apenas o endereço de e-mail do administração que adicionou o aplicativo.
1. Abaixo do endereço de e-mail final, digite o endereço de e-mail que deve receber o aviso de expiração do certificado e, em seguida, pressione Enter.
1. Repita a etapa anterior para cada endereço de e-mail que você deseja adicionar.
1. Para cada endereço de e-mail que você deseja excluir, selecione o ícone **Excluir** (uma lata de lixo) ao lado do endereço de e-mail.
1. Selecione **Salvar**.

Você receberá o email de notificação de aadnotification@microsoft.com. Para evitar que o e-mail vá para a sua localização de spam, adicione este e-mail aos seus contatos.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar um certificado que vencerá em breve

Se um certificado estiver prestes a expirar, você pode renová-lo usando um procedimento que não resulta em nenhum tempo de inatividade significativo para seus usuários. Para renovar um certificado de expiração:

1. Siga as instruções na [Seção Criar uma nova](#create-a-new-certificate) seção de certificado anteriormente, usando uma data que se sobrepõe ao certificado existente. Essa data limita a quantidade de tempo de inatividade causado pelo vencimento do certificado.
1. Se o aplicativo puder rolar automaticamente um certificado, defina o novo certificado como ativo seguindo estas etapas:
   1. Volte para a página **do Certificado de Assinatura SAML.**
   1. Na linha de certificadorecém-salva, selecione a ellipsis (**...**) e, em seguida, selecione **Tornar o certificado ativo**.
   1. Pule os próximos dois passos.

1. Se o aplicativo só puder lidar com um certificado de cada vez, escolha um intervalo de tempo de inatividade para executar o próximo passo. (Caso contrário, se o aplicativo não pegar automaticamente o novo certificado, mas pode lidar com mais de um certificado de assinatura, você pode executar a próxima etapa a qualquer momento.)
1. Antes de expirar o certificado antigo, siga as instruções no [Upload e ative uma](#upload-and-activate-a-certificate) seção de certificado anteriormente.
1. Faça login no aplicativo para certificar-se de que o certificado funciona corretamente.

## <a name="related-articles"></a>Artigos relacionados

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](../saas-apps/tutorial-list.md)
- [Gerenciamento de aplicativo com o Azure Active Directory](what-is-application-management.md)
- [Logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md)
- [Depurar o login único baseado em SAML para aplicativos no Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
