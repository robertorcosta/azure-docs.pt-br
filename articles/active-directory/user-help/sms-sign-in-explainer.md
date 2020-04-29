---
title: Experiência do usuário de entrada do SMS para número de telefone (versão prévia) – Azure AD
description: Saiba mais sobre a experiência do usuário de entrada do SMS para números de telefone novos ou existentes
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378828"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Usar seu número de telefone como um nome de usuário (versão prévia)

O registro de um dispositivo dá ao seu telefone acesso aos serviços da sua organização e não permite que sua organização acesse seu telefone. Se você for um administrador, poderá encontrar mais informações em [configurar e habilitar usuários para autenticação baseada em SMS](../authentication/howto-authentication-sms-signin.md).

Se sua organização não tiver disponibilizado a entrada do SMS, você não verá uma opção para ela ao registrar um telefone com sua conta.  

## <a name="when-you-have-a-new-phone-number"></a>Quando você tiver um novo número de telefone

Se você receber um novo telefone ou novo número e registrá-lo em uma organização para a qual o logon do SMS está disponível, você terá o processo normal de registro do telefone:

1. Selecione **Adicionar método**.
1. Selecione **telefone**.
1. Insira o número de telefone e selecione **enviar um texto para mim em um código**.
1. Depois de inserir o código, selecione **Avançar**.
1. Você verá um prompt que diz "SMS verificado. Seu telefone foi registrado com êxito. "

> [!Important]
> Devido a um problema conhecido na visualização, por um curto período, adicionar um número de telefone não registrará o número para entrar no SMS. Você precisará entrar com o número adicionado e, em seguida, seguir os prompts para registrar o número para entrar no SMS.

### <a name="when-the-phone-number-is-in-use"></a>Quando o número de telefone estiver em uso

Se você tentar usar um número de telefone que outra pessoa em sua organização esteja usando, você verá a seguinte mensagem:

![Mensagem de erro quando seu número de telefone já está em uso](media/sms-sign-in-explainer/sms-sign-in-error.png)

Entre em contato com seu administrador para corrigir o problema.

## <a name="when-you-have-an-existing-number"></a>Quando você tem um número existente

Se você já estiver usando um número de telefone com uma organização e usar seu número de telefone como um nome de usuário se tornar disponível, as etapas a seguir podem ajudá-lo a entrar.

1. Quando a entrada do SMS estiver disponível, será exibida uma faixa perguntando se você deseja habilitar o número de telefone para entrada do SMS:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Além disso, um botão **habilitar** será exibido se você selecionar o cursor no bloco do método de telefone:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Para habilitar o método, selecione **habilitar**. Você será solicitado a confirmar a ação:

    ![Caixa de diálogo de confirmação para habilitar a entrada do SMS para um número de telefone](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selecione **Habilitar**.

## <a name="when-you-remove-your-phone-number"></a>Quando você remover seu número de telefone

1. Para excluir o número de telefone, selecione o botão excluir no bloco do método de telefone de entrada do SMS.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Quando for solicitado a confirmar a ação, selecione **OK**.

Não é possível remover um número de telefone que esteja em uso como o método de entrada padrão. Para remover o número, você precisaria alterar o método de entrada padrão e, em seguida, remover o número de telefone novamente.
