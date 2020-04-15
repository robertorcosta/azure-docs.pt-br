---
title: Experiência do usuário de login do SMS para número de telefone (visualização) - Azure AD
description: Saiba mais sobre a experiência do usuário de login de SMS para números de telefone novos ou existentes
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378828"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Use seu número de telefone como nome de usuário (visualização)

O registro de um dispositivo dá ao seu telefone acesso aos serviços da sua organização e não permite que sua organização tenha acesso ao seu telefone. Se você é um administrador, você pode encontrar mais informações no [Configurar e habilitar os usuários para autenticação baseada em SMS](../authentication/howto-authentication-sms-signin.md).

Se sua organização não disponibilizou o login de SMS, você não verá uma opção para ele ao registrar um telefone com sua conta.  

## <a name="when-you-have-a-new-phone-number"></a>Quando você tem um novo número de telefone

Se você conseguir um novo telefone ou novo número e registrá-lo em uma organização para a qual o login de SMS está disponível, você experimenta o processo normal de registro telefônico:

1. Selecione **o método Adicionar**.
1. Selecione **Telefone**.
1. Digite o número de telefone e selecione **Texto-me um código**.
1. Depois de inserir o código, selecione **Next**.
1. Você verá um aviso que diz "SMS verificado. Seu telefone foi registrado com sucesso."

> [!Important]
> Devido a um problema conhecido na pré-visualização, por um curto período de tempo, a adição de número de telefone não registrará o número de login de SMS. Você terá que fazer login com o número adicionado e, em seguida, seguir as instruções para registrar o número de login de SMS.

### <a name="when-the-phone-number-is-in-use"></a>Quando o número de telefone está em uso

Se você tentar usar um número de telefone que outra pessoa na sua organização está usando, você verá a seguinte mensagem:

![Mensagem de erro quando seu número de telefone já está usado](media/sms-sign-in-explainer/sms-sign-in-error.png)

Entre em contato com seu pai para remediar o problema.

## <a name="when-you-have-an-existing-number"></a>Quando você tem um número existente

Se você já estiver usando um número de telefone com uma organização e usando seu número de telefone como nome de usuário, as seguintes etapas podem ajudá-lo a fazer login.

1. Quando o login do SMS estiver disponível, um banner é exibido perguntando se você gostaria de ativar o número de telefone para login de SMS:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Além disso, um botão **Habilitar** será exibido se você selecionar o cuidado no azulejo do método do telefone:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Para habilitar o método, **selecione Ativar**. Você é solicitado a confirmar a ação:

    ![Caixa de diálogo de confirmação para habilitar o login de SMS para um número de telefone](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selecione **Habilitar**.

## <a name="when-you-remove-your-phone-number"></a>Quando você remove seu número de telefone

1. Para excluir o número de telefone, selecione o botão excluir no tipo de telefone de login sms.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Quando solicitado para confirmar a ação, selecione **OK**.

Você não pode remover um número de telefone que está em uso como o método de login padrão. Para remover o número, você teria que alterar o método de login padrão e, em seguida, remover o número de telefone novamente.
