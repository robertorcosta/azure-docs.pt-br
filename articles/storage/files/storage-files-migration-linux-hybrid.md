---
title: Migração do Linux para o Azure File Sync
description: Saiba como migrar arquivos de um local de servidor Linux para uma implantação de nuvem híbrida com compartilhamentos de arquivos Azure File Sync e Azure.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: c80f43d61aeea8aba803267b7908831209812d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247711"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrar do Linux para uma implantação de nuvem híbrida com o Azure File Sync

O Azure File Sync funciona em servidores Windows com DAS (Direct Attached Storage, armazenamento anexado direto). Ele não suporta sincronização de e para linux ou um compartilhamento remoto de SMB.
Como resultado, transformar seus serviços de arquivos em uma implantação híbrida torna necessária uma migração para um Servidor Windows. Este artigo orienta você através do planejamento e execução de tal migração.

## <a name="migration-goals"></a>Metas de migração

O objetivo é mover as ações que você tem em seu servidor Linux Samba para um Servidor Windows. Em seguida, utilize o Azure File Sync para uma implantação de nuvem híbrida. Essa migração precisa ser feita de forma a garantir a integridade dos dados de produção, bem como a disponibilidade durante a migração. Este último requer manter o tempo de inatividade ao mínimo, para que ele possa caber ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="migration-overview"></a>Visão geral da migração

Como mencionado no artigo visão geral da [migração](storage-files-migration-overview.md)do Azure Files, o uso da ferramenta de cópia e abordagem correta é importante. Seu servidor Linux Samba está expondo ações de SMB diretamente em sua rede local. RoboCopy, integrado ao Windows Server, é a melhor maneira de mover seus arquivos neste cenário de migração.

Se você não estiver executando o Samba no seu servidor Linux e preferir migrar pastas para uma implantação híbrida em um Servidor Windows, você pode usar ferramentas de cópia do Linux em vez do RoboCopy. Se você fizer isso, esteja ciente dos recursos de fidelidade em sua ferramenta de cópia de arquivos. Revise a [seção de fundamentos de migração](storage-files-migration-overview.md#migration-basics) no artigo de visão geral da migração para saber o que procurar em uma ferramenta de cópia.

- Fase 1: [Identifique quantos compartilhamentos de arquivos do Azure você precisa](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fase 2: [Provisão de um Windows Server adequado no local](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: [Implante o recurso de nuvem Do Azure File Sync](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fase 4: [Implantar recursos de armazenamento do Azure](#phase-4-deploy-azure-storage-resources)
- Fase 5: [Implante o agente de sincronização de arquivos Do Azure](#phase-5-deploy-the-azure-file-sync-agent)
- Fase 6: [Configure o Azure File Sync no Servidor Windows](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fase 7: [RoboCopy](#phase-7-robocopy)
- Fase 8: [Corte de usuário](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: Identifique quantos compartilhamentos de arquivos do Azure você precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: Provisão de um Windows Server adequado no local

* Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de fail-over do Windows Server também é suportado.
* Provisão ou adicionar armazenamento anexado direto (DAS em comparação com nas, que não é suportado).

    A quantidade de armazenamento que você provisão pode ser menor do que o que você está usando atualmente no servidor Linux Samba, se você usar o recurso [de hierarquiagem em nuvem](storage-sync-cloud-tiering.md) Do Azure File Syncs.
    No entanto, quando você copiar seus arquivos do espaço maior do servidor Linux Samba para o volume menor do Windows Server em uma fase posterior, você precisará trabalhar em lotes:

    1. Mova um conjunto de arquivos que se encaixam no disco.
    2. Deixe a sincronização de arquivos e o ennívelmento da nuvem engajarem.
    3. Quando mais espaço livre for criado no volume, prossiga com o próximo lote de arquivos. 
    
    Você pode evitar essa abordagem de loteamento provisionando o espaço equivalente no Servidor Windows que seus arquivos ocupam no servidor Linux Samba. Considere ativar a eliminação da duplicação no Windows. Se você não quiser comprometer permanentemente essa alta quantidade de armazenamento no seu Windows Server, você pode reduzir o tamanho do volume após a migração e antes de ajustar as políticas de hierarquidor de nuvem. Isso cria um cache menor no local de seus compartilhamentos de arquivos do Azure.

A configuração de recursos (computação e RAM) do Windows Server que você implanta depende principalmente do número de itens (arquivos e pastas) que você estará sincronizando. Recomendamos ir com uma configuração de desempenho mais alta se você tiver alguma preocupação.

[Aprenda a dimensionar um Servidor Windows com base no número de itens (arquivos e pastas) que você precisa sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo anteriormente vinculado apresenta uma tabela com um intervalo para memória do servidor (RAM). Você pode orientar para o número menor para o seu servidor, mas esperar que a sincronização inicial possa levar significativamente mais tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: Implante o recurso de nuvem Do Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Implantar recursos de armazenamento do Azure

Nesta fase, consulte a tabela de mapeamento da Fase 1 e use-a para provisionar o número correto de contas de armazenamento do Azure e compartilhamentos de arquivos dentro delas.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: Implante o agente de sincronização de arquivos Do Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: Configure o Azure File Sync no Servidor Windows

Seu Windows Server registrado no local deve estar pronto e conectado à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Hierarquica na nuvem é o recurso AFS que permite que o servidor local tenha menos capacidade de armazenamento do que é armazenado na nuvem, mas ainda tenha o namespace completo disponível. Dados localmente interessantes também são armazenados em cache local para um desempenho de acesso rápido. O hierarquiserimento na nuvem é um recurso opcional por "ponto final do servidor" do Azure File Sync.

> [!WARNING]
> Se você provisionou menos armazenamento no volume do servidor Windows do que seus dados usados no servidor Linux Samba, então o hierarquicamento na nuvem é obrigatório. Se você não ativar o hierquiamento na nuvem, seu servidor não liberará espaço para armazenar todos os arquivos. Defina sua política de hierarquidões, temporariamente para a migração, para 99% de espaço livre de volume. Certifique-se de retornar às configurações de hierarquidar na nuvem após a conclusão da migração e configurá-la para um nível útil mais a longo prazo.

Repita as etapas de criação do grupo de sincronização e a adição da pasta de servidor correspondente como um ponto final do servidor para todos os compartilhamentos de arquivos do Azure / locais do servidor, que precisam ser configurados para sincronização.

Após a criação de todos os pontos finais do servidor, a sincronização está funcionando. Você pode criar um arquivo de teste e vê-lo sincronizar-se da localização do servidor para o compartilhamento de arquivos Azure conectado (conforme descrito pelo ponto final da nuvem no grupo de sincronização).

Ambos os locais, as pastas do servidor e os compartilhamentos de arquivos do Azure estão vazios e aguardando dados em ambos os locais. Na próxima etapa, você começará a copiar arquivos no Windows Server for Azure File Sync para movê-los para a nuvem. No caso de você ter ativado o hierquidamento na nuvem, o servidor começará a níveis de arquivos, caso você fique sem capacidade nos volumes locais.

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopy

A abordagem de migração básica é um RoboCopy do servidor Linux Samba para o seu Servidor Windows e o Azure File Sync para compartilhamentos de arquivos Do Zure.

Execute a primeira cópia local para sua pasta de destino do Windows Server:

1. Identifique a primeira localização no servidor Linux Samba.
1. Identifique a pasta correspondente no Servidor Windows, que já tem o Azure File Sync configurado nele.
1. Inicie a cópia usando o RoboCopy.

O comando RoboCopy a seguir copiará arquivos do armazenamento de servidores Linux Samba para sua pasta-alvo do Windows Server. O Windows Server irá sincronizá-lo com o compartilhamento de arquivos Do Zure. 

Se você provisionou menos armazenamento no seu Windows Server do que seus arquivos no servidor Linux Samba, então você configurou hierarquididade na nuvem. À medida que o volume local do Windows Server fica cheio, [a hierarcada](storage-sync-cloud-tiering.md) na nuvem entrará em ação e os arquivos de nível que já foram sincronizados com sucesso. O hierarquidamento em nuvem gerará espaço suficiente para continuar a cópia do servidor Linux Samba. A hierarcada na nuvem verifica uma vez por hora para ver o que foi sincronizado e liberar espaço em disco para alcançar o espaço livre de volume de 99%.
É possível que o RoboCopy mova arquivos mais rápido do que você pode sincronizar com a nuvem e tier localmente, ficando assim sem espaço em disco local. RoboCopy falhará. Recomenda-se que você trabalhe através das ações em uma sequência que previne isso. Por exemplo, não iniciar trabalhos do RoboCopy para todas as ações ao mesmo tempo, ou apenas mover ações que se encaixam na quantidade atual de espaço livre no Windows Server, para mencionar alguns.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Plano de fundo:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy execute multi-threaded. O padrão é 8, o máximo é 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<nome do arquivo\>
   :::column-end:::
   :::column span="1":::
      Saídas de status para arquivo LOG como UNICODE (substitui log existente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Saídas para janela de console. Usado em conjunto com a saída de um arquivo de log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Executa o RoboCopy no mesmo modo que um aplicativo de backup usaria. Ele permite que o RoboCopy mova arquivos para os que o usuário atual não tem permissões.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Permite executar este comando RoboCopy várias vezes, sequencialmente no mesmo alvo/destino. Identifica o que foi copiado antes e omite-o. Apenas alterações, adições e " exclusões " serão*processadas,* o que ocorreu desde a última execução. Se o comando não foi executado antes, nada é omitido. O *sinalizador /MIR* é uma excelente opção para locais de origem que ainda são usados e alterados ativamente.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fidelidade da cópia do arquivo (padrão é /COPY:DAT), copiar sinalizadores: D=Dados, A=Atributos, T=Carimbos de tempo, ACLs S=Security=NTFS, Informações do O=Proprietário, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPIAR TODAS as informações do arquivo (equivalente a /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fidelidade para a cópia de diretórios (padrão é /DCOPY:DA), copiar sinalizadores: D=Dados, A=Atributos, T=Carimbos de tempo
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>Fase 8: Corte de usuário

Quando você executa o comando RoboCopy pela primeira vez, seus usuários e aplicativos ainda estão acessando arquivos no servidor Linux Samba e potencialmente os alteram. É possível que o RoboCopy tenha processado um diretório, passa para o próximo e, em seguida, um usuário no local de origem (Linux) adiciona, altera ou exclui um arquivo que agora não será processado nesta execução atual do RoboCopy. O comportamento é esperado.

A primeira execução é sobre mover a maior parte dos dados para o seu Windows Server e para a nuvem via Azure File Sync. Esta primeira cópia pode levar muito tempo, dependendo de:

* Sua largura de banda de download.
* A largura de banda de upload.
* A velocidade de rede local e o número de quão idealmente o número de threads RoboCopy corresponde a ele.
* O número de itens (arquivos e pastas) que precisam ser processados pelo RoboCopy e pelo Azure File Sync.

Uma vez que a execução inicial esteja concluída, execute o comando novamente.

A segunda vez ele vai terminar mais rápido, porque ele só precisa transportar mudanças que aconteceram desde a última corrida. Durante esta segunda corrida, ainda assim, novas mudanças podem se acumular.

Repita este processo até que você esteja convencido de que o tempo necessário para concluir um RoboCopy para um local específico está dentro de uma janela aceitável para o tempo de inatividade.

Quando você considera o tempo de inatividade aceitável e está preparado para tirar o local do Linux offline: Para tirar o acesso do usuário offline, você tem a opção de alterar ACLs na raiz de compartilhamento, de modo que os usuários não possam mais acessar o local ou tomar qualquer outro apropriado passo que impede que o conteúdo mude nesta pasta no servidor Linux.

Execute uma última rodada robocopy. Ele vai pegar qualquer mudança, que pode ter sido perdida.
Quanto tempo esse passo final leva, depende da velocidade da varredura robocopy. Você pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo quanto tempo a corrida anterior levou.

Crie um compartilhamento na pasta do Windows Server e, possivelmente, ajuste sua implantação do DFS-N para apontar para ela. Certifique-se de definir as mesmas permissões de nível de compartilhamento que as ações do SMB do servidor Linux Samba. Se você usou usuários locais em seu servidor Linux Samba, você precisa recriar esses usuários como usuários locais do Windows Server e mapear os SIDs existentes O RoboCopy passou para o seu Windows Server para os SIDs dos usuários locais do seu novo Windows Server. Se você usou contas AD e ACLs, o RoboCopy irá movê-las como está e não há nenhuma ação adicional necessária.

Você terminou de migrar uma ação /grupo de ações para uma raiz ou volume comum. (Dependendo do mapeamento da Fase 1)

Você pode tentar executar algumas dessas cópias em paralelo. Recomendamos processar o escopo de um compartilhamento de arquivos Azure por vez.

> [!WARNING]
> Uma vez que você tenha movido todos os dados do servidor Linux Samba para o Servidor Windows, e sua migração estiver completa: Retorne a ***todos os*** grupos de sincronização no portal Azure e ajuste o valor percentual de porcentagem de espaço livre de volume de hierarquantes na nuvem para algo mais adequado para a utilização do cache, digamos 20%. 

A política de espaço livre de hierarquiagem em nuvem atua em um nível de volume com potencialmente vários pontos finais de servidor sincronizando a partir dele. Se você esquecer de ajustar o espaço livre em um ponto final de servidor, a sincronização continuará a aplicar a regra mais restritiva e tentar manter 99% de espaço livre em disco, fazendo com que o cache local não esteja funcionando como você poderia esperar. A menos que seu objetivo tenha apenas o namespace para um volume que só contém dados de arquivamento raramente acessados e você está reservando o resto do espaço de armazenamento para outro cenário.

## <a name="troubleshoot"></a>Solução de problemas

O problema mais provável que você pode encontrar, é que o comando RoboCopy falha com *"Volume cheio"* no lado do Servidor do Windows. A hierarcada na nuvem atua uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server, que foi sincronizado. Seu objetivo é atingir seu espaço livre de 99% no volume.

Deixe o progresso de sincronização e a hierarquiagem da nuvem liberar espaço em disco. Você pode observar isso no File Explorer em seu Windows Server.

Quando o Windows Server tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada quebra quando você entra nessa situação e você pode seguir em frente com confiança. A inconveniência de comandar o comando novamente é a única consequência.

Verifique o link na seção a seguir para solucionar problemas do Azure File Sync.

## <a name="next-steps"></a>Próximas etapas

Há mais para descobrir sobre os compartilhamentos de arquivos do Azure e o Azure File Sync. Os artigos a seguir ajudam a entender opções avançadas, práticas recomendadas e também contêm ajuda para solucionar problemas. Esses artigos [vinculam-se à documentação de compartilhamento de arquivos do Azure](storage-files-introduction.md) conforme apropriado.

* [Visão geral do AFS](https://aka.ms/AFS)
* [Guia de implantação do AFS](storage-files-deployment-guide.md)
* [Solução de problemas da AFS](storage-sync-files-troubleshoot.md)
