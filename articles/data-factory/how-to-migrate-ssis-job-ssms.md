---
title: Migrar empregos de SQL Server Integration Services (SSIS) no local para a fábrica de dados do Azure
description: Este artigo descreve como migrar os empregos sql server integration services (SSIS) para pipelines/atividades/gatilhos da Fábrica de Dados Do Azure usando o SQL Server Management Studio.
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
ms.openlocfilehash: 6e357e98d6c5190c6dfef675dc1ab9cf30a717c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455080"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrar empregos de agente de servidor SQL para ADF com SSMS

Ao [migrar as cargas de trabalho do SQL Server Integration Services (SSIS) para SSIS no ADF](scenario-ssis-migration-overview.md), depois que os pacotes SSIS forem migrados, você pode fazer a migração em lote de trabalhos do SQL Server Agent **SSIS Job Migration Wizard**com o tipo de passo de trabalho do Pacote de Serviços de Integração de Servidores SQL para a af

Em geral, para trabalhos de agente SQL selecionados com tipos de etapas de trabalho aplicáveis, **o SSIS Job Migration Wizard** pode:

- mapear a localização do pacote SSIS no local para onde os pacotes são migrados, que são acessíveis pelo SSIS no ADF.
    > [!NOTE]
    > A localização do pacote do Sistema de Arquivos é suportada apenas.
- migrar os trabalhos aplicáveis com etapas de trabalho aplicáveis aos recursos Correspondentes do ADF conforme abaixo:

|Objeto de trabalho do Agente SQL  |Recurso ADF  |Observações|
|---------|---------|---------|
|Trabalho de agente SQL|pipeline     |O nome do gasoduto será *gerado para \<nome de emprego>*. <br> <br> Os trabalhos de agente incorporado não são aplicáveis: <li> Trabalho de manutenção do servidor SSIS <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|Etapa de trabalho do SSIS|Executar a atividade do pacote SSIS|<li> O nome da \<atividade será o nome da etapa>. <li> A conta proxy usada na etapa de trabalho será migrada à medida que o Windows autenticar essa atividade. <li> *Opções de execução,* *exceto Use o tempo de execução de 32 bits* definido na etapa de trabalho será ignorado na migração. <li> *A verificação* definida na etapa de trabalho será ignorada na migração.|
|schedule      |agendar o gatilho        |O nome do gatilho de agendamento será *gerado para \<o nome do horário>*. <br> <br> As opções abaixo no cronograma de trabalho do SQL Agent serão ignoradas na migração: <li> Intervalo de segundo nível. <li> *Iniciar automaticamente quando o SQL Server Agent for iniciado* <li> *Iniciar sempre que as CPUs estiverem ociosas* <li> *dia da semana* e *dia de fim de semana*<time zone> <br> Abaixo estão as diferenças após o cronograma de trabalho do Agente SQL ser migrado para o gatilho de cronograma do ADF: <li> ADF Schedule Trigger a execução subseqüente é independente do estado de execução do antecedente acionado. <li> A configuração de recorrência do acionamento de gatilho do ADF difere da freqüência diária no trabalho de agente SQL.|

- gerar modelos ARM (Azure Resource Manager) na pasta de saída local e implantar na fábrica de dados diretamente ou posteriormente. Para obter mais informações sobre os modelos do ADF Resource Manager, consulte [os tipos de recursos microsoft.dataFactory](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Pré-requisitos

O recurso descrito neste artigo requer sql server management studio versão 18.5 ou superior. Para obter a versão mais recente do SSMS, confira [Baixar o SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrar empregos do SSIS para ODF

1. No SSMS, no Object Explorer, selecione SQL Server Agent, selecione Jobs e clique com o botão direito do mouse e selecione **Migrar trabalhos SSIS para ADF**.
![Menu](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Inscreva-se no Azure, selecione Azure Subscription, Data Factory e Integration Runtime. O Azure Storage é opcional, que é usado na etapa de mapeamento de localização do pacote se os trabalhos do SSIS a serem migrados tiverem pacotes do Sistema de Arquivos SSIS.
![Menu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mapeie os caminhos dos pacotes SSIS e arquivos de configuração em trabalhos ssis para caminhos de destino onde os pipelines migrados podem acessar. Nesta etapa de mapeamento, você pode:

    1. Selecione uma pasta de origem e, em seguida, **adicione mapeamento**.
    1. Atualizar o caminho da pasta de origem. Caminhos válidos são caminhos de pasta ou caminhos de pasta pai de pacotes.
    1. Atualizar o caminho da pasta de destino. O padrão é o caminho relativo à conta de armazenamento padrão, que é selecionada na etapa 1.
    1. Exclua um mapeamento selecionado via **Mapeamento de Exclusão**.
![passo2](media/how-to-migrate-ssis-job-ssms/step2.png)
![passo2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Selecione os trabalhos aplicáveis para migrar e configure as configurações da atividade correspondente do *pacote SSIS executado*.

    - *Configuração padrão,* aplica-se a todas as etapas selecionadas por padrão. Para obter mais informações sobre cada propriedade, consulte *A guia Configurações* para executar a [atividade do pacote SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando o local do pacote é *O Sistema de Arquivos (Pacote)*.
    ![passo3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Configuração de*etapas , configure a configuração de uma etapa selecionada.
        
        **Aplicar Configuração padrão**: o padrão é selecionado. Desmarcar para configurar a configuração apenas para a etapa selecionada.  
        Para obter mais informações sobre outras propriedades, consulte *A guia Configurações* para executar a [atividade do pacote SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando o local do pacote é *O Sistema de Arquivos (Pacote)*.
    ![passo3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Gerar e implantar o modelo ARM.
    1. Selecione ou insira o caminho de saída para os modelos ARM dos gasodutos ADF migrados. A pasta será criada automaticamente se não existir.
    2. Selecione a opção **implantar modelos ARM na fábrica de dados**:
        - O padrão não é selecionado. Você pode implantar modelos ARM gerados mais tarde manualmente.
        - Selecione para implantar modelos ARM gerados na fábrica de dados diretamente.
    ![etapa4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migre e verifique os resultados.
![passo5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Próximas etapas

[Executar e monitorar o pipeline](how-to-invoke-ssis-package-ssis-activity.md)
