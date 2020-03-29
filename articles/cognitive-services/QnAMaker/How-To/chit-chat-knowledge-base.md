---
title: Adicionar um bate-papo a uma base de dados de conhecimento do QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicionar um bate-papo pessoal ao seu bot o torna mais comunicativo e interessante ao criar uma KB. O QnA Maker permite adicionar com facilidade um conjunto previamente populado dos principais bate-papos à sua KB.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220705"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicionar bate-papo à base de dados de conhecimento

Adicionar bate-papo ao seu bot o torna mais comunicativo e interessante. O recurso de bate-papo do QnA Maker permite adicionar com facilidade um conjunto previamente populado dos principais bate-papos à sua KB (base de dados de conhecimento). Esse pode ser um ponto de partida para a personalidade do seu bot, o que economizará o tempo e o custo de escrevê-la do zero.  

Este conjunto de dados tem cerca de 100 cenários de bate-papo na voz de várias personas, como Profissional,Amigável e Espirituoso. Escolha a persona que mais se assemelha à voz do seu bot. Dada uma consulta de usuário, o QnA Maker tenta correspondê-la às perguntas e respostas do bate-papo conhecido mais próximo.  

Alguns exemplos das diferentes personalidades estão abaixo. Você pode ver todos os [conjuntos de dados](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) de personalidade, juntamente com detalhes das personalidades.

Para a consulta `When is your birthday?`do usuário de , cada personalidade tem uma resposta estilizada:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personalidade|Exemplo|
|--|--|
|Professional|A idade não se aplica a mim.|
|Amigável|Eu realmente não tenho uma idade.|
|Espirituoso|Estou livre da idade.|
|Cuidar|Eu não tenho uma idade.|
|Entusiasmado|Sou um robô, então não tenho idade.|
||


## <a name="language-support"></a>Suporte ao idioma

Os conjuntos de dados do chit-chat são suportados nos seguintes idiomas:

|Idioma|
|--|
|Chinês|
|Inglês|
|Francês|
|Alemanha|
|Italiano|
|Japonês|
|Coreano|
|Português|
|Espanhol|


## <a name="add-chit-chat-during-kb-creation"></a>Adicionar bate-papo durante a criação da KB
Durante a criação da base de dados de conhecimento, depois de adicionar suas URLs e arquivos de origem, há uma opção para adicionar um bate-papo. Escolha a personalidade que você deseja como sua base de bate-papo. Se você não quiser adicionar um bate-papo ou se já tiver um bate-papo compatível nas suas fontes de dados, escolha **Nenhum**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Adicionar bate-papo a uma KB existente
Selecione sua KB e, em seguida, navegue para a página **Configurações**. Há um link para todos os conjuntos de dados de bate-papo no formato **.tsv** apropriado. Baixe a personalidade que você desejar e, em seguida, carregue-a como uma fonte de arquivo. Não edite o formato ou os metadados ao baixar e carregar o arquivo. 
  
![Adicionar bate-papo a uma KB existente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Editar as perguntas e respostas do seu bate-papo
Ao editar sua KB, você verá uma nova fonte para bate-papo, com base na personalidade que você selecionou. Agora é possível adicionar perguntas alteradas ou editar as respostas, assim como com qualquer outra fonte. 

![Editar as perguntas e respostas do bate-papo](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Para exibir os metadados, **selecione 'Exibir opções'** na barra de ferramentas e, em seguida, **selecione Mostrar metadados**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicionar perguntas e respostas adicionais ao bate-papo
É possível adicionar novas perguntas e respostas ao bate-papo que não estão presentes no conjunto predefinido. Verifique se você não está duplicando um par de pergunta e resposta já abordado no conjunto de bate-papo. Ao adicionar novas perguntas e respostas ao bate-papo, elas são adicionadas à fonte **Editorial**. Para garantir que o ranker entenda que isso é bate-papo, adicione o par de tecla/valor de metadados "Editorial: chitchat", como visto na imagem a seguir:
   
![! [Adicionar qnAs de bate-papo] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Excluir o bate-papo de uma KB existente
Selecione sua KB e, em seguida, navegue para a página **Configurações**. Sua fonte de bate-papo é listada como um arquivo, com o nome de personalidade selecionada. Você pode excluí-lo como um arquivo de origem.

![Excluir o bate-papo da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Confira também 

[Visão geral do QnA Maker](../Overview/overview.md)
