---
title: Procedimento de failover hana para um local de desastre para SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como executar failover em um site de recuperação de desastres para SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617150"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedimento de failover de recuperação de desastre


>[!IMPORTANT]
>Este artigo não é um substituto para a documentação de administração SAP HANA ou Notas SAP. Esperamos que você tenha uma compreensão sólida e experiência em administração e operações SAP HANA, especialmente para backup, restauração, alta disponibilidade e recuperação de desastres (DR). Neste artigo, capturas de tela do SAP HANA Studio são mostradas. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de uma versão do SAP HANA para outra versão.

Há dois casos a considerar quando você falha em um site de DR:

- É necessário que o banco de dados do SAP HANA retorne para o status mais recente dos dados. Neste caso, há um script de autoatendimento com o qual você pode executar o failover sem a necessidade de entrar em contato com a Microsoft. Para o failback, você precisa trabalhar com a Microsoft.
- Você deseja restaurar um instantâneo de armazenamento que não seja o mais recente instantâneo replicado. Nesse caso, você precisa trabalhar com a Microsoft. 

>[!NOTE]
>As seguintes etapas devem ser feitas na unidade HANA Large Instance, que representa a unidade DR. 
 
Para restaurar os mais recentes instantâneos de armazenamento replicados, siga as etapas em "Executar failover COMPLETO dr - azure_hana_dr_failover" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf). 

Se você quiser que várias instâncias SAP HANA falhem, execute o comando azure_hana_dr_failover várias vezes. Quando solicitado, digite o SID SAP HANA que deseja falhar e restaurar. 


Você pode testar o failover dr também sem afetar a relação de replicação real. Para executar um failover de teste, siga as etapas em "Executar um failover de TESTE DR - azure_hana_test_dr_failover" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf). 

>[!IMPORTANT]
>*Não* execute nenhuma transação de produção na instância que você criou no site da DR através do processo de teste de **um failover**. O comando azure_hana_test_dr_failover cria um conjunto de volumes que não têm relação com o site principal. Como resultado, a sincronização de volta para o site primário *não* é possível. 

Se você quiser ter várias instâncias SAP HANA para testar, execute o script várias vezes. Quando solicitado, digite o SID SAP HANA da instância que deseja testar para failover. 

>[!NOTE]
>Se você precisar falhar no site do DR para resgatar alguns dados que foram excluídos horas atrás e precisar que os volumes de DR sejam definidos como um instantâneo anterior, este procedimento se aplica. 

1. Desligue a instância de não produção do HANA na unidade de recuperação de desastres do HANA Large Instances que você está executando. Uma instância de produção HANA adormecida é pré-instalada.
1. Certifique-se de que não há nenhum processo do SAP HANA em execução. Use o seguinte comando para esta verificação:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      A saída deve mostrar-lhe o processo **hdbdaemon** em um estado parado e nenhum outro processo HANA em um estado executado ou iniciado.
1. Determine para qual nome de instantâneo ou ID de backup do SAP HANA você deseja ter a recuperação de desastre restaurada. Em casos de recuperação de desastres reais, esse instantâneo é geralmente o instantâneo mais recente. Se você precisar recuperar dados perdidos, escolha um instantâneo mais antigo.
1. Contate o Suporte do Azure por meio de uma solicitação de suporte de alta prioridade. Peça a restauração desse instantâneo com o nome e a data do snapshot ou o ID de backup HANA no site da DR. O padrão é que o lado das operações restaura apenas o volume /hana/data. Se você quiser ter os volumes /hana/logbackups também, você precisa declarar especificamente isso. *Não restaure o volume/hana/shared.* Em vez disso, escolha arquivos específicos como global.ini fora do diretório **.snapshot** e seus subdiretórios depois de remontar o volume /hana/compartilhado para PRD. 

   No lado das operações, ocorrem as seguintes etapas:

   a. A replicação de instantâneos do volume de produção para volumes de recuperação de desastre é interrompida. Isso pode já ter acontecido se uma interrupção no local de produção for o motivo de você precisar realizar o procedimento de recuperação de desastre.
   
   b. O nome do instantâneo de armazenamento ou o instantâneo com a ID de backup escolhido é restaurado nos volumes de recuperação de desastre.
   
   c. Após a restauração, os volumes de recuperação de desastre estão disponíveis para serem montados para as unidades do SAP HANA em Instâncias Grandes na região de recuperação de desastre.
      
1. Monte os volumes de recuperação de desastre para a unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre. 
1. Inicie a instância de produção do SAP HANA inativa.
1. Se você optar por copiar logs de backup de transações para reduzir o tempo de RPO, mesclar os backups do log de transação no diretório DR /hana/logbackups recém-montado. Não substitua backups existentes. Copie backups mais recentes que não foram replicados com a replicação mais recente de um instantâneo de armazenamento.
1. Você também pode restaurar arquivos únicos fora dos instantâneos que não foram replicados para o volume /hana/compartilhado/PRD na região DR Azure.

As etapas a seguir mostram como recuperar a instância de produção do SAP HANA com base no instantâneo de armazenamento restaurado e nos backups do registro de transações disponíveis.

1. Altere o local de backup para **hana/logbackups** usando o SAP HANA Studio.

   ![Alterar o local de backup para recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. O SAP HANA verifica os locais do arquivo de backup e sugere o backup de log de transações mais recente para o qual ser restaurado. A varredura pode levar alguns minutos até que uma tela como a seguinte apareça:

   ![Lista de backups de log de transações para recuperação de DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Ajuste algumas destas configurações padrão:

      - Limpe **Usar backups delta**.
      - Selecione **Inicializar área de log**.

   ![Defina o Inicializar a área de log](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecione **Concluir**.

   ![Concluir a restauração da recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como mostrado aqui, deve aparecer. Tenha em mente que o exemplo é de uma restauração de recuperação de desastre de uma configuração de expansão do SAP HANA de três nós.

![Progresso da restauração](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se a restauração parar de responder na tela **Concluir** e não mostrar a tela de progresso, confirme se todas as instâncias de HANA SAP nos nós do trabalhador estão sendo executados. Se necessário, inicie manualmente as instâncias SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback de recuperação de desastre para um site de produção
É possível executar failback de uma recuperação de desastre para um local de produção. Vejamos um cenário em que o failover no site de recuperação de desastre foi causado por problemas na região de produção do Azure e não pela necessidade de recuperar dados perdidos. 

Você tem executado sua carga de trabalho de produção SAP por um tempo no local de recuperação de desastres. Conforme são resolvidos os problemas no local de produção, convém executar failback no local de produção. Como não é possível perder dados, a etapa no local de produção envolve várias etapas e cooperação com a equipe de operações do SAP HANA no Azure. Cabe a você acionar a equipe de operações para iniciar a sincronização de volta ao site de produção, depois que os problemas forem resolvidos.

Siga estas etapas:

1. A equipe de operações do SAP HANA do Azure obtém o gatilho para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação de desastre, que agora representam o estado de produção. Nesse estado, a unidade de Instância Grande do HANA no local de produção é desligada.
1. A equipe de operações sap HANA no Azure monitora a replicação e garante que ela seja capturada antes que eles o informem.
1. Desligue os aplicativos que usam a instância do HANA de produção no site de recuperação de desastre. Em seguida, faça um backup de log de transações do HANA. Em seguida, você interrompe a instância HANA que está sendo executado nas unidades HANA Large Instance no local de recuperação de desastres.
1. Após a ocorrência de HANA que está sendo executado na unidade HANA Large Instance no local de recuperação de desastres é encerrada, a equipe de operações sincroniza manualmente os volumes de disco novamente.
1. A equipe de operações SAP HANA on Azure inicia novamente a unidade HANA Large Instance no local de produção. Eles entregam para você. Certifique-se de que a ocorrência SAP HANA esteja em estado de desligamento no momento da inicialização da unidade HANA Large Instance.
1. Você executa as mesmas etapas de restauração de banco de dados que fez quando falhou anteriormente no site de recuperação de desastres.

## <a name="monitor-disaster-recovery-replication"></a>Monitorar a replicação de recuperação de desastre

Para monitorar o status do progresso da `azure_hana_replication_status`replicação de armazenamento, execute o script . Este comando deve ser executado a partir de uma unidade que é executada no local de recuperação de desastres para funcionar como esperado. O comando funciona não importa se a replicação está ativa. O comando pode ser executado para cada unidade HANA Large Instance do seu inquilino no local de recuperação de desastres. Ele não pode ser usado para obter detalhes sobre o volume de inicialização. 

Para obter mais informações sobre o comando e sua saída, consulte "Obter status de replicação DR - azure_hana_replication_status" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf).


## <a name="next-steps"></a>Próximas etapas
- Consulte [Monitor e solução de problemas do lado HANA](hana-monitor-troubleshoot.md).
