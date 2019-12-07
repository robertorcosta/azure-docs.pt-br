---
title: Criar uma palavra-chave personalizada-serviço de fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 42bcc336bfeb325a08c3d65438d66690c0b35100
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896482"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Criar uma palavra-chave personalizada usando o serviço de fala

Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Por exemplo, "Ei Cortana" é uma palavra-chave para o assistente da Cortana. Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.

Neste artigo, você aprenderá a criar uma palavra-chave personalizada para seu dispositivo.

## <a name="choose-an-effective-keyword"></a>Escolher uma palavra-chave efetiva

Considere as seguintes diretrizes ao escolher uma palavra-chave:

* Sua palavra-chave deve ser uma palavra em inglês ou uma frase. Não deve levar mais dois segundos para ser dita.

* Palavras com quatro a sete sílabas funcionam melhor. Por exemplo, "Ei, computador" é uma boa palavra-chave. Apenas "Hey" ("Ei") seria uma ruim.

* As palavras-chave devem seguir as regras de pronúncia em inglês comuns.

* Uma palavra única ou até mesmo inventada que siga as regras comuns de pronúncia do português pode reduzir falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.

* Não escolha uma palavra comum. Por exemplo, "comer" e "ir" são palavras que as pessoas costumam usar em conversas. Elas podem ser gatilhos falsos para o seu dispositivo.

* Evite usar uma palavra-chave que possa ter pronúncias alternativas. Os usuários precisariam saber a pronúncia "certa" para fazer o dispositivo responder. Por exemplo, “509” poderia ser pronunciado como "five zero nine" ("cinco zero nove") ou "five hundred and nine" ("quinhentos e nove"). "R.E.I." pode ser pronunciado como “r-e-i” ou “ray”. "Live" pode ser pronunciado "/līv/" ou "/liv/".

* Não use caracteres especiais, símbolos nem dígitos. Por exemplo, "Go #" e "20 + gatos" não seriam boas palavras-chave. No entanto, "go sharp" ("fique esperto") ou "twenty plus cats" ("mais de vinte gatos") pode funcionar. Você ainda pode usar os símbolos na sua identidade visual, bem como marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se você escolher uma palavra com marca registrada como sua palavra-chave, certifique-se de que você possui essa marca registrada ou que você tem permissão do proprietário da marca comercial para usar a palavra. A Microsoft não é responsável por problemas legais que possam surgir da sua escolha de palavra-chave.

## <a name="create-your-keyword"></a>Criar sua palavra-chave

Para poder usar uma palavra-chave personalizada, você precisará criar uma palavra-chave usando a página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, ela produz um arquivo que você implanta em seu dispositivo.

1. Vá para o [Speech Studio](https://aka.ms/sdsdk-speechportal) e **entre** ou, se você ainda não tiver uma assinatura de fala, escolha [**criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) , crie um **novo projeto**. 

1. Insira um **nome**, uma **Descrição**opcional, e selecione o idioma. Você precisará de um projeto por idioma e o suporte no momento é limitado ao idioma en-US.

    ![Descrever seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecione o projeto na lista. 

    ![Selecione seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Para iniciar um novo modelo de palavra-chave, clique em **treinar modelo**.

1. Insira um **nome** para o modelo de palavra-chave e a **Descrição** opcional e digite na **palavra-chave** de sua escolha e clique em **Avançar**. Temos algumas [diretrizes](#choose-an-effective-keyword) para ajudar a escolher uma palavra-chave efetiva.

    ![Insira sua palavra-chave](media/custom-keyword/custom-kws-portal-new-model.png) 

1. Agora, o portal criará pronúncias candidatas para sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remove as verificações ao lado de quaisquer pronúncias incorretas. Quando apenas as boas pronúncias estiverem marcadas, clique em **treinar** para começar a gerar a palavra-chave. 

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-choose-prons.png) 

1. Pode levar até dez minutos para que o modelo seja gerado. A lista de palavras-chave mudará de **processamento** para com **êxito** quando o modelo for concluído. Em seguida, você pode baixar o arquivo.

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-download-model.png) 

1. Salve o arquivo .zip no computador. Você precisará desse arquivo para implantar sua palavra-chave personalizada em seu dispositivo.

## <a name="next-steps"></a>Próximos passos

Teste sua palavra-chave personalizada com o guia de [início rápido SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart).
