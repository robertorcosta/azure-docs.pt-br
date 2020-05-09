---
title: Migrar trabalhos do SSIS (SQL Server Integration Services local) para Azure Data Factory
description: Este artigo descreve como migrar trabalhos do SQL Server Integration Services (SSIS) para Azure Data Factory pipelines/atividades/gatilhos usando SQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: b27fe2abc50396b527e61487acf9797db59c1cce
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627578"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrar trabalhos do SQL Server Agent para o ADF com o SSMS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao [migrar cargas de trabalho do SSIS (SQL Server Integration Services locais) para o SSIS no ADF](scenario-ssis-migration-overview.md), depois que os pacotes SSIS são migrados, você pode fazer a migração em lote de trabalhos de SQL Server Agent com o tipo de etapa de trabalho do pacote de SQL Server Integration Services para Azure data Factory (ADF) pipelines/atividades/agendamentos de agendamento por meio do **Assistente de migração de trabalho SSIS**do SQL Server Management Studio (SSMS).

Em geral, para trabalhos selecionados do SQL Agent com tipos de etapa de trabalho aplicáveis, o **Assistente de migração de trabalho do SSIS** pode:

- Mapeie a localização do pacote SSIS local para o qual os pacotes são migrados, que podem ser acessados pelo SSIS no ADF.
    > [!NOTE]
    > Só há suporte para o local do pacote do sistema de arquivos.
- Migre trabalhos aplicáveis com etapas de trabalho aplicáveis para os recursos do ADF correspondentes, como mostrado abaixo:

|Objeto de trabalho do SQL Agent  |Recurso do ADF  |Anotações|
|---------|---------|---------|
|Trabalho do SQL Agent|pipeline     |O nome do pipeline será *gerado para \<o nome do trabalho>*. <br> <br> Os trabalhos internos do agente não são aplicáveis: <li> Trabalho de manutenção do servidor SSIS <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|Etapa de trabalho do SSIS|Atividade executar pacote SSIS|<li> O nome da atividade será o \<nome da etapa>. <li> A conta proxy usada na etapa de trabalho será migrada como autenticação do Windows desta atividade. <li> *As opções de execução* , exceto *usar tempo de execução de 32 bits* definidos na etapa de trabalho, serão ignoradas na migração. <li> A *verificação* definida na etapa de trabalho será ignorada na migração.|
|schedule      |agendar o gatilho        |O nome do gatilho de agendamento será *gerado para \<o nome da agenda>*. <br> <br> As opções abaixo na agenda de trabalho do SQL Agent serão ignoradas na migração: <li> Intervalo de segundo nível. <li> *Iniciar automaticamente quando o SQL Server Agent for iniciado* <li> *Iniciar sempre que as CPUs estiverem ociosas* <li> dia *útil* e fim de *semana*<time zone> <br> Abaixo estão as diferenças após o agendamento de trabalho do SQL Agent ser migrado para o gatilho de agenda do ADF: <li> O gatilho de agendamento do ADF de execução subsequente é independente do estado de execução da execução disparada por Antecedent. <li> A configuração de recorrência do gatilho de agendamento do ADF difere da frequência diária no trabalho do SQL Agent.|

- gere modelos de Azure Resource Manager (ARM) na pasta de saída local e implante em data factory diretamente ou mais tarde manualmente. Para obter mais informações sobre os modelos do Gerenciador de recursos do ADF, consulte [tipos de recurso Microsoft. datafactory](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Pré-requisitos

O recurso descrito neste artigo requer SQL Server Management Studio versão 18,5 ou superior. Para obter a versão mais recente do SSMS, confira [Baixar o SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrar trabalhos do SSIS para o ADF

1. No SSMS, no Pesquisador de objetos, selecione SQL Server Agent, selecione trabalhos, clique com o botão direito do mouse e selecione **migrar trabalhos do SSIS para o ADF**.
![AdicionarMenu](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Entre no Azure, selecione assinatura do Azure, Data Factory e Integration Runtime. O armazenamento do Azure é opcional, que será usado na etapa de mapeamento de local do pacote se os trabalhos do SSIS a serem migrados tiverem pacotes do sistema de arquivos SSIS.
![AdicionarMenu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mapeie os caminhos de pacotes do SSIS e arquivos de configuração em trabalhos do SSIS para caminhos de destino em que os pipelines migrados podem acessar. Nesta etapa de mapeamento, você pode:

    1. Selecione uma pasta de origem e, em seguida, **Adicionar mapeamento**.
    1. Caminho da pasta de origem da atualização. Caminhos válidos são caminhos de pasta ou caminhos de pasta pai de pacotes.
    1. Caminho da pasta de destino da atualização. O padrão é o caminho relativo para a conta de armazenamento padrão, que é selecionada na etapa 1.
    1. Exclua um mapeamento selecionado por meio de **mapeamento de exclusão**.
![etapa 2](media/how-to-migrate-ssis-job-ssms/step2.png)
![etapa 2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Selecione os trabalhos aplicáveis a serem migrados e defina as configurações da *atividade de pacote SSIS executada*correspondente.

    - A *configuração padrão*aplica-se a todas as etapas selecionadas por padrão. Para obter mais informações sobre cada propriedade, consulte a *guia Configurações* da [atividade executar pacote SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando o local *do pacote é sistema de arquivos (pacote)*.
    ![etapa 3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Configuração de etapa*, defina a configuração para uma etapa selecionada.
        
        **Aplicar configuração padrão**: o padrão é selecionado. Desmarque para definir a configuração somente para a etapa selecionada.  
        Para obter mais informações sobre outras propriedades, consulte a *guia Configurações* da [atividade executar pacote SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando o local *do pacote é sistema de arquivos (pacote)*.
    ![etapa 3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Gere e implante o modelo ARM.
    1. Selecione ou insira o caminho de saída para os modelos ARM dos pipelines do ADF migrados. A pasta será criada automaticamente se não existir.
    2. Selecione a opção de **implantar modelos de ARM em seu data Factory**:
        - O padrão é desmarcado. Você pode implantar modelos ARM gerados mais tarde manualmente.
        - Selecione para implantar modelos ARM gerados para data factory diretamente.
    ![etapa4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migre e, em seguida, verifique os resultados.
![Step5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Próximas etapas

[Executar e monitorar o pipeline](how-to-invoke-ssis-package-ssis-activity.md)
