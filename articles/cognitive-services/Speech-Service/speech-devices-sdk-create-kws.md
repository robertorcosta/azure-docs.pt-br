---
title: Criar palavras-chave personalizadas - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subseqüente para a nuvem, até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua marca.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400069"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Crie uma palavra-chave personalizada usando o Speech Studio

Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Por exemplo, "Hey Cortana" é uma palavra-chave para a assistente Cortana. Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subseqüente para a nuvem, até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua marca.

Neste artigo, você aprende como criar uma palavra-chave personalizada para o seu dispositivo.

## <a name="create-your-keyword"></a>Crie sua palavra-chave

Antes de usar uma palavra-chave personalizada, você precisará criar uma palavra-chave usando a página [Palavra-Chave Personalizada](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, ela produz um arquivo que você implanta no seu dispositivo.

1. Vá ao [Speech Studio](https://aka.ms/sdsdk-speechportal) e faça **login** ou, se você ainda não tiver uma assinatura de fala, escolha Criar [**uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página [Palavra-chave personalizada,](https://aka.ms/sdsdk-wakewordportal) crie um **novo projeto**. 

1. Digite um **nome**, uma **descrição**opcional, e selecione o idioma. Você precisará de um projeto por idioma e o suporte está atualmente limitado ao idioma en-US.

    ![Descreva seu projeto de palavras-chave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecione seu projeto na lista. 

    ![Selecione seu projeto de palavras-chave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Para iniciar um novo modelo de palavra-chave clique **no modelo Train**.

1. Digite um **nome** para o modelo de palavra-chave e **descrição** opcional e digite a **palavra-chave** de sua escolha e clique em **Next**. Temos algumas [diretrizes](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) para ajudar a escolher uma palavra-chave eficaz.

    ![Digite sua palavra-chave](media/custom-keyword/custom-kws-portal-new-model.png)

1. O portal agora criará pronúncias de candidatos para sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remova as verificações ao lado de quaisquer pronúncias que estejam incorretas. Uma vez que apenas boas pronúncias são verificadas, clique em **Treinar** para começar a gerar a palavra-chave. 

    ![Revise sua palavra-chave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Pode levar até trinta minutos para o modelo ser gerado. A lista de palavras-chave mudará de **Processamento** para **Bem Sucedido** quando o modelo estiver concluído. Em seguida, você pode baixar o arquivo.

    ![Revise sua palavra-chave](media/custom-keyword/custom-kws-portal-download-model.png)

1. Salve o arquivo .zip no computador. Você precisará deste arquivo para implantar sua palavra-chave personalizada no seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

Teste sua palavra-chave personalizada com [dispositivos de fala SDK Quickstart](https://aka.ms/sdsdk-quickstart).
