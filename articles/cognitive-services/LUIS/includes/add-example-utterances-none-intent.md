---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648086"
---
O aplicativo cliente precisa saber se um enunciado não é significativo ou apropriado para o aplicativo. A intenção **None** é adicionada a cada aplicativo como parte do processo de criação para determinar se um enunciado não pode ser atendido pelo aplicativo cliente.

Se o LUIS retornar a intenção **None** para um enunciado, o aplicativo cliente poderá perguntar se o usuário deseja terminar a conversa ou fornecer mais orientações para continuar a conversa. 

Se você deixar a intenção **Nenhuma** vazia, um enunciado que deve ser previsto fora do domínio do assunto será previsto em uma das intenções existentes do domínio do assunto. O resultado é que o aplicativo cliente, assim como um chatbot, executará operações incorretas com base em uma previsão incorreta. 

1. Selecione **Intenções** no painel esquerdo.

1. Selecione a intenção **None**. Adicione três enunciados que o usuário pode inserir, mas que não são relevantes para o seu aplicativo:

    |Enunciados de exemplo de `None`|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
