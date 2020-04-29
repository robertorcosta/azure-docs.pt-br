---
title: Copiar arquivos de vários contêineres
description: Saiba como usar um modelo de solução para copiar arquivos de vários contêineres usando Azure Data Factory.
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
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414813"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiar arquivos de vários contêineres com o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve um modelo de solução que você pode usar para copiar arquivos de vários contêineres entre repositórios de arquivos. Por exemplo, você pode usá-lo para migrar seu data Lake do AWS S3 para Azure Data Lake Store. Ou, você pode usar o modelo para replicar tudo de uma conta de armazenamento de BLOBs do Azure para outra.

> [!NOTE]
> Se você quiser copiar arquivos de um único contêiner, é mais eficiente usar a [ferramenta copiar dados](copy-data-tool.md) para criar um pipeline com uma única atividade de cópia. O modelo neste artigo é mais do que você precisa para esse cenário simples.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Esse modelo enumera os contêineres do seu armazenamento de origem. Em seguida, ele copia esses contêineres para o repositório de destino.

O modelo contém três atividades:
- O **GetMetadata** verifica seu armazenamento de origem e obtém a lista de contêineres.
- **Foreach** Obtém a lista de contêineres da atividade **GetMetadata** e, em seguida, itera na lista e passa cada contêiner para a atividade de cópia.
- **Copiar** copia cada contêiner do repositório de armazenamento de origem para o repositório de destino.

O modelo define os seguintes parâmetros:
- *SourceFileFolder* é o caminho da pasta do armazenamento de fonte de dados, onde você pode obter uma lista dos contêineres. O caminho é o diretório raiz, que contém várias pastas de contêiner. O valor padrão desse parâmetro é `sourcefolder`.
- *SourceFileDirectory* é o caminho da subpasta no diretório raiz do seu armazenamento de fonte de dados. O valor padrão desse parâmetro é `subfolder`.
- *DestinationFileFolder* é o caminho da pasta para onde os arquivos serão copiados no armazenamento de destino. O valor padrão desse parâmetro é `destinationfolder`.
- *DestinationFileDirectory* é o caminho da subpasta onde os arquivos serão copiados no armazenamento de destino. O valor padrão desse parâmetro é `subfolder`.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o modelo **copiar vários arquivos entre os repositórios** de arquivos. Crie uma **nova** conexão com o repositório de armazenamento de origem. O repositório de armazenamento de origem é onde você deseja copiar arquivos de vários contêineres.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Crie uma **nova** conexão com o repositório de armazenamento de destino.

    ![Criar uma nova conexão para o destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecione **usar este modelo**.

    ![Usar este modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Você verá o pipeline, como no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecione **depurar**, insira os **parâmetros**e, em seguida, selecione **concluir**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Revise o resultado.

    ![Revisar o resultado](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Próximas etapas

- [Cópia em massa de um banco de dados usando uma tabela de controle com Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)
