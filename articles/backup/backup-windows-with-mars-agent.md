---
title: Fazer backup das máquinas windows usando o agente MARS
description: Use o agente Microsoft Azure Recovery Services (MARS) para fazer backup das máquinas Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408905"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Faça backup das máquinas do Windows usando o agente MARS do Azure Backup

Este artigo explica como fazer backup de máquinas Windows usando o serviço de backup do [Azure](backup-overview.md) e o agente Mars (Microsoft Azure Recovery Services, serviços de recuperação do Microsoft Azure). A MARS também é conhecida como o agente de backup do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Verificar os pré-requisitos
> * Crie uma política de backup e um cronograma.
> * Realize um backup demanda.

## <a name="before-you-start"></a>Antes de começar

* Saiba como [o Azure Backup usa o agente MARS para fazer backup das máquinas Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Conheça a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) que executa o agente MARS em um servidor MABS ou Data Protection Manager secundário.
* Revise [o que é suportado e o que você pode fazer backup](backup-support-matrix-mars-agent.md) pelo agente da MARS.
* [Verifique](install-mars-agent.md#verify-internet-access) o acesso à internet nas máquinas que você deseja fazer backup.
* Se o agente MARS não estiver instalado, saiba como instalá-lo [aqui](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Criar uma política de backup

A política de backup especifica quando tirar instantâneos dos dados para criar pontos de recuperação. Ele também especifica quanto tempo para manter os pontos de recuperação. Você usa o agente MARS para configurar uma política de backup.

O Azure Backup não leva automaticamente em conta o horário de verão (DST). Esse padrão pode causar alguma discrepância entre o tempo real e o tempo de backup programado.

Para criar uma política de backup:

1. Depois de baixar e registrar o agente MARS, abra o console do agente. Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.  

1. Em **Ações,** selecione **Agendamento de backup**.

    ![Agendar um backup do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. No Assistente de backup de agendamento, **selecione Como iniciar a** > **próxima**.
1. Em **Selecionar itens para fazer backup,** **selecione Adicionar itens**.

    ![Adicionar itens para fazer backup](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Na caixa **Selecionar itens,** selecione itens para fazer backup e selecione **OK**.

    ![Selecionar itens para fazer backup](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na **página Selecionar itens para fazer backup,** selecione **Next**.
1. Na página **Especificar agendamento de backup,** especifique quando fazer backups diários ou semanais. Em seguida, selecione **Next**.

    * Um ponto de recuperação é criado quando um backup é feito.
    * O número de pontos de recuperação criados em seu ambiente depende do seu cronograma de backup.
    * Você pode agendar até três backups diários por dia. No exemplo a seguir, ocorrem dois backups diários, um à meia-noite e outro às 18h.

        ![Configure um cronograma de backup diário](./media/backup-configure-vault/day-schedule.png)

    * Você pode executar backups semanais também. No exemplo a seguir, os backups são feitos todos os domingos e quartas-feiras alternados às 9:30 am e 1:00 AM.

        ![Configure um cronograma de backup semanal](./media/backup-configure-vault/week-schedule.png)

1. Na página **Selecionar política de retenção,** especifique como armazenar cópias históricas de seus dados. Em seguida, selecione **Next**.

    * As configurações de retenção especificam quais pontos de recuperação a serem armazenar e quanto tempo para armazená-los.
    * Para uma configuração de retenção diária, você indica que no momento especificado para a retenção diária, o último ponto de recuperação será retido para o número especificado de dias. Ou você pode especificar uma política de retenção mensal para indicar que o ponto de recuperação criado no dia 30 de cada mês deve ser armazenado por 12 meses.
    * A retenção de pontos de recuperação diários e semanais geralmente coincide com o cronograma de backup. Assim, quando o cronograma aciona um backup, o ponto de recuperação que o backup cria é armazenado durante a duração que a política de retenção diária ou semanal especifica.
    * No exemplo a seguir:

        * Backups diários à meia-noite e 18:00 são mantidos por sete dias.
        * Backups feitos em um sábado à meia-noite e 18:00 são mantidos por quatro semanas.
        * Backups feitos no último sábado do mês à meia-noite e às 18:00 são mantidos por 12 meses.
        * Os backups feitos no último sábado de março são mantidos por 10 anos.

        ![Exemplo de uma política de retenção](./media/backup-configure-vault/retention-example.png)

1. Na página **Escolher tipo de backup inicial,** decida se deseja fazer o backup inicial pela rede ou usar backup off-line. Para fazer o backup inicial na rede, selecione **Automaticamente na rede** > **Next**.

    Para obter mais informações sobre backup off-line, consulte [Use Azure Data Box para backup off-line](offline-backup-azure-data-box.md).

    ![Escolha um tipo de backup inicial](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na página **Confirmação,** revise as informações e selecione **Concluir**.

    ![Confirme o tipo de backup](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Depois que o assistente terminar de criar o cronograma de backup, selecione **Fechar**.

    ![Veja o andamento do cronograma de backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Crie uma política em cada máquina onde o agente está instalado.

### <a name="do-the-initial-backup-offline"></a>Faça o backup inicial offline

Você pode executar um backup inicial automaticamente pela rede, ou pode fazer backup off-line. A semeação offline para um backup inicial é útil se você tiver grandes quantidades de dados que exigirão muita largura de banda da rede para transferir.

Para fazer uma transferência offline:

1. Escreva os dados de backup em um local de encenação.
1. Use a ferramenta AzureOfflineBackupDiskPrep para copiar os dados do local de preparação para um ou mais discos SATA.

    A ferramenta cria um trabalho de importação do Azure. Para obter mais informações, consulte [O que é o serviço de importação/exportação do Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).
1. Envie os discos SATA para um data center do Azure.

    No data center, os dados do disco são copiados para uma conta de armazenamento do Azure. O Azure Backup copia os dados da conta de armazenamento para o cofre e os backups incrementais são agendados.

Para obter mais informações sobre a semeada off-line, consulte [Use Azure Data Box para backup off-line](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Habilitar a limitação de rede

Você pode controlar como o agente MARS usa a largura de banda da rede, permitindo o estrangulamento da rede. O estrangulamento é útil se você precisar fazer backup de dados durante as horas de trabalho, mas deseja controlar a largura de banda que a atividade de backup e restauração usa.

O estrangulamento da rede no Azure Backup usa [qualidade de serviço (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) no sistema operacional local.

O estrangulamento da rede para backups está disponível no Windows Server 2012 e posterior, e no Windows 8 e posterior. Os sistemas operacionais devem estar executando os pacotes de serviço mais recentes.

Para habilitar o estrangulamento da rede:

1. No agente MARS, selecione **'Alterar propriedades'.**
1. Na guia **Estrangulação,** **selecione Ativar o estrangulamento do uso da largura de banda da Internet para operações de backup**.

    ![Configurar o estrangulamento da rede para operações de backup](./media/backup-configure-vault/throttling-dialog.png)
1. Especifique a largura de banda permitida durante as horas de trabalho e horas não trabalhadas. Os valores de largura de banda começam em 512 Kbps e vão até 1.023 MBps. Em seguida, selecione **OK**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

1. No agente MARS, selecione **Backup Agora**.

    ![Fazer backup agora no Windows Server](./media/backup-configure-vault/backup-now.png)

1. Se a versão do agente MARS for 2.0.9169.0 ou mais recente, então você pode definir uma data de retenção personalizada. Na **seção 'Reter backup ' till',** escolha uma data no calendário.

   ![Use o calendário para personalizar uma data de retenção](./media/backup-configure-vault/mars-ondemand.png)

1. Na página **Confirmação,** revise as configurações e selecione **Backup**.
1. Selecione **Fechar** para fechar o assistente. Se você fechar o assistente antes que o backup termine, o assistente continuará a ser executado em segundo plano.

Após o backup inicial terminar, o status **concluído do Trabalho** aparece no console Backup.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Configurar o comportamento de retenção de políticas de backup demanda

> [!NOTE]
> Essas informações se aplicam apenas às versões do agente MARS com mais de 2.0.9169.0.
>

| Opção de agendamento de backup | Duração da retenção de dados
| -- | --
| Dia | **Retenção padrão**: Equivalente à "retenção em dias para backups diários". <br/><br/> **Exceção**: Se um backup agendado diário definido para retenção a longo prazo (semanas, meses ou anos) falhar, um backup demanda que é acionado logo após a falha é considerado para retenção a longo prazo. Caso contrário, o próximo backup programado é considerado para retenção a longo prazo.<br/><br/> **Cenário de exemplo**: O backup programado na quinta-feira às 8:00 AM falhou. Esse backup deveria ser considerado para retenção semanal, mensal ou anual. Assim, o primeiro backup demanda acionado antes do próximo backup programado na sexta-feira às 8:00 AM é marcado automaticamente para retenção semanal, mensal ou anual. Este backup substitui para o backup de quinta-feira 8:00 AM.
| Semana | **Retenção padrão**: Um dia. Backups demanda que são feitos para uma fonte de dados que tem uma política de backup semanal são excluídos no dia seguinte. Eles são excluídos mesmo que sejam os backups mais recentes para a fonte de dados. <br/><br/> **Exceção**: Se um backup agendado semanal que for definido para retenção a longo prazo (semanas, meses ou anos) falhar, um backup sob demanda que é acionado logo após a falha é considerado para retenção a longo prazo. Caso contrário, o próximo backup programado é considerado para retenção a longo prazo. <br/><br/> **Cenário de exemplo**: O backup programado na quinta-feira às 8:00 AM falhou. Esse backup deveria ser considerado para retenção mensal ou anual. Assim, o primeiro backup sob demanda que é acionado antes do próximo backup programado na quinta-feira às 8:00 AM é marcado automaticamente para retenção mensal ou anual. Este backup substitui para o backup de quinta-feira 8:00 AM.

Para obter mais informações, consulte [Criar uma política de backup](#create-a-backup-policy).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [restaurar arquivos no Azure](backup-azure-restore-windows-server.md).
* Encontre [perguntas comuns sobre backup de arquivos e pastas](backup-azure-file-folder-backup-faq.md)

