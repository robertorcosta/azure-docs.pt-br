---
title: Experiência de entrada do usuário através de SMS para números de telefone (versão prévia) – Azure AD
description: Saiba mais sobre a experiência de entrada do usuário através de SMS para números de telefone novos ou existentes
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 361e8c63dc036443e527d1027fe3fd0cca784d9a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747200"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Uso do número de telefone como um nome de usuário (versão prévia)

Registrar um dispositivo permite que o telefone acesse os serviços da sua organização, mas não permite que sua organização acesse seu dispositivo. Se você for um administrador, poderá encontrar mais informações em [Configuração e habilitação de usuários para autenticação baseada em SMS](../authentication/howto-authentication-sms-signin.md).

Se sua organização não tiver disponibilizado a entrada do usuário através de SMS, você não verá essa opção quando registrar um telefone com a sua conta.  

## <a name="when-you-have-a-new-phone-number"></a>Quando você tem um novo número de telefone

Se você tiver um novo telefone ou um novo número e você o registra em uma organização para a qual a entrada através de SMS está disponível, sua experiência será o processo normal de registro de telefone:

1. Selecione **Adicionar método**.
1. Selecione **Telefone**.
1. Insira o número de telefone e selecione **Enviar um código por SMS**.
1. Depois de inserir o código, selecione **Avançar**.
1. Será exibido um aviso que diz “SMS verificado. O telefone foi registrado com êxito.”

> [!Important]
> Devido a um problema conhecido na versão prévia, por um curto período de tempo, adicionar um número de telefone não registrará o número para entrada através de SMS. Você precisará entrar com o número adicionado e, em seguida, seguir os prompts para registrar o número para entrada através de SMS.

### <a name="when-the-phone-number-is-in-use"></a>Quando o número de telefone está sendo usado

Se tentar usar um número de telefone que outra pessoa em sua organização esteja usando, você verá a seguinte mensagem:

![Mensagem de erro quando o número de telefone já está sendo usado](media/sms-sign-in-explainer/sms-sign-in-error.png)

Entre em contato com o administrador para corrigir o problema.

## <a name="when-you-have-an-existing-number"></a>Quando você tem um número existente

Se você já estiver usando um número de telefone com uma organização, e a opção de usar seu número de telefone como um nome de usuário passa a estar disponível, as etapas a seguir podem ajudá-lo a entrar na conta.

1. Quando a entrada através de SMS estiver disponível, será exibida uma barra perguntando se você deseja habilitar o número de telefone para entrada através de SMS:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Além disso, um botão **Habilitar** será exibido se você selecionar o cursor no bloco do método de telefone:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Para habilitar o método, selecione **Habilitar**. Será solicitado que você confirme a ação:

    ![Caixa de diálogo de confirmação para habilitar a entrada através de SMS para um número de telefone](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selecione **Habilitar**.

## <a name="when-you-remove-your-phone-number"></a>Quando você remove seu número de telefone

1. Para excluir o número de telefone, selecione o botão para excluir no bloco do método de telefone para entrada através de SMS.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Quando a solicitação para confirmar a ação for exibida, selecione **OK**.

Não é possível remover um número de telefone que esteja em uso como o método de entrada padrão. Para remover o número, você precisaria alterar o método de entrada padrão e, em seguida, remover o número de telefone novamente.
