---
title: Fazer backup do Exchange Server com o Servidor de Backup do Azure
description: Saiba como fazer backup de um servidor do Exchange no Backup do Azure usando o Servidor de Backup do Azure
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 1d7d28d813df82a5e1ea0fe424bba2ef5a9a2684
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80421343"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Fazer backup de um servidor do Exchange no Azure com o Servidor de Backup do Azure

Este artigo descreve como configurar o Servidor de Backup do Microsoft Azure (MABS) para fazer backup de um servidor do Microsoft Exchange no Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, verifique se o Servidor de Backup do Azure está [instalado e preparado](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agente de proteção do MABS

Execute estas etapas para instalar o agente de proteção do MABS no servidor do Exchange:

1. Verifique se os firewalls estão configurados corretamente. Confira [Configurar exceções de firewall do agente](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Instale o agente no servidor do Exchange clicando em **Gerenciamento > Agentes > Instalar** no Console do Administrador do MABS. Confira [Instalar o agente de proteção do MABS](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) para conhecer as etapas detalhadas.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Criar um grupo de proteção do Exchange server

1. No Console do Administrador do MABS, clique em **Proteção** e, em seguida, clique em **Novo** na faixa de opções de ferramenta para abrir o assistente **Criar Novo Grupo de Proteção**.
2. Na tela de **boas-vindas** do assistente, clique em **Avançar**.
3. Na tela **Selecionar tipo do grupo de proteção**, escolha **Servidores** e clique em **Próximo**.
4. Escolha o banco de dados do servidor do Exchange que você quer proteger e clique em **Próximo**.

   > [!NOTE]
   > Se você estiver protegendo o Exchange 2013, verifique os [pré-requisitos do Exchange 2013](https://docs.microsoft.com/system-center/dpm/back-up-exchange?view=sc-dpm-2016).
   >
   >

    No exemplo abaixo, o banco de dados do Exchange 2010 foi selecionado.

    ![Selecionar membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Dê um nome ao grupo de proteção e escolha as duas opções a seguir:

   * Desejo a proteção de curto prazo usando Disco.
   * Desejo a proteção online.
6. Clique em **Avançar**.
7. Escolha a opção **Executar Eseutil para verificar a integridade dos dados** se você quiser verificar a integridade dos bancos de dados do Exchange Server.

    Depois de selecionar essa opção, a verificação de consistência do backup será executada em MABS para evitar o tráfego de e/s gerado com a execução do comando **Eseutil** no servidor Exchange.

   > [!NOTE]
   > Para usar essa opção, você deve copiar os arquivos ESE. dll e Eseutil. exe para o diretório C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin no servidor MABS. Caso contrário, o seguinte erro será disparado:   
   > ![erro de eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Clique em **Avançar**.
9. Escolha o banco de dados para **Copiar o Backup** e clique em **Próximo**.

   > [!NOTE]
   > Se você não selecionar "backup completo" para pelo menos uma cópia DAG de um banco de dados, os logs não serão truncados.
   >
   >
10. Configure as metas de **Backup de Curto Prazo** e clique em **Próximo**.
11. Confira o espaço em disco disponível e clique em **Próximo**.
12. Selecione a hora em que o servidor MABS criará a replicação inicial e clique em **Avançar**.
13. Escolha as opções de verificação de consistência e clique em **Próximo**.
14. Escolha o banco de dados do qual você deseja fazer backup no Azure e clique em **Próximo**. Por exemplo:

    ![Especificar dados de proteção online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Defina o cronograma do **Backup do Azure** e clique em **Próximo**. Por exemplo:

    ![Especifique o cronograma do backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Observe que os Pontos de recuperação online têm base nos pontos de recuperação completos expressos. Portanto, você deve agendar o ponto de recuperação online após o horário especificado para o ponto de recuperação completo expresso.
    >
    >
16. Configure a política de retenção para o **Backup do Azure** e clique em **Próximo**.
17. Escolha uma opção de replicação online e clique em **Próximo**.

    Se você tiver um banco de dados de grande porte, talvez a criação do backup inicial na rede demore bastante. Para evitar esse problema, crie um backup offline.  

    ![Especificar política de retenção online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as configurações e clique em **Criar Grupo**.
19. Clique em **Fechar**.

## <a name="recover-the-exchange-database"></a>Recuperar o banco de dados do Exchange

1. Para recuperar um banco de dados do Exchange, clique em **Recuperação** no Console do Administrador do MABS.
2. Localize o banco de dados do Exchange que você deseja recuperar.
3. Selecione um ponto de recuperação online na lista suspensa *tempo de recuperação* .
4. Clique em **Recuperar** para iniciar o **Assistente de Recuperação**.

Há cinco tipos de recuperação para os pontos de recuperação online:

* **Recuperar no local original do Exchange Server:** os dados serão recuperados no servidor original do Exchange.
* **Recuperar em outro banco de dados em um servidor do Exchange:** os dados serão recuperados em outro banco de dados, em outro servidor do Exchange.
* **Recuperar um Banco de Dados de Recuperação:** os dados serão recuperados em um RDB (Banco de Dados de Recuperação do Exchange).
* **Copiar em uma pasta de rede:** os dados serão recuperados em uma pasta de rede.
* **Cópia em fita:** se você tiver uma biblioteca de fitas ou uma unidade de fita autônoma conectada e configurada no MABS, o ponto de recuperação será copiado em uma fita livre.

    ![Escolher a replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Próximas etapas

* [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)
