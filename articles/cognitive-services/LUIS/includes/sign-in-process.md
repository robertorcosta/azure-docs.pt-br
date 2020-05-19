---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5e2a74f69f575f4b9b332b98d7e19089418b29ed
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585159"
---
## <a name="sign-in-to-luis-portal"></a>Entrar no portal do LUIS

Um novo usuário do LUIS precisa seguir este procedimento:

1. Entrar no [portal do LUIS](https://www.luis.ai), selecionar seu país e concordar com os termos de uso. Caso, em vez disso, você consulte **Meus aplicativos**, um recurso do LUIS já existirá e você deverá avançar e criar um aplicativo.

1. Selecionar **Criar recurso do Azure** e, em seguida, selecionar **Criar um recurso de criação para o qual migrar seus aplicativos.**

    ![Escolher um tipo de recurso de criação de Reconhecimento Vocal](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Preencher os detalhes do recurso.

    ![Criar recurso de criação](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Ao **criar um novo recurso de criação**, forneça as seguintes informações:

    * **Nome do recurso** – um nome personalizado que você escolher, usado como parte da URL para as consultas de ponto de extremidade de criação e previsão.
    * **Locatário** – o locatário ao qual sua assinatura do Azure está associada.
    * **Nome da assinatura** – a assinatura que será cobrada pelo recurso.
    * **Grupo de recursos** – o nome de um grupo de recursos personalizado que você escolher ou criar. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento.
    * **Local** – a escolha de local é baseada na seleção do **grupo de recursos**.
    * **Tipo de preço** – o tipo de preço determina a transação máxima por segundo e mês.

1. Um resumo do recurso a ser criado é exibido. Selecione **Avançar**.

    ![Criar recurso de criação](../media/sign-in/sign-in-confirm-key-selection.png)

1. Confirme selecionando **Continuar**.

    ![Criar recurso de criação](../media/sign-in/sign-in-confirm-continue.png)