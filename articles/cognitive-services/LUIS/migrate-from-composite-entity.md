---
title: Atualizar entidade composta-LUIS
description: Atualize a entidade composta para a entidade aprendida com o computador com o processo de atualização no portal do LUIS.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 9bbb03a2009bdcb6e2fa5d20aefadd4c7c99f025
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599420"
---
# <a name="upgrade-composite-entity-to-machine-learned-entity"></a>Atualizar entidade composta para entidade aprendida pelo computador

Atualize a entidade composta para a entidade aprendida com o computador para criar uma entidade que receba previsões mais completas com melhor desdação para depurar a entidade.

## <a name="current-version-model-restrictions"></a>Restrições do modelo de versão atual

O processo de atualização cria entidades aprendidas por máquina, com base nas entidades compostas existentes encontradas em seu aplicativo, em uma nova versão do seu aplicativo. Isso inclui as funções e filhos da entidade composta. O processo também alterna os rótulos no exemplo declarações para usar a nova entidade.

## <a name="upgrade-process"></a>Processo de atualização

O processo de atualização:
* Cria uma nova entidade aprendida por máquina para cada entidade composta.
* Entidades filho:
    * Se a entidade filho for usada somente na entidade composta, ela só será adicionada à entidade aprendida com o computador.
    * Se a entidade filho for usada em composição _e_ como uma entidade separada (rotulada no exemplo declarações), ela será adicionada à versão como uma entidade e como uma subentidade à nova entidade aprendida por máquina.
    * Se a entidade filho usar uma função, cada função será convertida em uma subentidade de mesmo nome.
    * Se a entidade filho for uma entidade não aprendida por computador (expressão regular, entidade de lista ou entidade predefinida), uma nova subentidade será criada com o mesmo nome e a nova subentidade terá um recurso usando a entidade não aprendida por máquina com o recurso necessário adicionado.
* Os nomes são retidos, mas devem ser exclusivos no mesmo nível de subentidade/irmão. Consulte [os limites de nomenclatura exclusivos](luis-boundaries.md#name-uniqueness).
* Os rótulos no exemplo declarações são alternados para a nova entidade aprendida por computador com subentidades.

Use o gráfico a seguir para entender como seu modelo é alterado:

|Objeto antigo|Novo objeto|Observações|
|--|--|--|
|Entidade composta|Entidade aprendida por máquina com estrutura|Ambos os objetos são objetos pai.|
|A entidade filho do composto é uma **entidade simples**|subentidade|Ambos os objetos são objetos filho.|
|A entidade filho do composto é uma **entidade predefinida** , como número|a subentidade com o nome da entidade predefinida, como número, e a subentidade tem _recurso_ de entidade de número predefinida com a opção de restrição definida como _true_.|a subentidade contém recurso com restrição no nível da subentidade.|
|A entidade filho do composto é uma **entidade predefinida** , como número, e a entidade predefinida tem uma **função**|a subentidade com o nome da função e a subentidade tem o recurso de entidade de número predefinido com a opção de restrição definida como true.|a subentidade contém recurso com restrição no nível da subentidade.|
|Função|subentidade|O nome da função se torna o nome da subentidade. A subentidade é um descendente direto da entidade aprendida pela máquina.|

## <a name="begin-upgrade-process"></a>Iniciar processo de atualização

Antes de atualizar, certifique-se de:

* Altere as versões se você não estiver na versão correta para atualizar


1. Inicie o processo de atualização da notificação ou você pode aguardar até o próximo prompt agendado.

    > [!div class="mx-imgBorder"]
    > ![Iniciar atualização de notificações](./media/update-composite-entity/notification-begin-update.png)

1. No pop-up, selecione **Atualizar agora**.

1. Examine o **que acontece quando você atualiza** as informações e selecione **continuar**.

1. Selecione as entidades compostas da lista para atualizar e selecione **continuar**.

1. Você pode mover quaisquer alterações não treinadas da versão atual para a versão atualizada marcando a caixa de seleção.

1. Selecione **continuar** para iniciar o processo de atualização.

1. A barra de progresso indica o status do processo de atualização.

1. Quando o processo for concluído, você estará em uma nova versão treinada com as novas entidades aprendidas por máquina. Selecione **experimentar as novas entidades** para ver a nova entidade.

    Se a atualização ou o treinamento falhar para a nova entidade, uma notificação fornecerá mais informações.

1. Na página lista de entidades, as novas entidades são marcadas com **novo** ao lado do nome do tipo.

## <a name="next-steps"></a>Próximas etapas

* [Autores e colaboradores](luis-how-to-collaborate.md)