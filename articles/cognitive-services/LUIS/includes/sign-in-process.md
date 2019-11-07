---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 1effd07cef439a6257028549b7b7114b742eb478
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488747"
---
## <a name="sign-in-to-luis-portal"></a>Entrar no portal do LUIS

Um novo usuário do LUIS precisa seguir este procedimento:

1. Entrar no [portal do LUIS](https://www.luis.ai), selecionar seu país e concordar com os termos de uso.
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