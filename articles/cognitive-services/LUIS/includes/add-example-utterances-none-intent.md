---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c81aaa02af0895e6c3a851fe15273d85de9b55bc
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262734"
---
O aplicativo cliente precisa saber se um enunciado não é significativo ou apropriado para o aplicativo. A intenção **None** é adicionada a cada aplicativo como parte do processo de criação para determinar se um enunciado não deve ser atendido pelo aplicativo cliente.

Se o LUIS retornar a intenção **None** para um enunciado, o aplicativo cliente poderá perguntar se o usuário deseja terminar a conversa ou fornecer mais orientações para continuar a conversa. 

Se você deixar a intenção **Nenhuma** vazia, um enunciado que deve ser previsto fora do domínio do assunto será previsto em uma das intenções existentes do domínio do assunto. O resultado é que o aplicativo cliente, assim como um chatbot, executará operações incorretas com base em uma previsão incorreta. 

1. Selecione **Intenções** no painel esquerdo.

1. Selecione a intenção **None**. Adicione três enunciados que o usuário pode inserir, mas que não são relevantes para o seu aplicativo para pedidos de pizza:

    |Enunciados de exemplo de `None`|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Esses exemplos não devem usar palavras que você espera no domínio do seu assunto, como `pizza`, `cheese`, `crust`, `pickup` `deliver`.