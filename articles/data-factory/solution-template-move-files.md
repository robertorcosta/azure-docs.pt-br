---
title: Mover arquivos entre o armazenamento baseado em arquivos
description: Aprenda a usar um modelo de solução para mover arquivos entre o armazenamento baseado em arquivos usando o Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b3165daa06ed975df9ccb677699d3ceb449327ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74941876"
---
# <a name="move-files-with-azure-data-factory"></a>Mover arquivos com a Fábrica de Dados do Azure

Este artigo descreve um modelo de solução que você pode usar para mover arquivos de uma pasta para outra entre lojas baseadas em arquivos. Um dos cenários comuns de usar este modelo: os arquivos são continuamente descartados para uma pasta de aterrissagem da sua loja de origem. Ao criar um gatilho de cronograma, o pipeline ADF pode mover periodicamente esses arquivos da fonte para a loja de destino.  A maneira que o pipeline do ADF consegue "arquivos móveis" é recebendo os arquivos da pasta de aterrissagem, copiando cada um deles para outra pasta na loja de destino e, em seguida, excluindo os mesmos arquivos da pasta de aterrissagem na loja de origem.

> [!NOTE]
> Esteja ciente de que este modelo foi projetado para mover arquivos em vez de mover pastas.  Se você quiser mover a pasta alterando o conjunto de dados para fazê-la conter apenas um caminho de pasta e, em seguida, usando a atividade de cópia e excluir atividade para fazer referência ao mesmo conjunto de dados representando uma pasta, você precisa ter muito cuidado. Isso porque você tem que ter certeza de que NÃO haverá novos arquivos entrando na pasta entre as operações de cópia e exclusão. Se houver novos arquivos chegando à pasta no momento em que a atividade de cópia tiver acabado o trabalho de cópia, mas a atividade de exclusão ainda não tiver sido iniciada, é possível que a atividade Excluir exclua esse novo arquivo que esteja entrando e que ainda NÃO foi copiado para o destino ao excluir a pasta inteira.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo obtém os arquivos da sua loja baseada em arquivos de origem. Em seguida, ele move cada um deles para a loja de destino.

O modelo contém cinco atividades:
- **GetMetadata** recebe a lista de objetos, incluindo os arquivos e subpastas da sua pasta na loja de origem. Ele não vai recuperar os objetos recursivamente. 
- **Filtrar** a lista de objetos da atividade **GetMetadata** para selecionar apenas os arquivos. 
- **ForEach** obtém a lista de arquivos da atividade **Filtro** e, em seguida, itera sobre a lista e passa cada arquivo para a atividade Copiar e Excluir.
- **Copiar** copia um arquivo da fonte para a loja de destino.
- **Excluir** exclui o mesmo arquivo da loja de origem.

O modelo define quatro parâmetros:
- *SourceStore_Location* é o caminho da pasta da sua loja de origem de onde você deseja mover arquivos. 
- *SourceStore_Directory* é o caminho da subpasta da sua loja de origem de onde você deseja mover arquivos.
- *DestinationStore_Location* é o caminho da pasta da sua loja de destino para onde você deseja mover arquivos. 
- *DestinationStore_Directory* é o caminho de subpasta da sua loja de destino para onde você deseja mover arquivos.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o modelo **de arquivos Mover.** Selecione a conexão existente ou crie uma **nova** conexão com a sua loja de arquivos de origem da qual você deseja mover arquivos. Esteja ciente de que **DataSource_Folder** e **DataSource_File** são referência à mesma conexão da sua loja de arquivos de origem.

    ![Criar uma nova conexão para a origem](media/solution-template-move-files/move-files1.png)

2. Selecione a conexão existente ou crie uma **nova** conexão com a sua loja de arquivos de destino para onde você deseja mover arquivos.

    ![Criar uma nova conexão para o destino](media/solution-template-move-files/move-files2.png)

3. Selecione Usar esta guia **modelo.**
    
4. Você verá o pipeline, como no seguinte exemplo:

    ![Mostrar o pipeline](media/solution-template-move-files/move-files4.png)

5. Selecione **Depurar,** digite **os Parâmetros**e, em seguida, **selecione Concluir**.   Os parâmetros são o caminho da pasta para onde você deseja mover arquivos e o caminho da pasta para onde você deseja mover arquivos. 

    ![Executar o pipeline](media/solution-template-move-files/move-files5.png)

6. Revise o resultado.

    ![Revisar o resultado](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos novos e alterados por LastModifiedDate com a Fábrica de Dados do Azure](solution-template-copy-new-files-lastmodifieddate.md)

- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)