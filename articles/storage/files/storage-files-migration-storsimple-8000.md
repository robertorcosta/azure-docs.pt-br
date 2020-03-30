---
title: Migração da série StorSimple 8000 para o Azure File Sync
description: Saiba como migrar um aparelho StorSimple 8100 ou 8600 para o Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7e5f70d0323aa5c502491ab99db303fde31ade83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528618"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migração do StorSimple 8100 e 8600 para o Azure File Sync

A série StorSimple 8000 é representada pelos aparelhos 8100 ou 8600 físicos, no local e seus componentes de serviço em nuvem. É possível migrar os dados de qualquer um desses aparelhos para um ambiente Azure File Sync. O Azure File Sync é o serviço Azure padrão e estratégico de longo prazo para o que os aparelhos StorSimple podem ser migrados.

A série StorSimple 8000 chegará ao fim [da vida útil](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) em dezembro de 2022. É importante começar a planejar sua migração o mais rápido possível. Este artigo fornece as etapas necessárias de conhecimento de fundo e migrações para uma migração bem-sucedida para o Azure File Sync. 

## <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure

> [!IMPORTANT]
> A Microsoft está comprometida em ajudar os clientes em sua migração. Envie AzureFilesMigration@microsoft um e-mail para um plano de migração personalizado, bem como assistência durante a migração.

O Azure File Sync é um serviço de nuvem da Microsoft, baseado em dois componentes principais:

* Sincronização de arquivos e hierarquiagem na nuvem.
* Compartilhamentos de arquivos como armazenamento nativo no Azure, que podem ser acessados em vários protocolos como SMB e file REST. Um compartilhamento de arquivos Azure é comparável a um compartilhamento de arquivos em um Servidor Windows, que você pode montar nativamente como uma unidade de rede. Ele suporta aspectos importantes de fidelidade de arquivos, como atributos, permissões e carimbos de tempo. Com os compartilhamentos de arquivos do Azure, não há mais necessidade de um aplicativo ou serviço para interpretar os arquivos e pastas armazenados na nuvem. Você pode acessá-los nativamente sobre protocolos familiares e clientes como o Windows File Explorer. Isso faz com que o arquivo Azure compartilhe a abordagem ideal e mais flexível para armazenar dados de servidor de arquivos de uso geral, bem como alguns dados de aplicativos, na nuvem.

Este artigo se concentra nas etapas de migração. Se antes de migrar você gostaria de saber mais sobre o Azure File Sync, recomendamos os seguintes artigos:

* [Sincronização de arquivos do Azure - visão geral](https://aka.ms/AFS "Visão geral")
* [Azure File Sync - guia de implantação](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Metas de migração

O objetivo é garantir a integridade dos dados de produção, bem como garantir a disponibilidade. Este último requer manter o tempo de inatividade ao mínimo, para que ele possa caber ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Caminho de migração da série StorSimple 8000 para o Azure File Sync

Um servidor windows local é necessário para executar um agente Azure File Sync. O Windows Server pode ser no mínimo um servidor 2012R2, mas o ideal é um Windows Server 2019.

Existem inúmeros caminhos de migração alternativos e isso criaria muito tempo de um artigo para documentar todos eles e ilustrar por que eles suportam riscos ou desvantagens sobre a rota que recomendamos como uma das melhores práticas neste artigo.

![Visão geral das fases de migração da série StorSimple 8000](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Visão geral da rota de migração da série StorSimple 8000 das fases mais abaixo neste artigo.")

A imagem anterior retrata fases que correspondem a seções deste artigo.
Usamos uma migração no lado da nuvem para evitar o recall desnecessário de arquivos para o seu aparelho StorSimple local. Essa abordagem evita afetar o comportamento local de cache ou o uso da largura de banda da rede, o que pode afetar suas cargas de trabalho de produção.
Uma migração do lado da nuvem está operando em um snapshot (um clone de volume) de seus dados. Assim, seus dados de produção são isolados deste processo - até o corte no final da migração. Trabalhar fora do que é essencialmente um backup, torna a migração segura e facilmente repetível, caso você se deem com quaisquer dificuldades.

## <a name="considerations-around-existing-storsimple-backups"></a>Considerações em torno dos backups StorSimple existentes

O StorSimple permite que você faça backups na forma de clones de volume. Este artigo usa um novo clone de volume para migrar seus arquivos ao vivo.
Se você precisa migrar backups além de seus dados ao vivo, então todas as orientações neste artigo ainda se aplicam. A única diferença é que, em vez de começar com um novo clone de volume, você começará com o clone de volume de backup mais antigo que você precisa migrar.

Esta é a sequência:

* Determine o conjunto mínimo de clones de volume que você deve migrar. Recomendamos manter esta lista ao mínimo, se possível, porque quanto mais backups você migrar, mais tempo o processo de migração global levará.
* Ao passar pelo processo de migração, comece com o clone de volume mais antigo que você pretende migrar e, em cada migração subseqüente, use o próximo mais antigo.
* Quando cada migração de clone de volume for concluída, você deve tirar um instantâneo de compartilhamento de arquivos do Azure. [Os instantâneos de compartilhamento de arquivos do Azure](storage-snapshots-files.md) são como você mantém backups point-in-time dos arquivos e da estrutura de pastas para os compartilhamentos de arquivos do Azure. Você precisará desses instantâneos após a migração ser concluída, para garantir que você tenha versões preservadas de cada um de seus clones de volume à medida que você progride através da migração.
* Certifique-se de tirar instantâneos de compartilhamento de arquivos do Azure para todas as partes de arquivo do Azure, que são servidas pelo mesmo volume StorSimple. Os clones de volume estão no nível de volume, os instantâneos de compartilhamento de arquivos do Azure estão no nível de compartilhamento. Você deve tirar um instantâneo de compartilhamento (em cada compartilhamento de arquivo do Azure) depois que a migração de um clone de volume for concluída.
* Repita o processo de migração de um clone de volume e tire instantâneos de compartilhamento após cada clone de volume até que você seja pego em um instantâneo dos dados ao vivo. O processo de migração de um clone de volume é descrito nas fases abaixo. 

Se você não precisar mover backups e puder iniciar uma nova cadeia de backups no lado de compartilhamento de arquivos do Azure após a migração de apenas os dados ao vivo, então isso é benéfico para reduzir a complexidade na migração e o tempo que a migração levará. Você pode tomar a decisão de mover ou não backups e quantos para cada volume (não cada ação) que você tem no StorSimple.

## <a name="phase-1-get-ready"></a>Fase 1: Prepare-se

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem anterior, visão geral que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        A base para a migração é um clone de volume e um aparelho de nuvem virtual, chamado StorSimple 8020.
Esta fase se concentra na implantação desses recursos no Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Implantar um aparelho virtual StorSimple 8020

Implantar um aparelho em nuvem é um processo que requer segurança, rede e algumas outras considerações.

> [!IMPORTANT]
> O guia a seguir contém algumas seções desnecessárias. Leia e siga o artigo desde o início até o "Passo 3". Então volte para este artigo. Você não precisa completar o "Passo 3" ou qualquer coisa além dele nesse guia, neste momento.

[Implantação de um aparelho virtual StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Determine um clone de volume para usar

Quando você estiver pronto para iniciar a migração, o primeiro passo é pegar um novo clone de volume - assim como você faria para backup - que captura o estado atual do seu armazenamento em nuvem StorSimple. Pegue um clone para cada um dos volumes StorSimple que você tem.
Se você estiver precisando de backups móveis, então o primeiro clone de volume que você usa não é um clone recém-criado, mas o clone de volume mais antigo (backup mais antigo) que você precisa migrar.
Consulte a seção ["Considerações em torno de backups StorSimple existentes"](#considerations-around-existing-storsimple-backups) para obter orientações detalhadas.

> [!IMPORTANT]
> O guia a seguir contém algumas seções desnecessárias. Leia e siga apenas as etapas descritas na seção vinculada. Então volte para este artigo. Você não precisa seguir a seção "Próximos passos".

[Criar clone de um volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Use o clone de volume

A última fase da fase 1 é fazer o clone de volume que você escolheu, disponível no aparelho virtual 8020 no Azure.

> [!IMPORTANT]
> O guia a seguir contém as etapas necessárias, mas também - no final - uma instrução para formatar o volume. **NÃO FORME O VOLUME** Leia e siga o vinculado à "seção 7" desde o início até a instrução: "10. Para formatar um volume simples, ..."  Pare antes de seguir este passo e retorne a este artigo.

[Monte um clone de volume no aparelho virtual 8020 no Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Resumo da Fase 1

Agora que você completou a fase 1, você terá feito o seguinte:

* Implantei um aparelho virtual StorSimple 8020 no Azure.
* Determinou com qual clone de volume você começará a migração.
* Montei seus clones de volume (um para cada volume vivo) no aparelho virtual StorSimple no Azure, com seus dados disponíveis para uso posterior.

## <a name="phase-2-cloud-vm"></a>Fase 2: Cloud VM

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem anterior, visão geral que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Depois que seu clone inicial estiver disponível no aparelho virtual StorSimple 8020 no Azure, agora é hora de provisionar uma VM e expor o clone de volume (ou múltiplo) a essa VM através do iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Implantar uma VM Azure

A máquina virtual do Windows Server no Azure é como o StorSimple 8020, uma peça temporária de infra-estrutura que só é necessária durante a migração.
A configuração da VM que você implanta depende principalmente do número de itens (arquivos e pastas) que você estará sincronizando. Recomendamos ir com uma configuração de desempenho mais alta se você tiver alguma preocupação.

Um único Servidor Windows pode sincronizar até 30 compartilhamentos de arquivos Do Zure.
As especificações que você decide precisam abranger cada compartilhamento/caminho ou a raiz do volume StorSimple e contar os itens (arquivos e pastas).

O tamanho geral dos dados é menos um gargalo - é o número de itens que você precisa para adaptar as especificações da máquina.

* [Aprenda a dimensionar um Servidor Windows com base no número de itens (arquivos e pastas) que você precisa sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

    **Por favor, note:** O artigo anteriormente vinculado apresenta uma tabela com um intervalo para memória do servidor (RAM). Oriente para o grande número para a VM Azure. Você pode orientar para o número menor para a sua máquina no local.

* [Aprenda a implantar uma VM Windows Sever.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Certifique-se de que a VM está implantada na mesma região do Azure que o aparelho virtual StorSimple 8020. Se, como parte dessa migração, você também precisar alterar a região de seus dados de nuvem da região em que está armazenado hoje, você pode fazer isso em uma etapa posterior, quando você prover compartilhamentos de arquivos do Azure.

> [!IMPORTANT]
> Para otimizar o desempenho, implante um **disco de SISTEMA OPERACIONAL muito rápido** para sua VM na nuvem. Você armazenará o banco de dados de sincronização no disco do SO para todos os volumes de dados. Além disso, certifique-se de criar um **grande disco do Sistema Operacional**. Dependendo do número de itens (arquivos e pastas) em seus volumes StorSimple, o disco do SISTEMA OPERACIONAL pode precisar de **várias centenas** de GiB de espaço para acomodar o banco de dados de sincronização.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Exponha os volumes StorSimple 8020 à VM Do Zure

Nesta fase, você está conectando um ou vários volumes StorSimple do aparelho virtual 8020 sobre iSCSI ao VM do Windows Server que você provisionou.

> [!IMPORTANT]
> Para os artigos a seguir, complete apenas o **IP privado get para o aparelho de nuvem** e Conecte-se sobre as seções **iSCSI** e retorne a este artigo.

1. [Obter IP privado para o dispositivo de nuvem](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Conecte-se pelo iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Resumo da Fase 2

Agora que você completou a fase 2, você tem: 

* Provisionado um Windows Server VM na mesma região que o dispositivo StorSimple virtual 8020
* Expôs todos os volumes aplicáveis do 8020 ao Windows Server VM sobre o iSCSI.
* Agora você deve ver o conteúdo do arquivo e da pasta, quando você usar o File Explorer no VM do servidor nos volumes montados.

Só prossiga para a fase 3 quando você tiver concluído essas etapas para todos os volumes que precisam de migração.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fase 3: Configure as partes de arquivos do Azure e prepare-se para o Azure File Sync

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem anterior, visão geral que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        Nesta fase, você estará determinando e provisionando vários compartilhamentos de arquivos do Azure, criando um Windows Server no local como uma substituição de dispositivo StorSimple e configurando esse servidor para o Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapeie seus namespaces existentes para compartilhamentos de arquivos do Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Implantar compartilhamentos de arquivos Do Zure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Se você precisar alterar a região do Azure da região atual em que os dados do StorSimple residem, então provamos os compartilhamentos de arquivos do Azure na nova região que você deseja usar. Você determina a região selecionando-a ao provisionar as contas de armazenamento que detêm suas partes de arquivo Do Zure. Certifique-se de que também o recurso Azure File Sync que você irá disponibilizar abaixo, está nessa mesma, nova região.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Implantar o recurso de nuvem Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se você precisar alterar a região do Azure da região atual em que os dados do StorSimple residem, então você provisionou as contas de armazenamento para os compartilhamentos de arquivos do Azure na nova região. Certifique-se de que você selecionou essa mesma região ao implantar este Serviço de Sincronização de Armazenamento.

### <a name="deploy-an-on-premises-windows-server"></a>Implantar um Servidor Windows no local

* Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de fail-over do Windows Server também é suportado. Não reutilize o servidor que você pode ter à frente do StorSimple 8100 ou 8600.
* Provisão ou adicionar armazenamento anexado direto (DAS em comparação com nas, que não é suportado).

É melhor praticar dar ao seu novo Windows Server uma quantidade igual ou maior de armazenamento do que o seu aparelho StorSimple 8100 ou 8600 tem disponível localmente para cache. Você usará o Windows Server da mesma forma que usou o aparelho StorSimple, se ele tiver a mesma quantidade de armazenamento que o aparelho, então a experiência de cache deve ser semelhante, se não a mesma.
Você pode adicionar ou remover o armazenamento do seu Servidor Windows à vontade. Isso permite que você dimensione o tamanho do volume local e a quantidade de armazenamento local disponível para cache.

### <a name="prepare-the-windows-server-for-file-sync"></a>Prepare o Windows Server para sincronização de arquivos

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configure o Azure File Sync no Servidor Windows

Seu Windows Server registrado no local deve estar pronto e conectado à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Certifique-se de ativar a hierarquiçada da nuvem!** Hierarquica na nuvem é o recurso AFS que permite que o servidor local tenha menos capacidade de armazenamento do que é armazenado na nuvem, mas ainda tenha o namespace completo disponível. Dados localmente interessantes também são armazenados em cache local para um desempenho de acesso rápido e local. Outra razão para ativar o hierarquidamento da nuvem nesta etapa é que não queremos sincronizar o conteúdo do arquivo nesta fase, apenas o namespace deve estar se movendo neste momento.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fase 4: Configure a VM Azure para sincronização

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem anterior, visão geral que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Esta fase diz respeito ao seu Azure VM com o iSCSI montado, clone s(s) de primeiro volume. Durante esta fase, você terá o VM conectado via Azure File Sync e iniciará uma primeira rodada de mover arquivos a partir de seu clone de volume StorSimple( s).
        
    :::column-end:::
:::row-end:::

Você já configurou seu servidor local que substituirá seu aparelho StorSimple 8100 ou 8600, para o Azure File Sync. 

Configurar o Azure VM é um processo quase idêntico, com uma etapa adicional. As seguintes etapas irão guiá-lo durante o processo.

> [!IMPORTANT]
> É importante que a VM Do Azure não esteja **configurada com hierarquidade na nuvem ativada!** Você trocará o volume deste servidor com clones de volume mais novos durante toda a migração. A hierarquiagem na nuvem não tem nenhum benefício e sobrecarga no uso da CPU que você deve evitar.

1. [Implante o agente AFS. (veja a seção anterior)](#prepare-the-windows-server-for-file-sync)
2. [Preparando o VM para o Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Configurar sincronização](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Prepare a VM para o Azure File Sync

O Azure File Sync é usado para mover os arquivos dos volumes do iSCSI StorSimples montados para os compartilhamentos de arquivos Azure de destino.
Durante este processo de migração, você montará vários clones de volume em sua VM, a mesma letra de unidade. O Azure File Sync deve ser configurado para ver o próximo clone de volume que você montou como uma versão mais recente dos arquivos e pastas e atualizar os compartilhamentos de arquivos do Azure conectados via Azure File Sync. 

> [!IMPORTANT]
> Para que isso funcione, uma chave de registro deve ser definida no servidor antes que o Azure File Sync seja configurado.

1. Crie um novo diretório na unidade de sistema da VM. As informações do Azure File Sync precisarão ser persistidas lá em vez de nos clones de volume montados. Por exemplo: `"C:\syncmetadata"`
2. Abra regedit e localize a seguinte colmeia de registro:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Criar uma nova chave de string de tipo, chamada: ***MetadataRootPath***
4. Defina o caminho completo para o diretório criado no volume do sistema, por exemplo:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Configure o Azure File Sync na VM do Azure

Esta etapa é semelhante à seção anterior, que discute como você configura OFS no servidor local.

A diferença é que você não deve habilitar hierarquidões em nuvem neste servidor e que você precisa ter certeza de que as pastas certas estão conectadas aos compartilhamentos de arquivos Azure certos. Caso contrário, a nomeação de compartilhamentos de arquivos do Azure e o conteúdo de dados não corresponderão e não há como renomear os recursos da nuvem ou pastas locais sem reconfigurar a sincronização.

Consulte a [seção anterior sobre como configurar o Azure File Sync em um servidor windows](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Resumo do passo 4

Neste ponto, você terá configurado com sucesso o Azure File Sync no Azure VM, você montou seus clones de volume StorSimple via iSCSI.
Os dados estão agora fluindo da VM do Azure para os vários compartilhamentos de arquivos do Azure e, a partir daí, um namespace totalmente cansado aparece no seu Windows Server no local.

> [!IMPORTANT]
> Certifique-se de que não há alterações feitas ou acesso do usuário concedidos ao Windows Server no momento.

Os dados iniciais de clone de volume que se deslocam através da VM do Azure para os compartilhamentos de arquivos do Azure podem levar um longo tempo, potencialmente semanas. Estimar o tempo que isso vai levar é complicado e depende de muitos fatores. Mais notavelmente a velocidade com que o Azure VM pode acessar arquivos nos volumes StorSimple e a rapidez com que o Azure File Sync pode processar os arquivos e pastas que precisam de sincronização. 

Por experiência, podemos supor que a largura de banda - portanto, o tamanho real dos dados - desempenha um papel subordinado. O tempo que esta ou qualquer rodada de migração subseqüente levará depende principalmente do número de itens que podem ser processados por segundo. Assim, por exemplo, 1 TiB com 100.000 arquivos e pastas provavelmente terminará mais lento do que 1 TiB com apenas 50.000 arquivos e pastas.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fase 5: Iterado através de vários clones de volume

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem anterior, visão geral que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Como discutido na fase anterior, a sincronização inicial pode levar muito tempo. Seus usuários e aplicativos ainda estão acessando o aparelho StorSimple 8100 ou 8600 no local. Isso significa que as mudanças estão se acumulando, e a cada dia um delta maior entre os dados ao vivo e o clone de volume inicial, você está migrando, formas. Nesta seção, você aprenderá como minimizar o tempo de inatividade usando vários clones de volume e dizendo quando a sincronização é feita.
    :::column-end:::
:::row-end:::

Infelizmente, o processo de migração não é instantâneo. Isso significa que o delta acima mencionado para os dados ao vivo é uma conseqüência inevitável. A boa notícia é que você pode repetir o processo de montagem de novos clones de volume. O delta de cada clone de volume será progressivamente menor. Assim, eventualmente, uma sincronização terminará em um período de tempo que você considerar aceitável para levar usuários e aplicativos off-line para cortar em seu servidor Windows no local.

Repita as seguintes etapas até que a sincronização seja concluída em uma duração rápida o suficiente para que você se sinta confortável em desligar usuários e aplicativos:

1. [Determine que a sincronização está completa para um determinado clone de volume.](#determine-when-sync-is-done)
2. [Pegue um novo clone de volume e monte-o no aparelho virtual 8020.](#the-next-volume-clones)
3. [Determine quando a sincronização é feita.](#determine-when-sync-is-done)
4. [Estratégia de corte](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>O próximo clone de volume(s)

Discutimos pegar um clone de volume no início deste artigo.
Esta fase tem duas ações:

1. [Pegue um clone de volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Monte esse clone de volume (veja acima)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Determine quando a sincronização é feita

Quando a sincronização é feita, você pode interromper a medição de tempo e determinar se você precisa repetir o processo de pegar um clone de volume e montá-lo ou se a sincronização de tempo tomada com o último clone de volume foi suficientemente pequena.

Para determinar a sincronização está concluída:

1. Abra o Visualizador de Eventos e navegue **para aplicativos e serviços**
2. Navegar e abrir **microsoft\fileSync\Agent\Telemetria**
3. Procure o evento mais recente **9102**, que corresponde a uma sessão de sincronização concluída
4. Selecione **Detalhes** e confirme se o valor do **SyncDirection** é **upload**
5. Verifique o **HResult** e confirme se ele mostra **0**. Isso significa que a sessão de sincronização foi bem sucedida. Se HResult é um valor não-zero, então houve um erro durante a sincronização. Se o **PerItemErrorCount** for maior que 0, então alguns arquivos ou pastas não sincronizaram corretamente. É possível ter um HResult de 0, mas um PerItemErrorCount que seja maior que 0. Neste ponto, você não precisa se preocupar com o PerItemErrorCount. Pegaremos esses arquivos mais tarde. Se essa contagem de erros for significativa, milhares de itens entrem em contato com o suporte ao cliente e peçam para serem conectados ao grupo de produtos Azure File Sync para obter orientação direta sobre as melhores e próximas fases.
6. Verifique se há vários eventos 9102 com HResult 0 em sequência. Isso indica que a sincronização está completa para este clone de volume.

### <a name="cut-over-strategy"></a>Estratégia de corte

1. Determine se a sincronização de um clone de volume é rápida o suficiente agora. (Delta pequeno o suficiente.)
2. Leve o aparelho StorSimple off-line.
3. Um robocopy final.

Meça o tempo e determine se a sincronização de um clone de volume recente pode terminar dentro de uma janela de tempo pequena o suficiente, que você pode pagar como tempo de inatividade em seu sistema.

Agora é hora de desativar o acesso do usuário ao aparelho StorSimple. Sem mais mudanças. O tempo de inatividade começou.
Você precisa deixar o aparelho on-line e conectado, mas agora deve evitar alterações nele.

Na fase 6 você alcançará qualquer delta nos dados ao vivo desde o último clone de volume.

## <a name="phase-6-a-final-robocopy"></a>Fase 6: Uma RoboCopy final

Neste ponto, há duas diferenças entre o seu Windows Server no local e o aparelho StorSimple 8100 ou 8600:

1. Pode haver arquivos que não foram sincronizados (consulte **PerItemErrors** no registro de eventos acima)
2. O aparelho StorSimple tem um cache preenchido contra o Windows Server apenas um namespace sem conteúdo de arquivo armazenado localmente neste momento.

![Uma imagem ilustrando uma parte da imagem anterior, visão geral que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Podemos trazer o cache do Windows Server até o estado do aparelho e garantir que nenhum arquivo seja deixado para trás com um RoboCopy final.

> [!CAUTION]
> É imperativo que o comando RoboCopy que você segue, seja exatamente como descrito abaixo. Só queremos copiar arquivos locais e arquivos que não tenham passado pela abordagem clone+sincronização de volume antes. Podemos resolver os problemas por que eles não sincronizaram mais tarde, depois que a migração estiver concluída. (Consulte [Azure File Sync solução de problemas](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). É provavelmente caracteres impublicáveis em nomes de arquivos que você não perderá quando eles forem excluídos.)

Comando RoboCopy:

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
      Permite que o RoboCopy considere apenas deltas entre o aparelho StorSimple (storsimple) e o target (diretório do Windows Server).
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

Você deve executar este comando RoboCopy para cada um dos diretórios no Windows Server como um alvo, que você configurou com sincronização de arquivo para um arquivo Azure.

Você pode executar vários desses comandos em paralelo.
Uma vez que esta etapa do RoboCopy esteja completa, você pode permitir que seus usuários e aplicativos acessem o Windows Server como fizeram com o aparelho StorSimple antes.

Consulte os arquivos de registro de robocopia para ver se os arquivos foram deixados para trás. Se os problemas existirem, na maioria dos casos você pode resolvê-los depois que a migração estiver concluída e seus usuários e aplicativos forem recolocados no seu Windows Server. Se você precisar corrigir quaisquer problemas, faça-o antes da fase 7.

É provável que seja necessário criar os compartilhamentos de SMB no Windows Server que você tinha nos dados do StorSimple antes. Você pode carregar a frente desta etapa e fazê-lo mais cedo para não perder tempo aqui, mas você deve garantir que antes deste ponto, não ocorram alterações nos arquivos no servidor windows.

Se você tiver uma implantação DFS-N, você pode apontar os espaços de nome DFN para os novos locais da pasta do servidor. Se você não tiver uma implantação DFS-N e você tiver o seu aparelho 8100 8600 localmente com um Servidor Windows, você pode tirar esse servidor do domínio e o domínio juntar seu novo Servidor Windows com AFS para o domínio, dar-lhe o mesmo nome de servidor que o servidor antigo e os mesmos nomes de compartilhamento, em seguida, o corte para o novo servidor permanece transparente para seus usuários, diretiva de grupo ou scripts.

## <a name="phase-7-deprovision"></a>Fase 7: Deprovision

Durante a última fase, você itera através de vários clones de volume, e eventualmente foi capaz de cortar o acesso do usuário ao novo Windows Server depois de levá-lo storSimple dispositivo off-line.

Agora você pode começar a desprodesder recursos desnecessários.
Antes de começar, é uma prática recomendada observar sua nova implantação do Azure File Sync na produção, por um tempo. Isso lhe dá opções para corrigir quaisquer problemas que você possa encontrar.

Uma vez que você esteja satisfeito e tenha observado sua implantação de AFS por pelo menos alguns dias, você pode começar a deprovisionar recursos nesta ordem:

1. Desligue a VM do Azure que usamos para mover dados dos clones de volume para os compartilhamentos de arquivos do Azure via sincronização de arquivos.
2. Vá para o recurso Storage Sync Service no Azure e não registre a VM do Azure. Isso remove-o de todos os grupos de sincronização.

    > [!WARNING]
    > **Certifique-se de escolher a máquina certa.** Você desligou o VM da nuvem, o que significa que ele deve aparecer como o único servidor offline na lista de servidores registrados. Você não deve escolher o Windows Server no local nesta etapa, fazendo isso irá desregistrá-lo.

3. Exclua o Azure VM e seus recursos.
4. Desabilite o aparelho Virtual StorSimple 8020.
5. Deprovisionar todos os recursos do StorSimple no Azure.
6. Desconecte o aparelho físico StorSimple do seu data center.

Sua migração está completa.

## <a name="next-steps"></a>Próximas etapas

Familiarize-se mais com o Azure File Sync. Especialmente com a flexibilidade das políticas de hierarquididade em nuvem.

Se você ver no portal Azure, ou nos eventos anteriores, que alguns arquivos não estão sincronizando permanentemente, revise o guia de solução de problemas para obter etapas para resolver esses problemas.

* [Visão geral do Azure File Sync: aka.ms/AFS](https://aka.ms/AFS)
* [Hierarque de nuvem](storage-sync-cloud-tiering.md) 
* [Guia de solução de problemas do Azure File Sync](storage-sync-files-troubleshoot.md)
