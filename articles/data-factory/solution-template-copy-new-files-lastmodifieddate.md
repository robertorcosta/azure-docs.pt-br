---
title: Copiar arquivos novos e alterados por LastModifiedDate
description: Aprenda a usar um modelo de solução para copiar arquivos novos e alterados por LastModifiedDate com o Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 979977b2dd2eb5742d4e488623c79cb91427f055
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414807"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copiar arquivos novos e alterados por LastModifiedDate com a Fábrica de Dados do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve um modelo de solução que você pode usar para copiar arquivos novos e alterados apenas por LastModifiedDate de uma loja baseada em arquivos para uma loja de destino. 

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo primeiro seleciona os arquivos novos e alterados apenas por seus atributos **LastModifiedDate**e, em seguida, copia esses arquivos selecionados do armazenamento de origem de dados para o armazenamento de destino de dados.

O modelo contém uma atividade:
- **Copiar** para copiar arquivos novos e alterados apenas por LastModifiedDate de uma loja de arquivos para uma loja de destinos.

O modelo define seis parâmetros:
-  *FolderPath_Source* é o caminho da pasta onde você pode ler os arquivos da loja de origem. Você precisa substituir o valor padrão pelo seu próprio caminho de pasta.
-  *Directory_Source* é o caminho da subpasta onde você pode ler os arquivos da loja de origem. Você precisa substituir o valor padrão pelo seu próprio caminho de subpasta.
-  *FolderPath_Destination* é o caminho da pasta onde você deseja copiar arquivos para a loja de destinos. Você precisa substituir o valor padrão pelo seu próprio caminho de pasta.
-  *Directory_Destination* é o caminho da subpasta onde você deseja copiar arquivos para a loja de destinos. Você precisa substituir o valor padrão pelo seu próprio caminho de subpasta.
-  *LastModified_From* é usado para selecionar os arquivos cujo atributo LastModifiedDate é posterior ou igual a este valor de data- data.  Para selecionar apenas os novos arquivos, que não foram copiados da última vez, este valor de data pode ser a hora em que o pipeline foi acionado da última vez. Você pode substituir o valor padrão '2019-02-01T00:00:00Z' ao seu esperado LastModifiedDate no fuso horário UTC. 
-  *LastModified_To* é usado para selecionar os arquivos cujo atributo LastModifiedDate é anterior a este valor de data. Para selecionar apenas os novos arquivos, que não foram copiados da última vez, este valor de data pode ser o momento presente.  Você pode substituir o valor padrão '2019-02-01T00:00:00Z' ao seu esperado LastModifiedDate no fuso horário UTC. 

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para modelo **Copiar novos arquivos apenas por LastModifiedDate**. Crie uma **nova** conexão com sua loja de armazenamento de origem. O armazenamento de armazenamento de origem é de onde você deseja copiar arquivos.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Crie uma **nova** conexão com sua loja de destino. A loja de destino é para onde você deseja copiar arquivos. 

    ![Criar uma nova conexão para o destino](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Você verá o pipeline disponível no painel, conforme mostrado no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Selecione **Depurar,** escrever o valor para **os Parâmetros** e selecionar **Concluir**.  Na imagem abaixo, definimos os parâmetros a seguir.
   - **FolderPath_Source** = pasta de origem
   - **Directory_Source** = subpasta
   - **FolderPath_Destination** = pasta de destino
   - **Directory_Destination** = subpasta
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    O exemplo é indicar que os arquivos, que foram modificados pela última vez dentro do tempo (**2019-02-01T00:00:00Z** para **2019-01T00:00:00Z**) serão copiados da **pasta/subpasta** de origem do caminho de origem para a **pasta de destino/subpasta**de destino de destino .  Você pode substituí-los por seus próprios parâmetros.

    ![Executar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Revise o resultado. Você verá apenas que os arquivos modificados pela última vez dentro do tempo configurado foram copiados para a loja de destino.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Agora você pode adicionar um gatilho de janelas caindo para automatizar este pipeline, para que o pipeline possa sempre copiar arquivos novos e alterados apenas por LastModifiedDate periodicamente.  Selecione **Adicionar gatilho**e selecione **Novo/Editar**.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Na janela **Adicionar Gatilhos**, selecione **+ Novo**.

9. Selecione **Janela de queda** para o tipo de gatilho, defina **A cada 15 minutos** como a recorrência (você pode mudar para qualquer tempo de intervalo). Selecione **Sim** para caixa ativada e selecione **OK**.

    ![Criar gatilho](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Defina o valor para os Parâmetros de **execução** de gatilho a seguir e selecione **Concluir**.
    - **FolderPath_Source** = **pasta de origem**.  Você pode substituir por sua pasta no armazenamento de dados de origem.
    - **Directory_Source** = **subpasta**.  Você pode substituir por sua subpasta no armazenamento de dados de origem.
    - **FolderPath_Destination** = **pasta de destinos**.  Você pode substituir por sua pasta no armazenamento de dados de destino.
    - **Directory_Destination** = **subpasta**.  Você pode substituir por sua subpasta no armazenamento de dados de destino.
    - **LastModified_From** =  **LastModified_From\@gatilho().saídas.windowStartTime**.  É uma variável do sistema do gatilho que determina a hora em que o oleoduto foi acionado da última vez.
    - **LastModified_To** = **LastModified_To\@gatilho().saídas.windowEndTime**.  É uma variável de sistema do gatilho que determina a hora em que o gasoduto é acionado desta vez.
    
    ![Parâmetros de entrada](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Selecione **Publicar Tudo**.
    
    ![Publicar Tudo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Crie novos arquivos em sua pasta de origem do armazenamento de origem de dados.  Agora você está esperando o pipeline ser acionado automaticamente e apenas os novos arquivos serão copiados para a loja de destino.

13. Selecione A guia **Monitorar** no painel de navegação esquerdo e aguarde cerca de 15 minutos se a recorrência do gatilho tiver sido definida a cada 15 minutos. 

14. Revise o resultado. Você verá que seu pipeline será acionado automaticamente a cada 15 minutos, e apenas os arquivos novos ou alterados da loja de origem serão copiados para a loja de destino em cada execução de pipeline.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
