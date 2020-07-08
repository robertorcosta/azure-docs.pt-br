---
title: Migração da série StorSimple 8000 para Sincronização de Arquivos do Azure
description: Saiba como migrar um dispositivo StorSimple 8100 ou 8600 para Sincronização de Arquivos do Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6ad132513c2ec61dd5a290da1a88e50f0ad6eb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510356"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migração do StorSimple 8100 e 8600 para Sincronização de Arquivos do Azure

A série StorSimple 8000 é representada pelo 8100 ou pelo 8600 dispositivos físicos, locais e seus componentes de serviço de nuvem. É possível migrar os dados de qualquer um desses dispositivos para um ambiente Sincronização de Arquivos do Azure. Sincronização de Arquivos do Azure é o serviço do Azure de longo prazo padrão e estratégico para o qual os dispositivos StorSimple podem ser migrados.

A série StorSimple 8000 atingirá o [fim da vida útil](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) em 2022 de dezembro. É importante começar a planejar sua migração assim que possível. Este artigo fornece as etapas de conhecimento e migrações em segundo plano necessárias para uma migração bem-sucedida para o Sincronização de Arquivos do Azure. 

## <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure

> [!IMPORTANT]
> A Microsoft está comprometida em auxiliar os clientes em sua migração. Email AzureFilesMigration@microsoft . com para um plano de migração personalizado, bem como assistência durante a migração.

Sincronização de Arquivos do Azure é um serviço de nuvem da Microsoft, com base em dois componentes principais:

* Sincronização de arquivos e camadas de nuvem.
* Compartilhamentos de arquivos como armazenamento nativo no Azure, que podem ser acessados por meio de vários protocolos, como SMB e REST de arquivo. Um compartilhamento de arquivos do Azure é comparável a um compartilhamento de arquivos em um Windows Server, que você pode montar nativamente como uma unidade de rede. Ele dá suporte a aspectos de fidelidade de arquivo importantes, como atributos, permissões e carimbos de data/hora. Com compartilhamentos de arquivos do Azure, não há mais necessidade de um aplicativo ou serviço para interpretar os arquivos e as pastas armazenados na nuvem. Você pode acessá-los nativamente por meio de protocolos e clientes familiares, como o explorador de arquivos do Windows. Isso faz com que o arquivo do Azure compartilhe a abordagem ideal e mais flexível para armazenar dados de servidor de arquivos de uso geral, bem como alguns dados de aplicativo, na nuvem.

Este artigo se concentra nas etapas de migração. Se antes de migrar você gostaria de saber mais sobre Sincronização de Arquivos do Azure, recomendamos os seguintes artigos:

* [Sincronização de Arquivos do Azure-visão geral](https://aka.ms/AFS "Visão geral")
* [Sincronização de Arquivos do Azure-guia de implantação](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Metas de migração

O objetivo é garantir a integridade dos dados de produção, bem como garantir a disponibilidade. O segundo requer um mínimo de tempo de inatividade, para que ele possa se ajustar ou ter apenas um pouco mais de uma janela de manutenção regular.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Caminho de migração da série StorSimple 8000 para Sincronização de Arquivos do Azure

Um servidor Windows local é necessário para executar um agente de Sincronização de Arquivos do Azure. O Windows Server pode ser, no mínimo, um servidor 2012R2, mas o ideal é o Windows Server 2019.

Há inúmeros caminhos de migração alternativos e criaria um artigo muito longo para documentar todos eles e ilustrar por que eles têm risco ou desvantagens na rota que recomendamos como prática recomendada neste artigo.

![Visão geral das fases de migração da série StorSimple 8000](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Visão geral da rota de migração da série StorSimple 8000 das fases abaixo neste artigo.")

A imagem anterior descreve as fases que correspondem às seções neste artigo.
Usamos uma migração no lado da nuvem para evitar a RECALL desnecessária de arquivos para seu dispositivo StorSimple local. Essa abordagem evita o impacto no comportamento do cache local ou na utilização da largura de banda da rede, ou seja, pode afetar suas cargas de trabalho de produção.
Uma migração no lado da nuvem está operando em um instantâneo (um clone de volume) de seus dados. Portanto, seus dados de produção são isolados desse processo-até o fim da migração. Ao trabalhar com o que é essencialmente um backup, o torna a migração segura e facilmente reproduzível, caso você tenha dificuldades.

## <a name="considerations-around-existing-storsimple-backups"></a>Considerações sobre backups do StorSimple existentes

O StorSimple permite que você faça backups na forma de clones de volume. Este artigo usa um novo clone de volume para migrar seus arquivos dinâmicos.
Se você precisar migrar backups além de seus dados dinâmicos, todas as diretrizes neste artigo ainda se aplicarão. A única diferença é que, em vez de começar com um novo clone de volume, você começará com o clone de volume de backup mais antigo que precisará migrar.

Esta é a sequência:

* Determine o conjunto mínimo de clones de volume que você deve migrar. É recomendável manter essa lista com um mínimo, se possível, porque quanto mais backups você migrar, mais tempo o processo de migração geral levará.
* Ao passar pelo processo de migração, comece com o clone de volume mais antigo que você pretende migrar e, em cada migração subsequente, use o próximo mais antigo.
* Quando cada migração de clone de volume for concluída, você deverá usar um instantâneo de compartilhamento de arquivos do Azure. Os [instantâneos de compartilhamento de arquivos do Azure](storage-snapshots-files.md) são como você mantém backups pontuais dos arquivos e da estrutura de pastas para seus compartilhamentos de arquivos do Azure. Você precisará desses instantâneos após a conclusão da migração, para garantir que você tenha preservado as versões de cada um dos clones de volume à medida que avança pela migração.
* Certifique-se de fazer instantâneos de compartilhamento de arquivos do Azure para todos os compartilhamentos de arquivos do Azure, que são servidos pelo mesmo volume do StorSimple. Os clones de volume estão no nível de volume, os instantâneos de compartilhamento de arquivos do Azure estão no nível de compartilhamento. Você deve usar um instantâneo de compartilhamento (em cada compartilhamento de arquivos do Azure) depois que a migração de um clone de volume for concluída.
* Repita o processo de migração para um clone de volume e tirando instantâneos de compartilhamento após cada clone de volume até obter um instantâneo dos dados dinâmicos. O processo de migração de um clone de volume é descrito nas fases abaixo. 

Se você não precisar mover backups e iniciar uma nova cadeia de backups no lado do compartilhamento de arquivos do Azure depois que a migração de apenas os dados dinâmicos for feita, isso será benéfico para reduzir a complexidade na migração e o tempo que a migração levará. Você pode tomar a decisão se deseja ou não mover backups e quantos de cada volume (não cada compartilhamento) você tem no StorSimple.

## <a name="phase-1-get-ready"></a>Fase 1: preparar-se

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem de visão geral anterior que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        A base para a migração é um clone de volume e um dispositivo de nuvem virtual, chamado de StorSimple 8020.
Esta fase se concentra na implantação desses recursos no Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Implantar um dispositivo virtual StorSimple 8020

A implantação de um dispositivo de nuvem é um processo que exige segurança, rede e algumas outras considerações.

> [!IMPORTANT]
> O guia a seguir contém algumas seções desnecessárias. Leia e siga o artigo desde o início até a "etapa 3". Em seguida, retorne a este artigo. No momento, não é necessário concluir a "etapa 3" ou nada além dele neste guia.

[Implantação de um dispositivo virtual StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Determinar um clone de volume a ser usado

Quando você estiver pronto para iniciar a migração, a primeira etapa será fazer um novo clone de volume, exatamente como você faria para o backup, que captura o estado atual de seu armazenamento em nuvem do StorSimple. Faça um clone para cada um dos volumes do StorSimple que você tem.
Se você precisar mover backups, o primeiro clone de volume usado não é um clone recém-criado, mas o clone de volume mais antigo (backup mais antigo) que você precisa migrar.
Consulte a seção ["considerações sobre backups do StorSimple existentes"](#considerations-around-existing-storsimple-backups) para obter orientações detalhadas.

> [!IMPORTANT]
> O guia a seguir contém algumas seções desnecessárias. Leia e siga apenas as etapas descritas na seção vinculada a. Em seguida, retorne a este artigo. Você não precisa seguir a seção "próximas etapas".

[Criar clone de um volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Usar o clone de volume

A última fase da fase 1 é fazer o clone do volume escolhido, disponível no dispositivo virtual 8020 no Azure.

> [!IMPORTANT]
> O guia a seguir contém as etapas necessárias, mas também-no final, uma instrução para formatar o volume. Não **Formatar o volume** Leia e siga a "seção 7" vinculada a partir da instrução inicial até: "10. Para formatar um volume simples,... "  Pare antes de seguir esta etapa e retorne a este artigo.

[Montar um clone de volume no dispositivo virtual 8020 no Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Resumo da fase 1

Agora que você concluiu a fase 1, terá feito o seguinte:

* Implantou um dispositivo virtual StorSimple 8020 no Azure.
* Determinado qual clone de volume você começará a migração.
* Montados seus clones de volume (um para cada volume ativo) para o dispositivo virtual StorSimple no Azure, com seus dados disponíveis para uso posterior.

## <a name="phase-2-cloud-vm"></a>Fase 2: VM de nuvem

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem de visão geral anterior que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Depois que o clone inicial estiver disponível no dispositivo virtual StorSimple 8020 no Azure, agora é hora de provisionar uma VM e expor o clone do volume (ou vários) para essa VM por iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Implantar uma VM do Azure

A máquina virtual do Windows Server no Azure é exatamente como o StorSimple 8020, uma parte temporária da infraestrutura que é necessária apenas durante a migração.
A configuração da VM que você implanta depende principalmente do número de itens (arquivos e pastas) que você estará sincronizando. É recomendável usar uma configuração de desempenho maior se você tiver alguma preocupação.

Um único Windows Server pode sincronizar até 30 compartilhamentos de arquivos do Azure.
As especificações que você decide precisam abranger cada compartilhamento/caminho ou a raiz do volume do StorSimple e contar os itens (arquivos e pastas).

O tamanho geral dos dados é menor do que um afunilamento – é o número de itens que você precisa para personalizar as especificações do computador.

* [Saiba como dimensionar um Windows Server com base no número de itens (arquivos e pastas) que você precisa sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

    **Observação:** O artigo vinculado anteriormente apresenta uma tabela com um intervalo para a memória do servidor (RAM). Orientar em direção ao número grande para a VM do Azure. Você pode orientar em direção ao número menor para seu computador local.

* [Saiba como implantar uma VM do Windows Server.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Verifique se a VM está implantada na mesma região do Azure que o dispositivo virtual StorSimple 8020. Se como parte dessa migração, você também precisa alterar a região dos dados da nuvem da região em que ela está armazenada hoje. você pode fazer isso em uma etapa posterior, ao provisionar compartilhamentos de arquivos do Azure.

> [!IMPORTANT]
> Muitas vezes, um Windows Server local é usado para front-end do seu dispositivo StorSimple local. Nessa configuração, é possível habilitar o recurso "[eliminação de duplicação de dados](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" no Windows Server. **Se você usou a eliminação de duplicação de dados com seus dados do StorSimple, certifique-se de habilitar a eliminação de duplicação de dados nesta VM do Azure também.** Não confunda essa eliminação de duplicação em nível de arquivo com a eliminação de duplicação de nível de bloco interna StorSimples, para a qual nenhuma ação é necessária.

> [!IMPORTANT]
> Para otimizar o desempenho, implante um **disco do sistema operacional rápido** para sua VM de nuvem. Você armazenará o banco de dados de sincronização no disco do sistema operacional para todos os seus volumes de dado. Além disso, certifique-se de criar um **grande disco do sistema operacional**. Dependendo do número de itens (arquivos e pastas) em seus volumes do StorSimple, o disco do sistema operacional pode precisar de **várias centenas de Gib** de espaço para acomodar o banco de dados de sincronização.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Expor os volumes do StorSimple 8020 para a VM do Azure

Nesta fase, você está conectando um ou vários volumes do StorSimple do dispositivo virtual 8020 sobre iSCSI à VM do Windows Server que você provisionou.

> [!IMPORTANT]
> Para os artigos a seguir, conclua apenas as seções **obter IP privado para o dispositivo de nuvem** e **conectar-se por meio de iSCSI** e retorne a este artigo.

1. [Obter IP privado para o dispositivo de nuvem](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Conectar-se via iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Resumo da fase 2

Agora que você concluiu a fase 2, você tem: 

* Provisionado uma VM do Windows Server na mesma região que o dispositivo virtual StorSimple 8020
* Expor todos os volumes aplicáveis do 8020 para a VM do Windows Server sobre iSCSI.
* Agora você deve ver o conteúdo do arquivo e da pasta, ao usar o explorador de arquivos na VM do servidor nos volumes montados.

Vá para a fase 3 somente quando tiver concluído essas etapas para todos os volumes que precisam de migração.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fase 3: configurar compartilhamentos de arquivos do Azure e preparar-se para Sincronização de Arquivos do Azure

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem de visão geral anterior que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        Nesta fase, você determinará e provisionar vários compartilhamentos de arquivos do Azure, criando um Windows Server local como uma substituição do dispositivo StorSimple e configurará esse servidor para Sincronização de Arquivos do Azure. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapear seus namespaces existentes para compartilhamentos de arquivos do Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Implantar compartilhamentos de arquivos do Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Se você precisar alterar a região do Azure da região atual em que os dados do StorSimple residem, provisione os compartilhamentos de arquivos do Azure na nova região que você deseja usar. Você determina a região selecionando-a ao provisionar as contas de armazenamento que mantêm os compartilhamentos de arquivos do Azure. Certifique-se de que também o recurso de Sincronização de Arquivos do Azure que você provisionar abaixo está na mesma região, nova.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Implantar o Sincronização de Arquivos do Azure recurso de nuvem

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se você precisar alterar a região do Azure da região atual em que os dados do StorSimple residem, você provisionou as contas de armazenamento para seus compartilhamentos de arquivos do Azure na nova região. Certifique-se de ter selecionado essa mesma região ao implantar esse serviço de sincronização de armazenamento.

### <a name="deploy-an-on-premises-windows-server"></a>Implantar um Windows Server local

* Crie um Windows Server 2019 – no mínimo 2012R2-como uma máquina virtual ou um servidor físico. Também há suporte para um cluster de failover do Windows Server. Não reutilize o servidor que você pode ter ao lado do StorSimple 8100 ou 8600.
* Provisione ou adicione armazenamento de conexão direta (DAS em comparação com o NAS, para o qual não há suporte).

É recomendável dar ao seu novo Windows Server uma quantidade igual ou maior de armazenamento do que o dispositivo StorSimple 8100 ou 8600 está disponível localmente para cache. Você usará o Windows Server da mesma maneira que usou o dispositivo StorSimple, se ele tiver a mesma quantidade de armazenamento que o dispositivo, então a experiência de cache deverá ser semelhante, se não for a mesma.
Você pode adicionar ou remover o armazenamento do seu Windows Server no. Isso permite que você dimensione o tamanho do volume local e a quantidade de armazenamento local disponível para cache.

### <a name="prepare-the-windows-server-for-file-sync"></a>Preparar o Windows Server para sincronização de arquivos

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configurar Sincronização de Arquivos do Azure no Windows Server

Seu Windows Server local registrado deve estar pronto e conectado à Internet para esse processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Certifique-se de ativar a disposição em camadas de nuvem!** A camada de nuvem é o recurso AFS que permite que o servidor local tenha menos capacidade de armazenamento do que o armazenado na nuvem, mas que tenha o namespace completo disponível. Os dados interessantes localmente também são armazenados em cache localmente para um desempenho rápido e local de acesso. Outro motivo para ativar a camada de nuvem nesta etapa é que não queremos sincronizar o conteúdo do arquivo neste estágio, somente o namespace deve ser movido no momento.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fase 4: configurar a VM do Azure para sincronização

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem de visão geral anterior que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Esta fase se preocupa com a VM do Azure com os clones do primeiro volume e do iSCSI montados. Durante essa fase, você obterá a VM conectada por meio de Sincronização de Arquivos do Azure e iniciará uma primeira rodada de mover arquivos do clone (s) do volume do StorSimple.
        
    :::column-end:::
:::row-end:::

Você já configurou seu servidor local que substituirá seu dispositivo StorSimple 8100 ou 8600, por Sincronização de Arquivos do Azure. 

Configurar a VM do Azure é um processo quase idêntico, com uma etapa adicional. As etapas a seguir irão guiá-lo pelo processo.

> [!IMPORTANT]
> É importante que a VM do Azure **não esteja configurada com a camada de nuvem habilitada!** Você vai trocar o volume desse servidor com clones de volume mais recentes durante a migração. A camada de nuvem não tem nenhum benefício e sobrecarga sobre o uso da CPU que você deve evitar.

1. [Implante o agente AFS. (consulte a seção anterior)](#prepare-the-windows-server-for-file-sync)
2. [Preparando a VM para Sincronização de Arquivos do Azure.](#get-the-vm-ready-for-azure-file-sync)
3. [Configurar sincronização](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Prepare a VM para Sincronização de Arquivos do Azure

Sincronização de Arquivos do Azure é usado para mover os arquivos dos volumes iSCSI StorSimple montados para os compartilhamentos de arquivos de destino do Azure.
Durante esse processo de migração, você montará vários clones de volume em sua VM, sob a mesma letra de unidade. Sincronização de Arquivos do Azure deve ser configurado para ver o próximo clone de volume que você montou como uma versão mais recente dos arquivos e pastas e atualizar os compartilhamentos de arquivos do Azure conectados via Sincronização de Arquivos do Azure. 

> [!IMPORTANT]
> Para que isso funcione, uma chave do registro deve ser definida no servidor antes que o Sincronização de Arquivos do Azure seja configurado.

1. Crie um novo diretório na unidade do sistema da VM. Sincronização de Arquivos do Azure informações precisarão ser persistidas em vez de nos clones do volume montado. Por exemplo: `"C:\syncmetadata"`
2. Abra regedit e localize o seguinte hive do registro:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Crie uma nova chave do tipo cadeia de caracteres chamada: ***MetadataRootPath***
4. Defina o caminho completo para o diretório que você criou no volume do sistema, por exemplo:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Configurar Sincronização de Arquivos do Azure na VM do Azure

Esta etapa é semelhante à seção anterior, que discute como você configura o AFS no servidor local.

A diferença é que você não deve habilitar a camada de nuvem nesse servidor e que precisa certificar-se de que as pastas certas estejam conectadas aos compartilhamentos de arquivos do Azure corretos. Caso contrário, o seu nome de compartilhamentos de arquivos do Azure e conteúdo de dados não corresponderá e não haverá como renomear os recursos de nuvem ou as pastas locais sem reconfigurar a sincronização.

Consulte a [seção anterior sobre como configurar sincronização de arquivos do Azure em um Windows Server](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Resumo da etapa 4

Neste ponto, você terá configurado com êxito Sincronização de Arquivos do Azure na VM do Azure que montou os clones de volume do StorSimple via iSCSI.
Agora, os dados estão fluindo da VM do Azure para vários compartilhamentos de arquivos do Azure e, a partir daí, um namespace totalmente cansado aparece no Windows Server local.

> [!IMPORTANT]
> Verifique se não há nenhuma alteração feita ou acesso de usuário concedido ao Windows Server no momento.

Os dados de clone de volume inicial que se movem pela VM do Azure para os compartilhamentos de arquivos do Azure podem levar muito tempo, possivelmente semanas. Estimar o tempo que isso levará é complicado e dependerá de muitos fatores. Mais notavelmente, a velocidade na qual a VM do Azure pode acessar arquivos nos volumes do StorSimple e a rapidez com que Sincronização de Arquivos do Azure pode processar os arquivos e as pastas que precisam de sincronização. 

A partir da experiência, podemos pressupor que a largura de banda, portanto, o tamanho real dos dados, reproduz uma função subordinada. O momento em que esse ou qualquer intervalo de migração subsequente levará dependerá, principalmente, do número de itens que podem ser processados por segundo. Portanto, por exemplo 1 TiB com um 100.000 arquivos e pastas, provavelmente terminará mais devagar que 1 TiB com apenas 50.000 arquivos e pastas.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fase 5: iterar por meio de vários clones de volume

:::row:::
    :::column:::
        ![Uma imagem ilustrando uma parte da imagem de visão geral anterior que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Conforme discutido na fase anterior, a sincronização inicial pode levar muito tempo. Seus usuários e aplicativos ainda estão acessando o dispositivo StorSimple 8100 ou 8600 local. Isso significa que as alterações estão sendo acumuladas e, com todos os dias, um Delta maior entre os dados dinâmicos e o clone do volume inicial, no momento, você está migrando, formulários. Nesta seção, você aprenderá a minimizar o tempo de inatividade usando vários clones de volume e dizendo quando a sincronização é feita.
    :::column-end:::
:::row-end:::

Infelizmente, o processo de migração não é instantâneo. Isso significa que o Delta mencionado anteriormente para os dados dinâmicos é uma consequência inevitável. A boa notícia é que você pode repetir o processo de montagem de novos clones de volume. O Delta de cada clone de volume será progressivamente menor. Portanto, eventualmente, uma sincronização será concluída em uma duração de tempo que você considerar aceitável para colocar os usuários e aplicativos offline para passar para o Windows Server local.

Repita as etapas a seguir até que a sincronização seja concluída em uma duração rápida o suficiente que você sinta à vontade para colocar os usuários e aplicativos offline:

1. [Determine se a sincronização foi concluída para um determinado clone de volume.](#determine-when-sync-is-done)
2. [Faça um novo clone (s) de volume e monte-o no dispositivo virtual 8020.](#the-next-volume-clones)
3. [Determine quando a sincronização é feita.](#determine-when-sync-is-done)
4. [Estratégia de recorte](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>O próximo clone (s) de volume

Discutimos um clone (s) de volume anteriormente neste artigo.
Esta fase tem duas ações:

1. [Fazer um clone de volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Montar esse clone de volume (veja acima)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Determinar quando a sincronização está concluída

Quando a sincronização é concluída, você pode interromper sua medição de tempo e determinar se precisa repetir o processo de fazer um clone de volume e montá-lo ou se a sincronização de tempo executada com o último clone de volume era suficientemente pequena.

Para determinar se a sincronização foi concluída:

1. Abra o Visualizador de Eventos e navegue até **aplicativos e serviços**
2. Navegue e abra **Microsoft\FileSync\Agent\Telemetry**
3. Procure o **evento 9102**mais recente, que corresponde a uma sessão de sincronização concluída
4. Selecione **detalhes** e confirme se o valor de **SyncDirection** é **upload**
5. Verifique o **HRESULT** e confirme se ele mostra **0**. Isso significa que a sessão de sincronização foi bem-sucedida. Se HResult for um valor diferente de zero, ocorrerá um erro durante a sincronização. Se o **PerItemErrorCount** for maior que 0, alguns arquivos ou pastas não foram sincronizados corretamente. É possível ter um HResult de 0, mas um PerItemErrorCount que seja maior que 0. Neste ponto, você não precisa se preocupar com o PerItemErrorCount. Capturaremos esses arquivos mais tarde. Se essa contagem de erros for significativa, milhares de itens, entre em contato com o atendimento ao cliente e peça para estar conectado ao grupo de produtos Sincronização de Arquivos do Azure para obter orientação direta sobre as próximas fases.
6. Verifique para ver vários eventos 9102 com HResult 0 em uma linha. Isso indica que a sincronização foi concluída para este clone de volume.

### <a name="cut-over-strategy"></a>Estratégia de recorte

1. Determine se a sincronização de um clone de volume é rápida o suficiente agora. (Delta pequeno o suficiente.)
2. Coloque o dispositivo StorSimple offline.
3. Um RoboCopy final.

Meça o tempo e determine se a sincronização de um clone de volume recente pode ser concluída dentro de uma janela de tempo pequena o suficiente, que você pode pagar como tempo de inatividade em seu sistema.

Agora é hora de desabilitar o acesso do usuário ao dispositivo StorSimple. Não há mais alterações. O tempo de inatividade foi iniciado.
Você precisa deixar o dispositivo online e conectado, mas agora deve impedir alterações nele.

Na fase 6, você encontrará qualquer Delta nos dados dinâmicos desde o último clone de volume.

## <a name="phase-6-a-final-robocopy"></a>Fase 6: um RoboCopy final

Neste ponto, há duas diferenças entre o Windows Server local e o dispositivo StorSimple 8100 ou 8600:

1. Pode haver arquivos que não foram sincronizados (consulte **PerItemErrors** no log de eventos acima)
2. O dispositivo StorSimple tem um cache populado versus o Windows Server apenas um namespace sem conteúdo de arquivo armazenado localmente no momento.

![Uma imagem ilustrando uma parte da imagem de visão geral anterior que ajuda a concentrar esta subseção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Podemos colocar o cache do Windows Server até o estado do dispositivo e garantir que nenhum arquivo seja deixado para trás com um RoboCopy final.

> [!CAUTION]
> É imperativo que o comando RoboCopy que você seguir, é exatamente o descrito abaixo. Queremos apenas copiar os arquivos que são locais e os arquivos que não foram movidos pela abordagem de clonagem de volume + sincronização antes. Podemos resolver os problemas por que não sincronizaram mais tarde, após a conclusão da migração. (Consulte [sincronização de arquivos do Azure solução de problemas](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). É mais provável que os caracteres não imprimíveis sejam impressos em nomes de arquivos que você não perderá quando forem excluídos.)

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
      Permite que o RoboCopy seja executado em vários threads. O padrão é 8, o máximo é 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Gera o status do arquivo de LOG como UNICODE (Substitui o log existente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Saídas para a janela do console. Usado em conjunto com a saída para um arquivo de log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Executa o RoboCopy no mesmo modo que um aplicativo de backup usaria. Ele permite que o RoboCopy mova arquivos aos quais o usuário atual não tem permissões.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy considere apenas deltas entre origem (dispositivo StorSimple) e destino (diretório do Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fidelidade da cópia de arquivo (o padrão é/COPY: DAT), sinalizadores de cópia: D = dados, A = atributos, T = carimbos de data/hora, S = segurança = ACLs de NTFS, O = informações de proprietário, U = informações de auditoria
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Copie todas as informações do arquivo (equivalente a/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fidelidade para a cópia de diretórios (o padrão é/DCOPY: DA), sinalizadores de cópia: D = data, A = atributos, T = carimbos de hora
   :::column-end:::
:::row-end:::

Você deve executar esse comando RoboCopy para cada um dos diretórios no Windows Server como um destino, que você configurou com a sincronização de arquivos para um arquivo do Azure.

Você pode executar vários desses comandos em paralelo.
Depois que essa etapa do RoboCopy for concluída, você poderá permitir que seus usuários e aplicativos acessem o Windows Server como faziam o dispositivo StorSimple antes.

Consulte os arquivos de log do Robocopy para ver se os arquivos foram deixados para trás. Se houver problemas, na maioria dos casos, você poderá resolvê-los depois que a migração for concluída e seus usuários e aplicativos tiverem sido redirecionados para o Windows Server. Se você precisar corrigir quaisquer problemas, faça isso antes da fase 7.

É provável que seja necessário criar os compartilhamentos SMB no Windows Server que você tinha nos dados do StorSimple antes. Você pode carregar essa etapa e fazer isso mais cedo para não perder tempo aqui, mas deve garantir que antes desse ponto, nenhuma alteração nos arquivos ocorra no Windows Server.

Se você tiver uma implantação DFS-N, poderá apontar os namespaces DFN para os locais da nova pasta do servidor. Se você não tiver uma implantação DFS-N e, em seguida, o dispositivo 8100 8600 foi disponibilizado localmente com um servidor Windows, você poderá pegar esse servidor fora do domínio e ingressar no domínio em seu novo Windows Server com o AFS para o domínio, atribuir a ele o mesmo nome de servidor que o servidor antigo e os mesmos nomes de compartilhamento, e o novo servidor permanecerá transparente para seus , política de grupo ou scripts.

## <a name="phase-7-deprovision"></a>Fase 7: desprovisionar

Durante a última fase, você fez a iteração por meio de vários clones de volume e, eventualmente, conseguiu reduzir o acesso do usuário para o novo Windows Server depois de colocar o dispositivo StorSimple offline.

Agora você pode começar a desprovisionar recursos desnecessários.
Antes de começar, é uma prática recomendada observar sua nova implantação de Sincronização de Arquivos do Azure em produção, por um pouco. Isso oferece opções para corrigir quaisquer problemas que você possa encontrar.

Quando estiver satisfeito e tiver observado sua implantação AFS por pelo menos alguns dias, você poderá começar a desprovisionar os recursos nesta ordem:

1. Desative a VM do Azure que usamos para mover dados dos clones de volume para os compartilhamentos de arquivos do Azure por meio da sincronização de arquivos.
2. Vá para o recurso de serviço de sincronização de armazenamento no Azure e cancele o registro da VM do Azure. Isso o Remove de todos os grupos de sincronização.

    > [!WARNING]
    > **Certifique-se de escolher o computador certo.** Você desativou a VM de nuvem, isso significa que ela deve ser mostrada como o único servidor offline na lista de servidores registrados. Você não deve escolher o Windows Server local nesta etapa. isso cancelará o seu registro.

3. Exclua a VM do Azure e seus recursos.
4. Desabilite o dispositivo virtual StorSimple 8020.
5. Desprovisione todos os recursos do StorSimple no Azure.
6. Desconecte o dispositivo físico StorSimple do seu data center.

A migração foi concluída.

## <a name="next-steps"></a>Próximas etapas

Familiarize-se com o Sincronização de Arquivos do Azure. Especialmente com a flexibilidade das políticas de camadas de nuvem.

Se você vir no portal do Azure ou nos eventos anteriores, que alguns arquivos não estão sincronizando permanentemente, examine o guia de solução de problemas para obter as etapas para resolver esses problemas.

* [Visão geral do Sincronização de Arquivos do Azure: aka.ms/AFS](https://aka.ms/AFS)
* [Disposição em camadas de nuvem](storage-sync-cloud-tiering.md) 
* [Guia de solução de problemas Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)
