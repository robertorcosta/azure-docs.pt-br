---
title: Fazer backup de um Exchange Server por meio do System Center DPM
description: Saiba como fazer backup de um servidor do Exchange no Backup do Azure usando o System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 813a13739020bed839cc389897704395c77a322d
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586488"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Fazer backup de um servidor do Exchange no Backup do Azure com o System Center 2012 R2 DPM

Este artigo descreve como configurar um servidor do System Center 2012 R2 DPM (Data Protection Manager) para fazer backup de um servidor do Microsoft Exchange no Backup do Azure.  

## <a name="updates"></a>Atualizações

Para registrar o servidor DPM no Backup do Azure, você deve instalar o pacote cumulativo de atualizações mais recente do System Center 2012 R2 DPM e a versão mais recente do Agente de Backup do Azure. Obtenha o pacote cumulativo de atualizações mais recente no [Catálogo da Microsoft](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Para os exemplos neste artigo, a versão 2.0.8719.0 do Agente de Backup do Azure está instalada, e Atualização Cumulativa 6 está instalada no System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Prerequisites

Antes de continuar, verifique se todos os [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para uso do Backup do Microsoft Azure como proteção às cargas de trabalho foram atendidos. Esses pré-requisitos incluem os seguintes:

* Criação de um cofre de backup no site do Azure.
* Download das credenciais do agente e do cofre no servidor DPM.
* Instalação do agente no servidor DPM.
* Uso das credenciais do cofre para registro no servidor DPM.
* Se você estiver protegendo o Exchange 2016, atualize para o DPM 2012 R2 UR9 ou posterior

## <a name="dpm-protection-agent"></a>Agente de proteção do DPM

Execute estas etapas para instalar o agente de proteção do DPM no servidor do Exchange:

1. Verifique se os firewalls estão configurados corretamente. Confira [Configurar exceções de firewall do agente](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758204(v=sc.12)).
2. Instale o agente no servidor do Exchange clicando em **Gerenciamento > Agentes > Instalar** no Console do Administrador do DPM. Confira [Instalar o agente de proteção do DPM](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758186(v=sc.12)) para conhecer as etapas detalhadas.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Criar um grupo de proteção do Exchange server

1. No Console do Administrador do DPM, clique em **Proteção** e, em seguida, clique em **Novo** na faixa de opções de ferramenta para abrir o assistente **Criar Novo Grupo de Proteção**.
2. Na tela de **boas-vindas** do assistente, clique em **Avançar**.
3. Na tela **Selecionar tipo do grupo de proteção**, escolha **Servidores** e clique em **Próximo**.
4. Escolha o banco de dados do servidor do Exchange que você quer proteger e clique em **Próximo**.

   > [!NOTE]
   > Se você estiver protegendo o Exchange 2013, verifique os [pré-requisitos do Exchange 2013](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/dn751029(v=sc.12)).
   >
   >

    No exemplo abaixo, o banco de dados do Exchange 2010 foi selecionado.

    ![Selecionar membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Dê um nome ao grupo de proteção e escolha as duas opções a seguir:

   * Desejo a proteção de curto prazo usando Disco.
   * Desejo a proteção online.
6. Clique em **Próximo**.
7. Escolha a opção **Executar Eseutil para verificar a integridade dos dados** se você quiser verificar a integridade dos bancos de dados do Exchange Server.

    Depois de escolher essa opção, a verificação de consistência do backup será executada no servidor DPM a fim de evitar o tráfego de E/S gerado pela execução do comando **eseutil** no servidor Exchange.

   > [!NOTE]
   > Para usar essa opção, você deve copiar os arquivos Ese.dll e Eseutil.exe no diretório C:\Arquivos de Programas\Microsoft System Center 2012 R2\DPM\DPM\bin no servidor DPM. Caso contrário, o seguinte erro será disparado:  
   > ![erro de eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Clique em **Próximo**.
9. Escolha o banco de dados para **Copiar o Backup** e clique em **Próximo**.

   > [!NOTE]
   > Se você não escolher "Backup completo" para pelo menos uma cópia de DAG de um banco de dados, os logs não ficarão truncados.
   >
   >
10. Configure as metas de **Backup de Curto Prazo** e clique em **Próximo**.
11. Confira o espaço em disco disponível e clique em **Próximo**.
12. Escolha a hora na qual o servidor DPM criará a replicação inicial e clique em **Próximo**.
13. Escolha as opções de verificação de consistência e clique em **Próximo**.
14. Escolha o banco de dados do qual você deseja fazer backup no Azure e clique em **Próximo**. Por exemplo:

    ![Especificar dados de proteção online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Defina o cronograma do **Backup do Azure** e clique em **Próximo**. Por exemplo:

    ![Especifique o cronograma do backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Observe que os Pontos de recuperação online têm base nos pontos de recuperação completos expressos. Portanto, você deve agendar o ponto de recuperação online após o período especificado para o ponto de recuperação completo expresso.
    >
    >
16. Configure a política de retenção para o **Backup do Azure** e clique em **Próximo**.
17. Escolha uma opção de replicação online e clique em **Próximo**.

    Se você tiver um banco de dados de grande porte, talvez a criação do backup inicial na rede demore bastante. Para evitar esse problema, crie um backup offline.  

    ![Especificar política de retenção online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as configurações e clique em **Criar Grupo**.
19. Clique em **fechar**

## <a name="recover-the-exchange-database"></a>Recuperar o banco de dados do Exchange

1. Para recuperar um banco de dados do Exchange, clique em **Recuperação** no Console do Administrador do DPM.
2. Localize o banco de dados do Exchange que você deseja recuperar.
3. Selecione um ponto de recuperação online na lista suspensa *tempo de recuperação* .
4. Clique em **Recuperar** para iniciar o **Assistente de Recuperação**.

Há cinco tipos de recuperação para os pontos de recuperação online:

* **Recuperar no local original do Exchange Server:** os dados serão recuperados no servidor original do Exchange.
* **Recuperar em outro banco de dados em um servidor do Exchange:** os dados serão recuperados em outro banco de dados, em outro servidor do Exchange.
* **Recuperar um Banco de Dados de Recuperação:** os dados serão recuperados em um RDB (Banco de Dados de Recuperação do Exchange).
* **Copiar em uma pasta de rede:** os dados serão recuperados em uma pasta de rede.
* **Cópia em fita:** se você tiver uma biblioteca de fitas ou uma unidade de fita autônoma conectada e configurada no servidor DPM, o ponto de recuperação será copiado em uma fita livre.

    ![Escolher a replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Próximas etapas

* [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)
