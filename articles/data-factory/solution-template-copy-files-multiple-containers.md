---
title: Copiar arquivos de vários contêineres
description: Aprenda a usar um modelo de solução para copiar arquivos de vários contêineres usando o Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 0c4c26ba163f83483b3eb48e51d91f9a919a887c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439834"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiar arquivos de vários contêineres com o Azure Data Factory

Este artigo descreve um modelo de solução que você pode usar para copiar arquivos de vários contêineres entre lojas de arquivos. Por exemplo, você pode usá-lo para migrar seu lago de dados do AWS S3 para o Azure Data Lake Store. Ou, você pode usar o modelo para replicar tudo, desde uma conta de armazenamento Azure Blob até outra.

> [!NOTE]
> Se você quiser copiar arquivos de um único contêiner, é mais eficiente usar a [Ferramenta de Dados de Cópia](copy-data-tool.md) para criar um pipeline com uma única atividade de cópia. O modelo deste artigo é mais do que você precisa para esse cenário simples.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo enumera os recipientes da sua loja de armazenamento de origem. Em seguida, copia esses contêineres para a loja de destino.

O modelo contém três atividades:
- **GetMetadata** verifica sua loja de armazenamento de origem e recebe a lista de contêineres.
- **ForEach** obtém a lista de contêineres da atividade **GetMetadata** e, em seguida, itera sobre a lista e passa cada contêiner para a atividade Copiar.
- **Copiar** cópias de cada recipiente da loja de armazenamento de origem para a loja de destino.

O modelo define os seguintes parâmetros:
- *SourceFileFolder* é o caminho da pasta do seu armazenamento de origem de dados, onde você pode obter uma lista dos contêineres. O caminho é o diretório raiz, que contém várias pastas de contêiner. O valor padrão desse parâmetro é `sourcefolder`.
- *SourceFileDirectory* é o caminho da subpasta o diretório raiz do seu armazenamento de origem de dados. O valor padrão desse parâmetro é `subfolder`.
- *DestinationFileFolder* é o caminho da pasta para onde os arquivos serão copiados em sua loja de destino. O valor padrão desse parâmetro é `destinationfolder`.
- *DestinationFileDirectory* é o caminho da subpasta para onde os arquivos serão copiados em sua loja de destino. O valor padrão desse parâmetro é `subfolder`.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para copiar **vários recipientes de arquivos entre o** modelo Lojas de arquivos. Crie uma **nova** conexão com sua loja de armazenamento de origem. O armazenamento de armazenamento de origem é onde você deseja copiar arquivos de vários contêineres.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Crie uma **nova** conexão com sua loja de armazenamento de destino.

    ![Criar uma nova conexão para o destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Você verá o pipeline, como no seguinte exemplo:

    ![Mostrar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecione **Depurar,** digite **os Parâmetros**e, em seguida, **selecione Concluir**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Revise o resultado.

    ![Revisar o resultado](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Próximas etapas

- [Cópia em massa de um banco de dados usando uma tabela de controle com a Fábrica de Dados Do Azure](solution-template-bulk-copy-with-control-table.md)

- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)
