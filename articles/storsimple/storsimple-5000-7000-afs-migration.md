---
title: Migrar dados do StorSimple séries 5000-7000 para Sincronização de Arquivos do Azure| Microsoft Docs
description: Descreve como migrar dados do StorSimple séries 5000/7000 para AFS (Sincronização de Arquivos do Azure).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "65150748"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrar dados do StorSimple séries 5000-7000 para a Sincronização de Arquivos do Azure

> [!IMPORTANT]
> Em 9 de julho de 2019, a série StorSimple 5000/7000 atingirá o status de fim de suporte (EOS). É recomendável que os clientes da série StorSimple 5000/7000 migrem para uma das alternativas descritas no documento.

A migração de dados é o processo de mover dados de um local de armazenamento para outro. Isso implica fazer uma cópia exata dos dados atuais de uma organização de um dispositivo para outro — preferencialmente sem interromper ou desabilitar aplicativos ativos — e, em seguida, redirecionar toda a atividade de E/S (entrada/saída) para o novo dispositivo. 

Dispositivos de armazenamento do StorSimple das séries 5000 e 7000 chegarão ao final do serviço em julho de 2019. Isso significa que a Microsoft não poderá mais dar suporte para o hardware e software do StorSimple séries 5000/7000 após julho de 2019. Os clientes que estiverem usando esses dispositivos devem migrar os dados do StorSimple para outras soluções de armazenamento híbrido no Azure. Este artigo aborda a migração de dados de um dispositivo StorSimple séries 5000/7000 para AFS (Sincronização de Arquivos do Azure).

## <a name="intended-audience"></a>Público-alvo

Este artigo destina-se a profissionais de TI (tecnologia da informação) e profissionais do conhecimento responsáveis por implantar e gerenciar dispositivos do StorSimple 5000/7000 no datacenter. Clientes que usam dispositivos do StorSimple para cargas de trabalho do servidor de arquivos (com Windows Server) podem achar esse caminho de migração particularmente atraente. Se os recursos da Sincronização de Arquivos do Azure funcionam bem para a sua organização, este artigo irá ajudá-lo a entender como migrar para essas soluções do StorSimple.

## <a name="migration-considerations"></a>Considerações sobre migração

Esse processo funciona para clientes que configuraram um compartilhamento de arquivos do Windows usando um volume StorSimple para armazenamento. Migrar dados do StorSimple 5000/7000 para Sincronização de Arquivos do Azure envolve converter esse local de compartilhamento de arquivos em um [Ponto de Extremidade de Servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) e, em seguida, usar unidades conectadas localmente como outro ponto de extremidade que se tornará o novo local. 

Ao mover para AFS, os pontos a seguir deverão ser considerados:

1. Atualmente, os Arquivos do Azure têm uma restrição de 5 TB/compartilhamento. Essa restrição pode ser resolvida usando a Sincronização de Arquivos do Azure com dados distribuídos por vários compartilhamentos de Arquivos do Azure. Para obter mais informações, consulte o [Padrão de crescimento de dados para implantação de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Essa migração baixa todo o conjunto de dados primário para um dispositivo local, pois a cópia de dados é feita a partir do dispositivo local. Assegure-se de que tenha largura de banda suficiente para acomodar essa transferência.
3. Este processo não preserva os instantâneos que já foram criados. Apenas migra os dados primários. O processo também não preserva os modelos de largura de banda associados ou as políticas de backup. [Use o Backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-files) para configurar as políticas de backup depois que os dados forem migrados no compartilhamento de Arquivos do Azure.
4. O StorSimple fornece hardware interno. No entanto, com Sincronização de Arquivos do Azure/Arquivos do Azure o próprio hardware local do Windows Server estará sendo usado como o cache local. É necessário garantir que tenha capacidade de armazenamento suficiente para manter o conjunto de dados de sua escolha local. Para obter mais informações sobre camadas e requisito de espaço livre necessário de configuração, revise como [Criar um ponto de extremidade de servidor ao implantar Sincronização de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Revise o [preço para Sincronização de Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/), pois ele varia de acordo com o StorSimple. A AFS não possui eliminação de duplicação e compactação, como o StorSimple.

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Aqui você encontrará os pré-requisitos de migração para seu dispositivo herdado 5000 ou da série 7000 para Sincronização de Arquivos do Azure. Antes de começar, verifique se você tem:

- Acesso a um dispositivo StorSimple 5000/7000 executando a versão de software v2.1.1.518 ou posterior. Não há suporte para versões anteriores. O canto superior direito da interface do usuário da Web do dispositivo StorSimple deve exibir a versão do software em execução.  
    Se o dispositivo não estiver executando a v2.1.1.518, atualize o sistema para a versão mínima requerida. Para obter instruções detalhadas, consulte [Atualizar seu sistema para v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Verifique se há trabalhos de backup ativos em execução no dispositivo de origem. Isso inclui trabalhos no host do Console de Proteção de Dados do StorSimple. Aguarde até que os trabalhos atuais sejam concluídos. 
- Acesso a um host do Windows Server conectado ao dispositivo StorSimple séries 5000-7000. O host deve estar executando uma versão com suporte do Windows Server, conforme descrito em [Interoperabilidade da Sincronização de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Volumes do StorSimple são montados no host e contêm compartilhamentos de arquivos.
- O host tem armazenamento local suficiente para armazenar os dados armazenados em cache localmente.
- Acesso de nível proprietário à assinatura do Azure que será usada para implantar Sincronização de Arquivos do Azure. Você pode enfrentar problemas ao criar um ponto de extremidade de nuvem para seu grupo de sincronização se você não tiver permissões de nível de administrador ou proprietário.
- Acesso a uma [conta de armazenamento v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-overview) com um compartilhamento de arquivos do Azure ao qual você deseja sincronizar. Para obter mais informações, consulte [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Como [Criar um Compartilhamento de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Processo de migração

A migração de dados do StorSimple 5000-7000 para AFS é um processo de duas etapas:
1.  Replique os dados do servidor de arquivos local em que os volumes do StorSimple estão montados em um compartilhamento de Arquivos do Azure.  A replicação é feita por meio de um agente de AFS que você instala.
2.  Desconecte o dispositivo StorSimple. Os discos locais atuam como o cache local.

### <a name="migration-steps"></a>Etapas da migração

Execute as etapas a seguir para migrar o compartilhamento de arquivos do Windows configurado em volumes do StorSimple para um compartilhamento de Sincronização de Arquivos do Azure. 
1.  Execute estas etapas no mesmo host do Windows Server em que os volumes do StorSimple estão montados ou use um sistema diferente. 
    - [Prepare o Windows Server para usar com sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Instale o agente de sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Implante o serviço de sincronização de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registre o Windows Server com o serviço de sincronização de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Criar um grupo de sincronização e um ponto de extremidade de nuvem](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). É necessário fazer grupos de sincronização para cada compartilhamento de arquivos do Windows que precisa ser migrado do host.
    - [Criar um ponto de extremidade de servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Especifique o caminho como o caminho do volume do StorSimple que contém os dados de compartilhamento de arquivos. Por exemplo, se o volume do StorSimple for a unidade `J` e os dados residirem em `J:/<myafsshare>`, inclua esse caminho como um ponto de extremidade de servidor. Deixe a **Camada** como **Desabilitado**.
2.  Aguarde até que a sincronização do servidor de arquivos esteja concluída. Para cada servidor em um determinado grupo de sincronização, verifique se:
    - Os timestamps da última tentativa de sincronização para upload e download são recentes.
    - O status é verde para carregamento e download.
    - A **Atividade de Sincronização** mostra poucos ou nenhum arquivo restante para sincronizar.
    - **Arquivos Não Sincronizando** é 0 para upload e download.
    Para obter mais informações sobre quando a sincronização do servidor estiver concluída, vá para [solucionar problemas sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). A sincronização pode levar várias horas a dias, dependendo do tamanho dos dados e da largura de banda. Quando a sincronização estiver concluída, todos os dados estarão em segurança no Compartilhamento de Arquivos do Azure. 
3.  Acesse os compartilhamentos nos volumes do StorSimple. Selecione um compartilhamento, clique com o botão direito e selecione **Propriedades**. Observe as permissões de compartilhamento em **Segurança**. Será necessário aplicar essas permissões precisarão manualmente ao novo compartilhamento na etapa posterior.
4.  Dependendo se você usa o mesmo host do Windows Server ou um diferente, as próximas etapas serão diferentes.

    Ignore essa etapa e prossiga para a próxima etapa, se estiver usando um host diferente do Windows Server. Se você estiver usando o mesmo servidor de arquivos do Windows para AFS, agora passará por alguns minutos de tempo de inatividade. 
    - **Tempo de inatividade inicia** - Exclua o ponto de extremidade de servidor criado na *etapa 1F*. 
    - Crie um novo ponto de extremidade de servidor com o caminho em que você quer que os dados residam daqui em diante.
    - Depois que o ponto de extremidade de servidor mostrar como Íntegro (isso poderá demorar alguns minutos), você verá os dados nesse novo local. Agora, é possível configurar o host do Windows Server para servir arquivos desse novo local. - **Tempo de inatividade termina**.
5.  Se estiver usando outro servidor de arquivos do Windows para Sincronização de Arquivos do Azure, não haverá nenhum tempo de inatividade. 
    - Adicione outro ponto de extremidade de servidor com o caminho do armazenamento local que está preparado para usar como um cache, em vez do dispositivo StorSimple. 
    - Será possível ver os arquivos no novo servidor em poucos minutos. Você poderá alternar o dispositivo StorSimple para esse novo local no host a qualquer momento.

    > [!TIP] 
    > Para minimizar interrupção, considere configurar esse novo compartilhamento de arquivos com o mesmo nome e o mesmo caminho daquele que está sendo substituído. Se estiver usando DFS-N, isso poderá exigir que você faça alterações na configuração.
6.  Reconfigure as permissões de compartilhamento, conforme observado na *etapa 3*.

Se você enfrentar algum problema durante a migração de dados, [Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Próximas etapas

Se a AFS não for a solução que você precisa, saiba como [Migrar dados de um StorSimple séries 5000-7000 para um dispositivo da série 8000](storsimple-8000-migrate-from-5000-7000.md).

