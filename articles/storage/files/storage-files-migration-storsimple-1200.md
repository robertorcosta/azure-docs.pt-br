---
title: Migração do StorSimple 1200 para o Azure File Sync
description: Saiba como migrar um aparelho virtual da série StorSimple 1200 para o Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502389"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Migração do StorSimple 1200 para o Azure File Sync

A série StorSimple 1200 é um aparelho virtual que é executado em um data center local. É possível migrar os dados deste aparelho para um ambiente Azure File Sync. O Azure File Sync é o serviço Azure padrão e estratégico de longo prazo para o que os aparelhos StorSimple podem ser migrados.

A série StorSimple 1200 chegará ao fim [da vida útil](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) em dezembro de 2022.  É importante começar a planejar sua migração o mais rápido possível. Este artigo fornece as etapas necessárias de conhecimento de fundo e migrações para uma migração bem-sucedida para o Azure File Sync. 

## <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure

> [!IMPORTANT]
> A Microsoft está comprometida em ajudar os clientes em sua migração. Envie AzureFilesMigration@microsoft um e-mail para um plano de migração personalizado, bem como assistência durante a migração.

O Azure File Sync é um serviço de nuvem da Microsoft, baseado em dois componentes principais:

* Sincronização de arquivos e hierarquiagem na nuvem.
* Compartilhamentos de arquivos como armazenamento nativo no Azure, que podem ser acessados em vários protocolos como SMB e file REST. Um compartilhamento de arquivos Azure é comparável a um compartilhamento de arquivos em um Servidor Windows, que você pode montar nativamente como uma unidade de rede. Ele suporta aspectos importantes de fidelidade de arquivos, como atributos, permissões e carimbos de tempo. Ao contrário do StorSimple, nenhum aplicativo/serviço é necessário para interpretar os arquivos e pastas armazenados na nuvem. A abordagem ideal e mais flexível para armazenar dados do servidor de arquivos de uso geral, bem como alguns dados de aplicativos, na nuvem.

Este artigo se concentra nas etapas de migração. Se antes de migrar você gostaria de saber mais sobre o Azure File Sync, recomendamos os seguintes artigos:

* [Sincronização de arquivos do Azure - visão geral](https://aka.ms/AFS "Visão geral")
* [Azure File Sync - guia de implantação](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Metas de migração

O objetivo é garantir a integridade dos dados de produção, bem como garantir a disponibilidade. Este último requer manter o tempo de inatividade ao mínimo, para que ele possa caber ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Caminho de migração storSimple 1200 para Azure File Sync

Um servidor windows local é necessário para executar um agente Azure File Sync. O Windows Server pode ser no mínimo um servidor 2012R2, mas o ideal é um Windows Server 2019.

Existem inúmeros caminhos de migração alternativos e isso criaria muito tempo de um artigo para documentar todos eles e ilustrar por que eles suportam riscos ou desvantagens sobre a rota que recomendamos como uma das melhores práticas neste artigo.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Visão geral da rota de migração das etapas abaixo neste artigo.":::

A imagem anterior mostra etapas que correspondem a seções neste artigo.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Passo 1: Provisione seu Windows Server e armazenamento no local

1. Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de fail-over do Windows Server também é suportado.
2. Provisão ou adicionar armazenamento anexado direto (DAS em comparação com nas, que não é suportado). O tamanho do armazenamento do Windows Server deve ser igual ou maior do que o tamanho da capacidade disponível do seu aparelho Virtual StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Passo 2: Configure o armazenamento do Windows Server

Nesta etapa, você mapeia sua estrutura de armazenamento StorSimple (volumes e compartilhamentos) para sua estrutura de armazenamento do Windows Server.
Se você planeja fazer alterações na sua estrutura de armazenamento, o que significa o número de volumes, a associação de pastas de dados a volumes ou a estrutura de subpastas acima ou abaixo das ações atuais do SMB/NFS, então agora é a hora de levar essas alterações em consideração.
Alterar a estrutura de arquivos e pastas depois que o Azure File Sync estiver configurado, é complicado e deve ser evitado.
Este artigo pressupõe que você está mapeando 1:1, então você deve levar suas alterações de mapeamento em consideração ao seguir as etapas deste artigo.

* Nenhum dos seus dados de produção deve acabar no volume do sistema do Windows Server. O hierarquidamento em nuvem não é suportado em volumes de sistema. No entanto, esse recurso é necessário para a migração, bem como operações contínuas como uma substituição StorSimple.
* Provisionar o mesmo número de volumes em seu Windows Server como você tem em seu dispositivo virtual StorSimple 1200.
* Configure quaisquer funções, recursos e configurações do Windows Server que você precise. Recomendamos que você opte pelas atualizações do Windows Server para manter seu sistema operacional seguro e atualizado. Da mesma forma, recomendamos optar pelo Microsoft Update para manter os aplicativos da Microsoft atualizados, incluindo o agente Azure File Sync.
* Não configure nenhuma pasta ou compartilhamento antes de ler as seguintes etapas.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Passo 3: Implante o primeiro recurso de nuvem do Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Passo 4: Combine sua estrutura local de volume e pasta com os recursos de compartilhamento de arquivos Do Azure File Sync e Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Passo 5: Provision Ações de arquivos Do Zure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Passo 6: Configure as pastas-alvo do Windows Server

Em etapas anteriores, você considerou todos os aspectos que determinarão os componentes de suas topologias de sincronia. Agora é hora de preparar o servidor para receber arquivos para upload.

Crie **todas as** pastas, que sincronizarão cada uma com seu próprio compartilhamento de arquivos Azure.
É importante que você siga a estrutura da pasta que você documentou anteriormente. Se, por exemplo, você decidiu sincronizar vários compartilhamentos locais de SMB juntos em um único compartilhamento de arquivo Azure, então você precisa colocá-los uma pasta raiz comum no volume. Crie esta pasta raiz de destino no volume agora.

O número de compartilhamentos de arquivos do Azure provisionados deve corresponder ao número de pastas criadas nesta etapa + o número de volumes que você irá sincronizar no nível raiz.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Passo 7: Implante o agente de sincronização de arquivos Do Zure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Passo 8: Configurar sincronização

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Certifique-se de ativar a hierarquiçada da nuvem!** Isso é necessário se o servidor local não tiver espaço suficiente para armazenar o tamanho total de seus dados no armazenamento em nuvem StorSimple. Defina sua política de hierarquidões, temporariamente para a migração, para 99% de espaço livre de volume.

Repita as etapas de criação do grupo de sincronização e a adição da pasta de servidor correspondente como um ponto final do servidor para todos os compartilhamentos de arquivos do Azure / locais do servidor, que precisam ser configurados para sincronização.

### <a name="step-9-copy-your-files"></a>Passo 9: Copie seus arquivos

A abordagem de migração básica é um RoboCopy do seu dispositivo virtual StorSimple para o seu Windows Server e o Azure File Sync para compartilhamentos de arquivos Do Zure.

Execute a primeira cópia local para sua pasta de destino do Windows Server:

* Identifique a primeira localização no seu aparelho Virtual StorSimple.
* Identifique a pasta correspondente no Servidor Windows, que já tem o Azure File Sync configurado nele.
* Inicie a cópia usando o RoboCopy

O seguinte comando RoboCopy recordará arquivos do armazenamento StorSimple Azure para o StorSimple local e, em seguida, movê-los para a pasta de destino do Windows Server. O Windows Server irá sincronizá-lo com o compartilhamento de arquivos Do Zure. À medida que o volume local do Windows Server fica cheio, a hierarcada na nuvem entrará em ação e os arquivos de nível que já foram sincronizados com sucesso. A hierarquiagem em nuvem gerará espaço suficiente para continuar a cópia do aparelho virtual StorSimple. A hierarcada na nuvem verifica uma vez por hora para ver o que foi sincronizado e liberar espaço em disco para alcançar o espaço livre de volume de 99%.

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
      /UNILOG:<file name>
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
      Permite executar este comando RoboCopy várias vezes, sequencialmente no mesmo alvo/destino. Identifica o que foi copiado antes e omite-o. Apenas alterações, adições e " exclusões " serão*processadas,* o que ocorreu desde a última execução. Se o comando não foi executado antes, nada é omitido. Esta é uma excelente opção para locais de origem que ainda são usados e alterados ativamente.
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

Quando você executa o comando RoboCopy pela primeira vez, seus usuários e aplicativos ainda estão acessando os arquivos e pastas do StorSimple e potencialmente o alteram. É possível que o RoboCopy tenha processado um diretório, passa para o próximo e, em seguida, um usuário no local de origem (StorSimple) adiciona, altera ou exclui um arquivo que agora não será processado nesta execução atual do RoboCopy. Isso é bom.

A primeira execução é sobre mover a maior parte dos dados de volta para o local, para o seu Windows Server e fazer backup na nuvem via Azure File Sync. Isso pode levar muito tempo, dependendo de:

* sua largura de banda de download
* a velocidade de recall do serviço de nuvem StorSimple
* a largura de banda de upload
* o número de itens (arquivos e pastas), que precisam ser processados por qualquer serviço

Uma vez que a execução inicial esteja concluída, execute o comando novamente.

A segunda vez ele vai terminar mais rápido, porque ele só precisa transportar mudanças que aconteceram desde a última corrida. Essas mudanças provavelmente já são locais para o StorSimple, porque são recentes. Isso está reduzindo ainda mais o tempo porque a necessidade de recall da nuvem é reduzida. Durante esta segunda corrida, ainda assim, novas mudanças podem se acumular.

Repita este processo até que você esteja convencido de que o tempo necessário para ser concluído é um tempo de inatividade aceitável.

Quando você considerar o tempo de inatividade aceitável e estiver preparado para tirar o local do StorSimple off-line, faça-o agora: Por exemplo, remova o compartilhamento de SMB para que nenhum usuário possa acessar a pasta ou tomar qualquer outra etapa apropriada que impeça que o conteúdo mude neste pasta no StorSimple.

Execute uma última rodada robocopy. Isso vai pegar qualquer mudança, que pode ter sido perdida.
Quanto tempo esse passo final leva, depende da velocidade da varredura robocopy. Você pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo quanto tempo a corrida anterior levou.

Crie um compartilhamento na pasta do Windows Server e, possivelmente, ajuste sua implantação do DFS-N para apontar para ela. Certifique-se de definir as mesmas permissões de nível de compartilhamento que no seu compartilhamento StorSimple SMB.

Você terminou de migrar uma ação /grupo de ações para uma raiz ou volume comum. (Dependendo do que você mapeou e decidiu que precisava entrar no mesmo compartilhamento de arquivos do Azure.)

Você pode tentar executar algumas dessas cópias em paralelo. Recomendamos processar o escopo de um compartilhamento de arquivos Azure por vez.

> [!WARNING]
> Uma vez que você tenha movido todos os dados de você StorSimple para o Windows Server, e sua migração estiver completa: Retorne a ***todos os*** grupos de sincronização no portal Azure e ajuste o valor percentual de porcentagem de espaço livre de volume de hierarquamento da nuvem para algo mais adequado para a utilização do cache, digamos 20%. 

A política de espaço livre de hierarquiagem em nuvem atua em um nível de volume com potencialmente vários pontos finais de servidor sincronizando a partir dele. Se você esquecer de ajustar o espaço livre em um ponto final de servidor, a sincronização continuará a aplicar a regra mais restritiva e tentar manter 99% de espaço livre em disco, fazendo com que o cache local não esteja funcionando como você poderia esperar. A menos que seu objetivo tenha apenas o namespace para um volume que só contém dados de arquivamento raramente acessados.

## <a name="troubleshoot"></a>Solução de problemas

O problema mais provável que você pode encontrar, é que o comando RoboCopy falha com *"Volume cheio"* no lado do Servidor do Windows. Se esse for o caso, então sua velocidade de download é provavelmente melhor do que a sua velocidade de upload. A hierarcada na nuvem atua uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server, que foi sincronizado.

Deixe o progresso de sincronização e a hierarquiagem da nuvem liberar espaço em disco. Você pode observar isso no File Explorer em seu Windows Server.

Quando o Windows Server tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada quebra quando você entra nessa situação e você pode seguir em frente com confiança. A inconveniência de comandar o comando novamente é a única consequência.

Você também pode encontrar outros problemas do Azure File Sync.
Por mais improvável que sejam, se isso acontecer, dê uma olhada no **guia de solução de problemas link azure File Sync**.

## <a name="relevant-links"></a>Links relevantes

Conteúdo de migração:

* [Guia de migração da série StorSimple 8000](storage-files-migration-storsimple-8000.md)

Conteúdo do Azure File Sync:

* [Visão geral do AFS](https://aka.ms/AFS)
* [Guia de implantação do AFS](storage-files-deployment-guide.md)
* [Solução de problemas da AFS](storage-sync-files-troubleshoot.md)
