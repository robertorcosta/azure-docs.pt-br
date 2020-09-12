---
title: Entrar no portal do LUIS
description: Se você for um novo usuário entrando no portal do LUIS, a experiência de entrada será ligeiramente diferente com base na sua conta de usuário atual.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: 58212fc35df7f230682d4f80b5eadf6cd8ca0902
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007974"
---
# <a name="sign-in-to-luis-portal"></a>Entrar no portal do LUIS

Se você for um novo usuário entrando no portal do LUIS, a experiência de entrada será ligeiramente diferente com base na sua conta de usuário atual:
  * Associado a uma assinatura do Azure
  * Não associado a uma assinatura do Azure

## <a name="determine-account-type"></a>Determinar o tipo de conta

Quando você entrar pela primeira vez no portal do LUIS, use os seguintes indicadores visuais para determinar o tipo de conta.

### <a name="account-without-azure-subscription"></a>Conta sem assinatura do Azure

Uma conta, que não está associada a uma assinatura do Azure, tem o ícone do Azure na barra de navegação superior direita. Depois de migrar para o tipo de conta associada, o ícone não será mais exibido.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Captura de tela parcial da barra de navegação do LUIS com o ícone do Azure.":::

### <a name="account-with-azure-subscription"></a>Conta com a assinatura do Azure

Uma conta associada a uma assinatura do Azure permite que você selecione sua assinatura e o recurso a serem usados.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Captura de tela parcial do portal do LUIS com caixas de seleção suspensas de seleção de recursos.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Entrar com uma conta associada a uma assinatura do Azure

1. Entre no [portal do Luis](https://www.luis.ai) e concorde com os termos de uso.

1. Você terá duas opções de inscrição:

    * Continue usando um recurso do Azure, que é o caminho recomendado e em breve será o único caminho disponível. Esse caminho permite vincular sua conta do LUIS a um recurso de criação do Azure durante a inscrição, escolhendo um recurso existente em sua assinatura ou criando um novo recurso. Isso é equivalente a se inscrever migrado sem a necessidade de passar o [processo de migração](luis-migration-authoring.md#what-is-migration) posteriormente. Todos os usuários serão solicitados a migrar até 2 de novembro de 2020.

    * Continue usando o início ou a chave de avaliação. Esse caminho permite que você entre no LUIS com o iniciador ou o recurso de avaliação que está sendo fornecido sem a necessidade de criar recursos. Se você escolher esse caminho, eventualmente será necessário [migrar sua conta](luis-migration-authoring.md#migration-steps) e vincular seus aplicativos a um recurso de criação. É por isso que escolher o caminho em que você continua com o recurso do Azure é recomendado.

    [Saiba mais sobre criação e chaves de início](luis-how-to-azure-subscription.md#luis-resources). Ambos os recursos fornecem 1 milhão transações de criação gratuitas e 1000 transações de ponto de extremidade de previsão gratuita.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Captura de tela parcial para escolher um tipo de Reconhecimento vocal recurso de criação.":::

1. Usar um recurso de criação existente

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Escolher recurso de criação":::

    Se você já tiver recursos de criação de LUIS em sua assinatura e associar um à sua conta do LUIS durante a entrada, escolha a opção **usar recurso de criação existente** e forneça as seguintes informações:

    * **Locatário** – o locatário ao qual sua assinatura do Azure está associada. Não será possível alternar os locatários da janela existente. Você pode alternar locatários selecionando o avatar mais à direita, que contém as iniciais na barra superior.
    * **Nome da assinatura** -a assinatura que será associada ao recurso. Se você tiver mais de uma assinatura que pertence ao seu locatário, selecione aquela que você deseja na lista suspensa.
    * **Nome do recurso** -o recurso de criação ao qual você deseja que a conta seja associada.

    > [!Note]
    > Se o recurso de criação que você está procurando estiver esmaecido na lista suspensa, isso significa que você entrou em um portal regional diferente. [Entenda o conceito de portais regionais](luis-reference-regions.md#luis-authoring-regions).

1. Criar um novo recurso de criação

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Criar recurso de criação":::

    Ao **criar um novo recurso de criação**, forneça as seguintes informações:

    * **Locatário** – o locatário ao qual sua assinatura do Azure está associada. Não será possível alternar os locatários da janela existente. Você pode alternar locatários selecionando o avatar mais à direita, que contém as iniciais na barra superior.
    * **Nome do recurso** -um nome personalizado que você escolhe, usado como parte da URL para suas transações de criação. O nome do recurso só pode incluir caracteres alfanuméricos, '-', e não pode iniciar ou terminar com '-'. Se quaisquer outros símbolos forem incluídos no nome, a criação de um recurso falhará.
    * **Nome da assinatura** -a assinatura que será associada ao recurso. Se você tiver mais de uma assinatura que pertence ao seu locatário, selecione aquela que você deseja na lista suspensa.
    * **Grupo de recursos** -um nome de grupo de recursos personalizado que você escolhe em sua assinatura. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento. Se você não tiver um grupo de recursos em sua assinatura, não será permitido criar um no portal do LUIS. Acesse [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) para criar um, em seguida, vá para Luis para continuar o processo de entrada.

1. Depois de escolher seu caminho, pode levar alguns segundos até que um sinal que diga "sua conta foi migrada com êxito seja exibido. Conclua selecionando **continuar**.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Confirmar recurso de criação":::

    > [!Note]
    > Se você tiver uma assinatura e pelo menos um recurso de criação na região igual à que está se inscrevendo no portal, você poderá entrar automaticamente no LUIS migrado e associado a um recurso sem a necessidade de escolher em qual caminho ir.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Entrar com uma conta de usuário não associada a uma assinatura do Azure

1. Entre no [portal do Luis](https://www.luis.ai) e verifique se você concorda com os termos de uso.

1. Conclua selecionando **continuar**. Você fará logon automaticamente com uma chave de avaliação/início. Isso significa que, eventualmente, você será solicitado a [migrar sua conta](luis-migration-authoring.md#migration-steps) e vincular seus aplicativos a um recurso de criação. Para passar no processo de migração, você precisará entrar para uma [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Nenhum cenário de assinatura":::

## <a name="troubleshooting"></a>Solução de problemas

* Se você criar um recurso de criação do portal do Azure em uma região diferente do portal no qual está entrando, o recurso de criação ficará esmaecido.
* Ao criar um novo recurso, verifique se o nome do recurso inclui apenas caracteres alfanuméricos, '-', e não pode iniciar ou terminar com '-'. Caso contrário, ocorrerá uma falha.
* Verifique se você tem as [permissões corretas em sua assinatura para criar um recurso do Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Se você não tiver as permissões apropriadas, entre em contato com o administrador da sua assinatura para fornecer permissões suficientes.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Iniciar um novo aplicativo](luis-how-to-start-new-app.md)
