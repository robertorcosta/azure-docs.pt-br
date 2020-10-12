---
title: Notas sobre a versão do Gerenciador de Armazenamento do Microsoft Azure
description: Leia as notas de versão da versão mais recente do Gerenciador de Armazenamento do Microsoft Azure. As notas de versão para versões anteriores também são mostradas.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 91bc7adaf7829766c471056c50c1c3abd70dda63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87828771"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Notas sobre a versão do Gerenciador de Armazenamento do Microsoft Azure

Este artigo contém as notas de versão mais recentes para Gerenciador de Armazenamento do Azure, bem como notas de versão para versões anteriores. 

O [Gerenciador de Armazenamento do Microsoft Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

Para baixar versões anteriores do Gerenciador de Armazenamento, você pode visitar a [página de lançamentos](https://github.com/microsoft/AzureStorageExplorer/releases) do repositório github.

## <a name="version-1110"></a>1.11.0 da versão
4/11/2019

### <a name="new"></a>Novo
* Operações para BLOBs, ADLS Gen2 e Managed Disks usam o AzCopy integrado. Mais especificamente, as seguintes operações são feitas usando AzCopy:
   * Blobs
      * Abrir para edição + carregamento
      * Carregar, incluindo arrastar & soltar
      * Baixar
      * Copiar & colar #1249
      * Excluir
   * Blobs de ADLS Gen2
      * Carregar, incluindo arrastar & soltar
      * Baixar
      * Copiar & colar
      * Excluir, incluindo exclusão de pasta
   * Managed Disks
      * Carregar
      * Baixar
      * Copiar & colar

   Além disso, vários recursos solicitados com frequência foram adicionados à experiência de AzCopy integrada:
   * Resoluções de conflitos-você será solicitado durante as transferências para resolver conflitos. #1455
   * Carregar como BLOBs de páginas – você pode escolher se o AzCopy carrega ou não arquivos. vhd e. vhdx como BLOBs de páginas. #1164 e #1601
   * Parâmetros AzCopy configuráveis – várias configurações foram adicionadas para ajustar o desempenho e o uso de recursos do AzCopy. Veja mais detalhes abaixo.

* Para habilitar o acesso de vários protocolos de ADLS Gen2 e BLOBs e aprimorar ainda mais as experiências de ADLS Gen2, adicionamos os seguintes recursos para as contas de ADLS Gen2:
   * Pesquisar usando nomes amigáveis para definir permissões de ACL
   * Exibir contêineres ocultos, como $logs e $web
   * Adquirir e interromper a concessão de contêiner
   * Adquirir e interromper a concessão de blob #848
   * Gerenciar políticas de acesso de contêiner
   * Configurar camadas de acesso de BLOB
   * Copiar & colar BLOBs

* Nesta versão, estamos visualizando 17 idiomas adicionais. Você pode alternar para um idioma de sua escolha na página configurações em "aplicativo" → "configurações regionais" → "idioma (visualização)". Ainda estamos trabalhando duro para traduzir cadeias de caracteres adicionais e melhorar a qualidade da tradução. Se você tiver algum comentário sobre uma tradução ou se observar uma cadeia de caracteres que ainda não está traduzida, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* Em todas as versões, tentamos integrar algumas configurações para habilitar a desativação correta Gerenciador de Armazenamento. Nesta versão, adicionamos as configurações para configurar ainda mais o AzCopy, bem como para ocultar os nós de serviço:
   * Limite de largura de banda AzCopy-ajuda a controlar a quantidade de AzCopy de rede usada. Você pode encontrar essa configuração em "Transfers" → "AzCopy" → "taxa de transferência máxima". #1099
   * Verificação de MD5 do AzCopy – permite que você configure se e como a estritamente AzCopy verifica se há hashes MD5 no download. Você pode encontrar essa configuração em "Transfers" → "AzCopy" → "check MD5".
   * Tamanho do buffer de memória e simultaneidade de AzCopy – por padrão, o AzCopy analisará seu computador para determinar valores padrão razoáveis para essas configurações. Mas se você tiver problemas de desempenho, essas configurações avançadas poderão ser usadas para adaptar ainda mais o modo como o AzCopy é executado no computador. Você pode encontrar essas configurações em "transferes" → "AzCopy". #994
   * Exibir e ocultar nós de serviço-essas configurações fornecem as opções para exibir ou ocultar qualquer um dos serviços do Azure aos quais Gerenciador de Armazenamento dá suporte. Você pode encontrar essas configurações na seção "serviços". #1877

* Ao criar um instantâneo de um disco gerenciado, um nome padrão é fornecido agora. #1847
* Ao anexar com o Azure AD, se você anexar um contêiner de blob ADLS Gen2, "(ADLS Gen2)" será mostrado ao lado do nó. #1861

### <a name="fixes"></a>Correções
* Ao copiar, carregar ou baixar discos grandes, Gerenciador de Armazenamento às vezes falharia em revogar o acesso aos discos envolvidos na operação. Esse problema foi corrigido. #2048
* As estatísticas de tabela falharam ao exibir uma consulta de chave de partição. Esse problema foi corrigido. #1886

### <a name="known-issues"></a>Problemas conhecidos
* Gerenciador de Armazenamento 1.11.0 agora requer um ponto de extremidade DFS (como "myaccount.dfs.core.windows.net") para anexar a contêineres de ADLS Gen2. As versões anteriores do Gerenciador de Armazenamento permitiam que você usa um ponto de extremidade de BLOB. Esses anexos podem não funcionar mais após a atualização para o 1.11.0. Se você encontrar esse problema, anexe novamente usando o ponto de extremidade do DFS.
* As configurações numéricas não são verificadas se se encontram em um intervalo válido. #2140
* A cópia de contêineres de blob de uma conta de armazenamento para outra no modo de exibição de árvore pode falhar. Estamos investigando o problema. #2124
* A configuração de atualização automática ainda não afeta todas as operações no Gerenciador de BLOBs.
* Os recursos de disco gerenciado não têm suporte no Azure Stack.
* Se um upload ou uma colagem do disco falhar e um novo disco tiver sido criado antes da falha, Gerenciador de Armazenamento não excluirá o disco para você.
* Dependendo de quando você cancela um upload de disco ou cola, é possível deixar o novo disco em um estado corrompido. Se isso acontecer, você precisará excluir o novo disco ou chamar manualmente as APIs de disco para substituir o conteúdo do disco de modo que ele não seja mais corrompido.
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Veja # 537 para mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
   * ADLS Gen2
   * Managed Disks
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="previous-releases"></a>Versões anteriores

* [1.10.1 da versão](#version-1101)
* [1.10.0 da versão](#version-1100)
* [1.9.0 da versão](#version-190)
* [1.8.1 da versão](#version-181)
* [1.8.0 da versão](#version-180)
* [Versão 1.7.0](#version-170)
* [Versão 1.6.2](#version-162)
* [Versão 1.6.1](#version-161)
* [1.6.0 da versão](#version-160)
* [Versão 1.5.0](#version-150)
* [Version 1.4.4](#version-144)
* [Versão 1.4.3](#version-143)
* [Versão 1.4.2](#version-142)
* [Versão 1.4.1](#version-141)
* [Versão 1.3.0](#version-130)
* [Versão 1.2.0](#version-120)
* [Version 1.1.0](#version-110)
* [Versão 1.0.0](#version-100)
* [Versão 0.9.6](#version-096)
* [Versão 0.9.5](#version-095)
* [Versões 0.9.4 e 0.9.3](#version-094-and-093)
* [Versão 0.9.2](#version-092)
* [Versões 0.9.1 e 0.9.0](#version-091-and-090)
* [Versão 0.8.16](#version-0816)
* [Versão 0.8.14](#version-0814)
* [Versão 0.8.13](#version-0813)
* [Versões 0.8.12 e 0.8.11 e 0.8.10](#version-0812-and-0811-and-0810)
* [Versões 0.8.9 e 0.8.8](#version-089-and-088)
* [Versão 0.8.7](#version-087)
* [Versão 0.8.6](#version-086)
* [Versão 0.8.5](#version-085)
* [Versão 0.8.4](#version-084)
* [Versão 0.8.3](#version-083)
* [Versão 0.8.2](#version-082)
* [Versão 0.8.0](#version-080)
* [Versão 0.7.20160509.0](#version-07201605090)
* [Versão 0.7.20160325.0](#version-07201603250)
* [Versão 0.7.20160129.1](#version-07201601291)
* [Versão 0.7.20160105.0](#version-07201601050)
* [Versão 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>1.10.1 da versão
19/9/2019

### <a name="hotfix"></a>Hotfix
* Alguns usuários encontraram um erro no 1.10.0 ao tentar exibir seus dados em suas contas do ADLS Gen 1. Esse erro impediu que o painel do Explorer fosse renderizado corretamente. Esse problema foi corrigido. #1853 #1865

### <a name="new"></a>Novo
* Agora Gerenciador de Armazenamento tem uma interface do usuário de configurações dedicada. Você pode acessá-lo em Editar configurações de → ou clicando no ícone de configurações (a engrenagem) na barra de ferramentas vertical à esquerda. Esse recurso é a primeira etapa que estamos levando em voltar a fornecer uma variedade de [configurações solicitadas pelo usuário](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). A partir desta versão, há suporte para as seguintes configurações:
  * Tema
  * Proxy
  * Fazer logoff na #6 de saída
  * Habilitar entrada de fluxo de código do dispositivo
  * #1526 de atualização automática
  * Habilitar AzCopy
  * Duração da SAS do AzCopy se houver outras configurações que você gostaria de ver adicionadas, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) que descreva a configuração que você deseja ver.
* Gerenciador de Armazenamento agora dá suporte a Managed Disks. Você pode:
  * Carregar um VHD local para um novo disco
  * Baixar um disco
  * Copiar e colar discos entre grupos de recursos e regiões
  * Excluir discos
  * Criar um instantâneo de um disco

O carregamento, o download e a cópia entre regiões de discos são alimentados por AzCopy v10.
* Agora, o Gerenciador de Armazenamento pode ser instalado por meio do armazenamento de snap no Linux. Quando você instala o por meio do repositório de snap, todas as dependências são instaladas para você, incluindo o .NET Core! Atualmente, verificamos se Gerenciador de Armazenamento é executado bem no Ubuntu e no CentOS. Se você encontrar problemas ao instalar do repositório de snap em outros distribuições do Linux, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Para saber mais sobre a instalação do repositório de snap, consulte nosso [Guia de introdução](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux). #68
* Duas alterações importantes foram feitas para anexar com Azure Active Directory (Azure AD), que se destinam a tornar o recurso mais útil para ADLS Gen2 usuários:
  * Agora você seleciona o locatário no qual está anexando o recurso. Isso significa que você não precisa mais ter acesso de RBAC à assinatura do recurso.
  * Se você estiver anexando um contêiner de blob ADLS Gen2, agora você poderá anexar a um caminho específico no contêiner.
* Ao gerenciar ACLs para ADLS Gen2 arquivos e pastas, Gerenciador de Armazenamento agora mostrará os nomes amigáveis para entidades na ACL. #957
* Ao adicionar por meio de OID a um ADLS Gen2 ACL, o Gerenciador de Armazenamento agora validará que o OID pertence a uma entidade válida em seu locatário. #1603
* Os atalhos de teclado para navegar entre as guias agora usam mais combinações de teclas padrão. #1018
* O meio de clique em uma guia agora o fechará. #1348
* Se uma transferência AzCopy contiver ignorar e não houver falhas, Gerenciador de Armazenamento agora mostrará um ícone de aviso para realçar que as ignorações ocorreram. #1490
* O AzCopy integrado foi atualizado para a versão 10.2.1. Além disso, agora você pode exibir a versão do AzCopy instalada na caixa de diálogo sobre. #1343

### <a name="fixes"></a>Correções
* Muitos usuários se apresentaram a vários erros "não é possível ler a versão de indefinido" ou "não é possível ler a conexão de erro indefinido" ao trabalhar com contas de armazenamento anexadas. Embora ainda continuemos investigando a causa raiz desse problema, no 1.10.0, melhoramos a manipulação de erros em relação ao carregamento de contas de armazenamento anexadas. #1626, #985 e #1532
* Era possível que a árvore do Gerenciador (lado esquerdo) entrasse em um estado em que o foco saltaria para o nó superior repetidamente. Esse problema foi corrigido. #1596
* Ao gerenciar os instantâneos de um blob, os leitores de tela não lêem o carimbo de data/hora associado ao instantâneo. Esse problema foi corrigido. #1202
* A configuração de proxy no macOS não estava sendo definida no tempo para o processo de autenticação usá-las. Esse problema foi corrigido. #1567
* Se uma conta de armazenamento em uma nuvem soberanas foi anexada usando o nome e a chave, AzCopy não funcionaria. Esse problema foi corrigido. #1544
* Ao anexar por meio de uma cadeia de conexão, Gerenciador de Armazenamento agora removerá espaços à direita. #1387

### <a name="known-issues"></a>Problemas conhecidos
* A configuração de atualização automática ainda não afeta todas as operações no Gerenciador de BLOBs.
* Os recursos de disco gerenciado não têm suporte no Azure Stack.
* Se um upload ou uma colagem do disco falhar e um novo disco tiver sido criado antes da falha, Gerenciador de Armazenamento não excluirá o disco para você.
* Dependendo de quando você cancela um upload de disco ou cola, é possível deixar o novo disco em um estado corrompido. Se isso acontecer, você precisará excluir o novo disco ou chamar manualmente as APIs de disco para substituir o conteúdo do disco de modo que ele não seja mais corrompido.
* Dependendo de quando você cancela um upload de disco ou cola, é possível deixar o novo disco em um estado corrompido. Se isso acontecer, você precisará excluir o novo disco ou chamar manualmente as APIs de disco para substituir o conteúdo do disco de modo que ele não seja mais corrompido.
* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Veja # 537 para mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
   * ADLS Gen2
   * Managed Disks
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.


## <a name="version-1100"></a>1.10.0 da versão
9/12/2019

### <a name="new"></a>Novo

* Agora Gerenciador de Armazenamento tem uma interface do usuário de configurações dedicada. Você pode acessá-lo em Editar configurações de → ou clicando no ícone de configurações (a engrenagem) na barra de ferramentas vertical à esquerda. Esse recurso é a primeira etapa que estamos levando em voltar a fornecer uma variedade de [configurações solicitadas pelo usuário](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). A partir desta versão, há suporte para as seguintes configurações:
    * Tema
    * Proxy
    * Fazer logoff na [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6) de saída
    * Habilitar entrada de fluxo de código do dispositivo
    * [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526) de atualização automática
    * Habilitar AzCopy
    * Duração da SAS do AzCopy

    Se houver outras configurações que você gostaria de ver adicionadas, [abra um problema no GitHub que descreva a configuração que você deseja ver](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Gerenciador de Armazenamento agora dá suporte a Managed Disks. Você pode:
    * Carregar um VHD local para um novo disco
    * Baixar um disco
    * Copiar e colar discos entre grupos de recursos e regiões
    * Excluir discos
    * Criar um instantâneo de um disco

    O carregamento, o download e a cópia entre regiões de discos são alimentados por AzCopy v10.
* Agora, o Gerenciador de Armazenamento pode ser instalado por meio do armazenamento de snap no Linux. Quando você instala o por meio do repositório de snap, todas as dependências são instaladas para você, incluindo o .NET Core! Atualmente, verificamos se Gerenciador de Armazenamento é executado bem no Ubuntu e no CentOS. Se você encontrar problemas ao instalar do repositório de snap em outros distribuições do Linux, [abra um problema no GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Para saber mais sobre a instalação do repositório de snap, consulte nosso [Guia de introdução](https://aka.ms/storageexplorer/snapinformation). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Duas alterações importantes foram feitas para anexar com Azure Active Directory (Azure AD), que são destinadas a tornar o recurso mais útil para ADLS Gen2 usuários: * agora você seleciona o locatário no qual está anexando o recurso. Isso significa que você não precisa mais ter acesso de RBAC à assinatura do recurso.
        * Se você estiver anexando um contêiner de blob ADLS Gen2, agora você pode anexar a um caminho específico no contêiner.
* Ao gerenciar ACLs para ADLS Gen2 arquivos e pastas, Gerenciador de Armazenamento agora mostrará os nomes amigáveis para entidades na ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Ao adicionar por meio de OID a um ADLS Gen2 ACL, o Gerenciador de Armazenamento agora validará que o OID pertence a uma entidade válida em seu locatário. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Os atalhos de teclado para navegar entre as guias agora usam mais combinações de teclas padrão. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* O meio de clique em uma guia agora o fechará. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Se uma transferência AzCopy contiver ignorar e não houver falhas, Gerenciador de Armazenamento agora mostrará um ícone de aviso para realçar que as ignorações ocorreram. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* O AzCopy integrado foi atualizado para a versão 10.2.1. Além disso, agora você pode exibir a versão do AzCopy instalada na caixa de diálogo sobre. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Correções

* Muitos usuários se apresentaram a vários erros "não é possível ler a versão de indefinido" ou "não é possível ler a conexão de erro indefinido" ao trabalhar com contas de armazenamento anexadas. Embora ainda continuemos investigando a causa raiz desse problema, no 1.10.0, melhoramos a manipulação de erros em relação ao carregamento de contas de armazenamento anexadas. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)e [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Era possível que a árvore do Gerenciador (lado esquerdo) entrasse em um estado em que o foco saltaria para o nó superior repetidamente. Esse problema foi corrigido. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Ao gerenciar os instantâneos de um blob, os leitores de tela não lêem o carimbo de data/hora associado ao instantâneo. Esse problema foi corrigido. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* A configuração de proxy no macOS não estava sendo definida no tempo para o processo de autenticação usá-las. Esse problema foi corrigido. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Se uma conta de armazenamento em uma nuvem soberanas foi anexada usando o nome e a chave, AzCopy não funcionaria. Esse problema foi corrigido. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Ao anexar por meio de uma cadeia de conexão, Gerenciador de Armazenamento agora removerá espaços à direita. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Problemas conhecidos

* A configuração de atualização automática ainda não afeta todas as operações no Gerenciador de BLOBs.
* Os recursos de disco gerenciado não têm suporte no Azure Stack.
* Se um upload ou uma colagem do disco falhar e um novo disco tiver sido criado antes da falha, Gerenciador de Armazenamento não excluirá o disco para você.
* Dependendo de quando você cancela um upload de disco ou cola, é possível deixar o novo disco em um estado corrompido. Se isso acontecer, você precisará excluir o novo disco ou chamar manualmente as APIs de disco para substituir o conteúdo do disco de modo que ele não seja mais corrompido.
* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Veja # 537 para mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração personalizada do AAD, talvez não consiga entrar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
   * ADLS Gen2
   * Managed Disks
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="version-190"></a>1.9.0 da versão
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Baixar Gerenciador de Armazenamento do Azure 1.9.0
- [Gerenciador de Armazenamento do Azure 1.9.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Gerenciador de Armazenamento do Azure 1.9.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Gerenciador de Armazenamento do Azure 1.9.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Agora você pode anexar contêineres de blob por meio do Azure AD (permissões RBAC ou ACL). Esse recurso destina-se a ajudar os usuários que têm acesso aos contêineres, mas não às contas de armazenamento nas quais os contêineres estão. Consulte nosso guia de Introdução para obter mais informações sobre esse recurso.
* Adquira e interrompa a concessão agora trabalhe com o RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* O gerenciamento de políticas de acesso e a definição do nível de acesso público agora funcionam com o RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* A exclusão de pastas de BLOBs agora funciona com o RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* A alteração da camada de acesso ao blob agora funciona com o RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Agora você pode redefinir rapidamente o acesso rápido por meio de "Help" → "Reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Recursos de visualização

* A entrada do fluxo de código do dispositivo agora está disponível para visualização. Para habilitá-lo, vá para "visualização" → "usar entrada de fluxo de código de dispositivo". Incentivamos qualquer usuário que tenha problemas com janelas de entrada em branco para experimentar esse recurso, pois pode ser uma forma mais confiável de entrar.
* O Gerenciador de Armazenamento integrado ao AzCopy está disponível para visualização no momento. Para habilitá-lo, acesse "visualização" → "usar AzCopy para upload e download de blob aprimorados". As transferências de blob concluídas com AzCopy devem ser mais rápidas e com melhor desempenho.

### <a name="fixes"></a>Correções

* Corrigido não é possível carregar mais de 50 assinaturas para uma conta. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Corrigido o botão "entrar" não está funcionando na barra de referências que aparece quando um link direto falha. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Correção de não carregar arquivos. app no macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* "Repetir tudo" corrigido não está funcionando para uma renomeação de blob com falha. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* "Cancelar" fixo não está funcionando ao abrir um blob. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Correção de vários problemas de ortografia e dica de ferramenta em todo o produto. Muitos agradecimentos a todos os que relataram esses problemas! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Problemas conhecidos

* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentativa de acessar BLOBs ADLS Gen2 quando por trás de um proxy pode falhar.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Veja # 537 para mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
   * ADLS Gen2
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="version-181"></a>1.8.1 da versão
5/13/2019

### <a name="hotfixes"></a>Hotfixes
* Em alguns casos, clicar em "carregar mais" no nível de recurso não retornará a próxima página de recursos. Esse problema foi corrigido. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* No Windows, os downloads do AzCopy falharão se um único arquivo ou pasta fosse baixado e o nome do arquivo ou da pasta tivesse um caractere que era inválido para um caminho do Windows. Esse problema foi corrigido. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Em casos extremamente raros, ao executar uma renomeação de um compartilhamento de arquivos ou uma renomeação em um compartilhamento de arquivos, se as cópias da renomeação falharem ou se o Storage Explore não conseguir confirmar o sucesso das cópias com o Azure, houve o potencial para Gerenciador de Armazenamento excluir os arquivos originais antes de a cópia ter sido concluída. Esse problema foi corrigido.

### <a name="new"></a>Novo

* A versão integrada do AzCopy foi atualizada para a versão 10.1.0.
* Ctrl/Cmd + R agora pode ser usado para atualizar o editor voltado para o momento. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A versão da API de armazenamento Azure Stack foi alterada para 2017-04-17.
* A caixa de diálogo Gerenciar acesso para ADLS Gen2 agora manterá a máscara sincronizada de forma semelhante a outras ferramentas de permissões POSIX. A interface do usuário também avisará se for feita uma alteração que faz com que as permissões de um usuário ou grupo excedam os limites da máscara. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Para carregamentos do AzCopy, o sinalizador para calcular e definir o hash MD5 agora está habilitado. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Recursos de visualização

* A entrada do fluxo de código do dispositivo agora está disponível para visualização. Para habilitá-lo, vá para "visualização" → "usar entrada de fluxo de código de dispositivo". Incentivamos qualquer usuário que tenha problemas com janelas de entrada em branco para experimentar esse recurso, pois pode ser uma forma mais confiável de entrar.
* O Gerenciador de Armazenamento integrado ao AzCopy está disponível para visualização no momento. Para habilitá-lo, acesse "visualização" → "usar AzCopy para upload e download de blob aprimorados". As transferências de blob concluídas com AzCopy devem ser mais rápidas e com melhor desempenho.

### <a name="fixes"></a>Correções

* A caixa de diálogo políticas de acesso não definirá mais uma data de expiração nas políticas de acesso de armazenamento que não têm expiração. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Algumas alterações foram feitas na caixa de diálogo gerar SAS para garantir que as políticas de acesso armazenadas sejam usadas corretamente ao gerar uma SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ao tentar carregar um arquivo com alinhamento de não 512 bytes em um blob de páginas, Gerenciador de Armazenamento agora vai expor um erro mais relevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* A cópia de um contêiner de BLOB que utiliza um nome de exibição falharia. Agora, o nome real do contêiner de BLOBs é usado. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* A tentativa de executar determinadas ações em uma pasta ADLS Gen2 que tinha caracteres Unicode em seu nome falharia. Todas as ações agora devem funcionar. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemas conhecidos

* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentativa de acessar BLOBs ADLS Gen2 quando por trás de um proxy pode falhar.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Veja # 537 para mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
   * ADLS Gen2
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="version-180"></a>1.8.0 da versão
5/1/2019

### <a name="new"></a>Novo

* A versão integrada do AzCopy foi atualizada para a versão 10.1.0.
* Ctrl/Cmd + R agora pode ser usado para atualizar o editor voltado para o momento. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A versão da API de armazenamento Azure Stack foi alterada para 2017-04-17.
* A caixa de diálogo Gerenciar acesso para ADLS Gen2 agora manterá a máscara sincronizada de forma semelhante a outras ferramentas de permissões POSIX. A interface do usuário também avisará se for feita uma alteração que faz com que as permissões de um usuário ou grupo excedam os limites da máscara. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Para carregamentos do AzCopy, o sinalizador para calcular e definir o hash MD5 agora está habilitado. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Recursos de visualização

* A entrada do fluxo de código do dispositivo agora está disponível para visualização. Para habilitá-lo, vá para "visualização" → "usar entrada de fluxo de código de dispositivo". Incentivamos qualquer usuário que tenha problemas com janelas de entrada em branco para experimentar esse recurso, pois pode ser uma forma mais confiável de entrar.
* O Gerenciador de Armazenamento integrado ao AzCopy está disponível para visualização no momento. Para habilitá-lo, acesse "visualização" → "usar AzCopy para upload e download de blob aprimorados". As transferências de blob concluídas com AzCopy devem ser mais rápidas e com melhor desempenho.

### <a name="fixes"></a>Correções

* A caixa de diálogo políticas de acesso não definirá mais uma data de expiração nas políticas de acesso de armazenamento que não têm expiração. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Algumas alterações foram feitas na caixa de diálogo gerar SAS para garantir que as políticas de acesso armazenadas sejam usadas corretamente ao gerar uma SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ao tentar carregar um arquivo com alinhamento de não 512 bytes em um blob de páginas, Gerenciador de Armazenamento agora vai expor um erro mais relevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* A cópia de um contêiner de BLOB que utiliza um nome de exibição falharia. Agora, o nome real do contêiner de BLOBs é usado. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* A tentativa de executar determinadas ações em uma pasta ADLS Gen2 que tinha caracteres Unicode em seu nome falharia. Todas as ações agora devem funcionar. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemas conhecidos

* Ao executar um download de BLOB não AzCopy, o MD5 para arquivos grandes não está sendo verificado. Isso ocorre devido a um bug no SDK de armazenamento. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentativa de acessar BLOBs ADLS Gen2 quando por trás de um proxy pode falhar.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Veja # 537 para mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
   * ADLS Gen2
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A execução do Gerenciador de Armazenamento no Linux exige que determinadas dependências sejam instaladas primeiro. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Gerenciador de armazenamento para obter mais informações.

## <a name="version-170"></a>Versão 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Baixar Gerenciador de Armazenamento do Azure 1.7.0
- [Gerenciador de Armazenamento do Azure 1.7.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Gerenciador de Armazenamento do Azure 1.7.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Gerenciador de Armazenamento do Azure 1.7.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Novo

* Agora você pode alterar o proprietário e o grupo proprietário ao gerenciar o acesso para um ADLS Gen2 contêiner, arquivo ou pasta.
* No Windows, a atualização de Gerenciador de Armazenamento de dentro do produto agora é uma instalação incremental. Isso deve resultar em uma experiência de atualização mais rápida. Se você preferir uma instalação limpa, poderá baixar o [instalador](https://azure.microsoft.com/features/storage-explorer/) por conta própria e, em seguida, instalá-lo manualmente. #1089

### <a name="preview-features"></a>Recursos de visualização

* A entrada do fluxo de código do dispositivo agora está disponível para visualização. Para habilitá-lo, vá para "visualização" → "usar entrada de fluxo de código de dispositivo". Incentivamos qualquer usuário que tenha problemas com janelas de entrada em branco para experimentar esse recurso, pois pode ser uma forma mais confiável de entrar. #938
* O Gerenciador de Armazenamento integrado ao AzCopy está disponível para visualização no momento. Para habilitá-lo, acesse "visualização" → "usar AzCopy para upload e download de blob aprimorados". As transferências de blob concluídas com AzCopy devem ser mais rápidas e com melhor desempenho.

### <a name="fixes"></a>Correções

* Agora você pode escolher o tipo de BLOB que deseja carregar como quando AzCopy está habilitado. #1111
* Anteriormente, se você tivesse habilitado sites estáticos para uma conta de armazenamento ADLS Gen2 e, em seguida, anexou-o com o nome e a chave, Gerenciador de Armazenamento não detectou que o namespace hierárquico estava habilitado. Esse problema foi corrigido. #1081
* No editor de BLOB, a classificação por dias de retenção restantes ou o status foi rompido. Esse problema foi corrigido. #1106
* Depois da 1.5.0, Gerenciador de Armazenamento não espere mais que as cópias do lado do servidor sejam concluídas antes de relatar o sucesso durante uma renomeação ou cópia & colar. Esse problema foi corrigido. #976
* Ao usar o recurso AzCopy experimental, o comando copiado depois de clicar em "copiar comando para a área de transferência" nem sempre era executável por si só. Agora, todos os comandos necessários para executar a transferência manualmente serão copiados. #1079
* Anteriormente, ADLS Gen2 BLOBs não eram acessíveis se você estivesse atrás de um proxy. Isso ocorreu devido a um bug em uma nova biblioteca de rede usada pelo SDK de armazenamento. No 1.7.0, uma tentativa de mitigar esse problema foi feita, mas algumas pessoas podem continuar a ver os problemas. Uma correção completa será lançada em uma atualização futura. #1090
* No 1.7.0, a caixa de diálogo Salvar arquivo agora memoriza corretamente o último local em que você salvou um arquivo. #16
* No painel Propriedades, a camada de SKU de uma conta de armazenamento estava sendo mostrada como a espécie da conta. Esse problema foi corrigido. #654
* Às vezes, era impossível interromper a concessão de um blob, mesmo se você inseriu o nome do blob corretamente. Esse problema foi corrigido. #1070

### <a name="known-issues"></a>Problemas conhecidos

* Ao usar o RBAC, Gerenciador de Armazenamento requer algumas permissões de camada de gerenciamento para acessar os recursos de armazenamento. Consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter mais informações.
* A tentativa de acessar BLOBs ADLS Gen2 quando por trás de um proxy pode falhar.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Veja # 537 para mais informações.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, veja #537.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Versão 1.6.2
09/01/2019

### <a name="hotfixes"></a>Hotfixes
* Na 1.6.1, entidades adicionadas às ACLs do ADLS Gen2 por ObjectId de entidades que não eram usuários sempre foram adicionadas como grupos. Agora, somente grupos são adicionados como grupos e entidades como Aplicativos Empresariais e Entidades de Serviço são adicionadas como usuários. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Se uma conta de Armazenamento do ADLS Gen2 não tivesse contêineres e fosse anexada com o nome e a chave, o Gerenciador de Armazenamento não detectaria que a Conta de Armazenamento era ADLS Gen2. Esse problema foi corrigido. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* Na 1.6.0, conflitos durante a copiar e colar não solicitariam uma resolução. Em vez disso, a cópia em conflito simplesmente falharia. Agora, no primeiro conflito, você será consultado sobre como gostaria de resolvê-lo. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Devido a limitações de API, todas as validações de ObjectIds na caixa de diálogo Gerenciar o acesso foram desabilitadas. A validação agora ocorrerá somente para usuário UPNs. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Na caixa de diálogo Gerenciar o acesso ao ADLS Gen2, não foi possível modificar as permissões para um grupo. Esse problema foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Adicionado o arrastar e soltar para carregar suporte para o editor do ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Na propriedade de URL na caixa de diálogo de propriedades para arquivos e pastas Gen2 ADLS faltava, às vezes, um '/'. Esse problema foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se ao obter as permissões atuais para um contêiner do ADLS Gen2, o arquivo ou a pasta falha, então o erro agora é exibido no log de atividades corretamente. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário criado para abrir arquivos foi encurtado para reduzir a chance de criação de um caminho que é maior que MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A caixa de diálogo Conectar agora aparece corretamente quando nenhum usuário está conectado e os recursos não foram anexados. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* No 1.6.0, salvar as propriedades para arquivos e Blobs de HNS seria codificar o valor de cada propriedade. Isso resultou na codificação desnecessária de valores que continham somente caracteres ASCII. Agora, os valores só serão codificados se eles contiverem caracteres não ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Fazer o download de uma pasta em um contêiner de Blob não HNS falhará se uma SAS foi usada e ela não tiver permissões de leitura. Esse problema foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Cancelar uma transferência de AzCopy que não funcionou. Esse problema foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* O AzCopy falhará ao tentar fazer o download de uma pasta de um contêiner de Blob do ADLS Gen2, se a pasta tinha espaços em seu nome. Esse problema foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor do CosmosDB foi interrompido no 1.6.0. Agora isso foi corrigido. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Use agora o Gerenciador de Armazenamento para acessar seus dados de Blob por meio de [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se estiver conectado e o Gerenciador de Armazenamento não pôde recuperar as chaves para sua conta de armazenamento, em seguida, um token OAuth será usado para autenticar ao interagir com seus dados.
* O Gerenciador de Armazenamento agora dá suporte a contas de armazenamento do ADLS Gen2. Quando o Gerenciador de Armazenamento detecta que esse namespace hierárquico está habilitado para uma conta de armazenamento, "(ADLS Gen2 Preview )" irá aparecer ao lado do nome da sua conta de armazenamento. O Gerenciador de Armazenamento é capaz de detectar se o namespace hierárquico está habilitado quando você está ou não conectado, ou se tiver conectado a sua conta de armazenamento com o nome e a chave. Para contas de armazenamento do ADLS Gen2, pode-se usar o Gerenciador de Armazenamento:
  * Criar e excluir contêiner
  * Gerenciar propriedades do contêiner e permissões (lado esquerdo)
  * Exibir e navegar em dados dentro de contêineres
  * Criar novas pastas
  * Carregar, fazer o download, renomear e excluir arquivos e pastas
  * Gerencie propriedades de arquivo, pasta e permissões (lado direito).
    
    Outros recursos típicos de Blob, como a exclusão reversível e instantâneos, não estão disponíveis no momento. Gerenciando permissões também só está disponível quando conectado. Além disso, ao trabalhar em uma conta de armazenamento do ADLS Gen2, o Gerenciador de Armazenamento usará o AzCopy para todos os carregamentos, downloads e padrões usando o nome e as credenciais de chave para todas as operações, se disponível.
* Depois de comentários do usuário de alta segurança, interromper a concessão mais uma vez pode ser usado para interromper as concessões em vários blobs ao mesmo tempo.

### <a name="known-issues"></a>Problemas conhecidos

* Ao fazer o download de uma conta de armazenamento do ADLS Gen2 se um dos arquivos que estão sendo transferidos já existir, o AzCopy irá falhar algumas vezes. Isso será corrigido em um hotfix futuro.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, veja #537.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Versão 1.6.1
18/12/2018

### <a name="hotfixes"></a>Hotfixes
* Devido a limitações de API, todas as validações de ObjectIds na caixa de diálogo Gerenciar o acesso foram desabilitadas. A validação agora ocorrerá somente para usuário UPNs. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Na caixa de diálogo Gerenciar o acesso ao ADLS Gen2, não foi possível modificar as permissões para um grupo. Esse problema foi corrigido. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Adicionado o arrastar e soltar para carregar suporte para o editor do ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Na propriedade de URL na caixa de diálogo de propriedades para arquivos e pastas Gen2 ADLS faltava, às vezes, um '/'. Esse problema foi corrigido. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se ao obter as permissões atuais para um contêiner do ADLS Gen2, o arquivo ou a pasta falha, então o erro agora é exibido no log de atividades corretamente. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* O caminho temporário criado para abrir arquivos foi encurtado para reduzir a chance de criação de um caminho que é maior que MAX_PATH no Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A caixa de diálogo Conectar agora aparece corretamente quando nenhum usuário está conectado e os recursos não foram anexados. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* No 1.6.0, salvar as propriedades para arquivos e Blobs de HNS seria codificar o valor de cada propriedade. Isso resultou na codificação desnecessária de valores que continham somente caracteres ASCII. Agora, os valores só serão codificados se eles contiverem caracteres não ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Fazer o download de uma pasta em um contêiner de Blob não HNS falhará se uma SAS foi usada e ela não tiver permissões de leitura. Esse problema foi corrigido. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Cancelar uma transferência de AzCopy que não funcionou. Esse problema foi corrigido. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* O AzCopy falhará ao tentar fazer o download de uma pasta de um contêiner de Blob do ADLS Gen2, se a pasta tinha espaços em seu nome. Esse problema foi corrigido. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* O editor do CosmosDB foi interrompido no 1.6.0. Agora isso foi corrigido. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Novo

* Use agora o Gerenciador de Armazenamento para acessar seus dados de Blob por meio de [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se estiver conectado e o Gerenciador de Armazenamento não pôde recuperar as chaves para sua conta de armazenamento, em seguida, um token OAuth será usado para autenticar ao interagir com seus dados.
* O Gerenciador de Armazenamento agora dá suporte a contas de armazenamento do ADLS Gen2. Quando o Gerenciador de Armazenamento detecta que esse namespace hierárquico está habilitado para uma conta de armazenamento, "(ADLS Gen2 Preview )" irá aparecer ao lado do nome da sua conta de armazenamento. O Gerenciador de Armazenamento é capaz de detectar se o namespace hierárquico está habilitado quando você está ou não conectado, ou se tiver conectado a sua conta de armazenamento com o nome e a chave. Para contas de armazenamento do ADLS Gen2, pode-se usar o Gerenciador de Armazenamento:
  * Criar e excluir contêiner
  * Gerenciar propriedades do contêiner e permissões (lado esquerdo)
  * Exibir e navegar em dados dentro de contêineres
  * Criar novas pastas
  * Carregar, fazer o download, renomear e excluir arquivos e pastas
  * Gerencie propriedades de arquivo, pasta e permissões (lado direito).
    
    Outros recursos típicos de Blob, como a exclusão reversível e instantâneos, não estão disponíveis no momento. Gerenciando permissões também só está disponível quando conectado. Além disso, ao trabalhar em uma conta de armazenamento do ADLS Gen2, o Gerenciador de Armazenamento usará o AzCopy para todos os carregamentos, downloads e padrões usando o nome e as credenciais de chave para todas as operações, se disponível.
* Depois de comentários do usuário de alta segurança, interromper a concessão mais uma vez pode ser usado para interromper as concessões em vários blobs ao mesmo tempo.

### <a name="known-issues"></a>Problemas conhecidos

* Ao fazer o download de uma conta de armazenamento do ADLS Gen2 se um dos arquivos que estão sendo transferidos já existir, o AzCopy irá falhar algumas vezes. Isso será corrigido em um hotfix futuro.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, veja #537.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Versão 1.6.0
12/5/2018

### <a name="new"></a>Novo

* Use agora o Gerenciador de Armazenamento para acessar seus dados de Blob por meio de [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se estiver conectado e o Gerenciador de Armazenamento não pôde recuperar as chaves para sua conta de armazenamento, em seguida, um token OAuth será usado para autenticar ao interagir com seus dados.
* O Gerenciador de Armazenamento agora dá suporte a contas de armazenamento do ADLS Gen2. Quando o Gerenciador de Armazenamento detecta que esse namespace hierárquico está habilitado para uma conta de armazenamento, "(ADLS Gen2 Preview )" irá aparecer ao lado do nome da sua conta de armazenamento. O Gerenciador de Armazenamento é capaz de detectar se o namespace hierárquico está habilitado quando você está ou não conectado, ou se tiver conectado a sua conta de armazenamento com o nome e a chave. Para contas de armazenamento do ADLS Gen2, pode-se usar o Gerenciador de Armazenamento:
  * Criar e excluir contêiner
  * Gerenciar propriedades do contêiner e permissões (lado esquerdo)
  * Exibir e navegar em dados dentro de contêineres
  * Criar novas pastas
  * Carregar, fazer o download, renomear e excluir arquivos e pastas
  * Gerencie propriedades de arquivo, pasta e permissões (lado direito).
    
    Outros recursos típicos de Blob, como a exclusão reversível e instantâneos, não estão disponíveis no momento. Gerenciando permissões também só está disponível quando conectado. Além disso, ao trabalhar em uma conta de armazenamento do ADLS Gen2, o Gerenciador de Armazenamento usará o AzCopy para todos os carregamentos, downloads e padrões usando o nome e as credenciais de chave para todas as operações, se disponível.
* Depois de comentários do usuário de alta segurança, interromper a concessão mais uma vez pode ser usado para interromper as concessões em vários blobs ao mesmo tempo.

### <a name="known-issues"></a>Problemas conhecidos

* Ao fazer o download de uma conta de armazenamento do ADLS Gen2 se um dos arquivos que estão sendo transferidos já existir, o AzCopy irá falhar algumas vezes. Isso será corrigido em um hotfix futuro.
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, veja #537.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Versão 1.5.0
29/10/2018

### <a name="new"></a>Novo

* Agora você pode usar o [AzCopy v10 (Preview)](https://github.com/Azure/azure-storage-azcopy) para fazer o upload e o download de Blobs. Para ativar esse recurso, vá ao menu "Experimental" e clique em "Usar AzCopy para Melhorar Upload e Download do Blob". Quando ativado, o AzCopy será usado nos seguintes cenários:
   * Upload de pastas e arquivos para contêineres de blob, seja pela barra de ferramentas ou arrastando e soltando.
   * Download de pastas e arquivos, através da barra de ferramentas ou menu de contexto.

* Além disso, ao usar o AzCopy:
   * Você pode copiar o comando AzCopy usado para executar a transferência para sua área de transferência. Basta clicar em "Copiar AzCopy comando à área de transferência" no log de atividades.
   * Você precisará atualizar o editor de blob manualmente após o upload.
   * O upload de arquivos para anexar blobs não é suportado, os arquivos vhd serão enviados como blobs de páginas e todos os outros arquivos serão carregados como blobs de bloco.
   * Erros e conflitos que ocorrem durante o upload ou download não serão revelados até que um upload ou download seja concluído.

Por fim, o suporte para o uso do AzCopy com Compartilhamentos de Arquivos será disponibilizado no futuro.
* O Gerenciador de armazenamento agora está usando elétrons versão 2.0.11.
* Quebrar concessões agora só pode ser executado em um blob de cada vez. Além disso, você tem que digitar o nome do blob cujo aluguel você está quebrando. Essa alteração foi feita para reduzir a probabilidade de quebra acidental de uma concessão, especialmente para VMs. #394
* Se você encontrar problemas de login, poderá tentar redefinir a autenticação. Vá para o menu "Ajuda" e clique em "Redefinir" para acessar essa funcionalidade. #419

### <a name="fix"></a>Fix

* Após um forte feedback do usuário, o nó do emulador padrão foi reativado. Você ainda pode adicionar mais conexões de emulador por meio da caixa de diálogo Conectar, mas se o emulador estiver configurado para usar as portas padrão, você também poderá usar o nó "Emulator * Default Ports" em "Contas locais e anexadas / armazenamento". #669
* O Storage Explorer não permitirá mais que você defina valores de metadados de blob com espaço em branco à esquerda ou à direita. #760
* O botão "Entrar" sempre foi ativado nas mesmas páginas da caixa de diálogo Conectar. Agora ela é desabilitada quando apropriado. #761
* O Acesso Rápido não gerará mais um erro no console quando nenhum item de Acesso Rápido for adicionado.

### <a name="known-issues"></a>Problemas conhecidos

* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Para obter mais informações, veja #537.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se isso não desbloquear você, comente sobre este assunto.
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron. Para solucionar esse problema ao carregar ou fazer o download de um contêiner de blob, você pode usar o recurso experimental do AzCopy.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não suporta os seguintes recursos. A tentativa de usar esses recursos ao trabalhar com recursos do Azure Stack pode resultar em erros inesperados.
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Versão 1.4.4
15/10/2018

### <a name="hotfixes"></a>Hotfixes
* A versão da API do Gerenciamento de Recursos do Azure foi revertida para desbloquear usuários do Azure US Government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Controles giratórios de carregamento do agora estão usando animações CSS para reduzir o uso de GPU pelo Gerenciador de Armazenamento. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Anexos de recursos externos, como os de conexões SAS e emuladores, foram significativamente aprimorados. Agora você pode:
   * Personalizar o nome de exibição do recurso que você está anexando. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexar a vários emuladores locais usando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicionar recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Agora o Gerenciador de Armazenamento é compatível com a Exclusão reversível. Você pode:
   * Configurar uma política de Exclusão reversível clicando com o botão direito do mouse no nó de Contêineres de Blob da sua conta de armazenamento.
   * Exibir blobs excluídos de modo reversível no Editor de Blob, selecionando "Blobs ativos e excluídos" na lista suspensa próxima à barra de navegação.
   * Cancelar a exclusão de blobs com exclusão reversível.

### <a name="fixes"></a>Correções
* A ação "Definir configurações de CORS" não está mais disponível em contas de Armazenamento Premium porque as contas de Armazenamento Premium não são compatíveis com CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora há uma propriedade de Assinatura de Acesso Compartilhado para Serviços Anexados de SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Agora a ação "Definir a camada de acesso padrão" está disponível para contas do Armazenamento de Blob e GPV2 que foram fixadas no Acesso Rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Gerenciador de Armazenamento poderia falhar ao mostrar as contas de Armazenamento Clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou Azurite, você precisará tê-los a escutar conexões de suas portas padrão. Caso contrário, o Gerenciador de armazenamento não poderá se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se, ao fazer isso, você não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Versão 1.4.3
11/10/2018

### <a name="hotfixes"></a>Hotfixes
* A versão da API do Gerenciamento de Recursos do Azure foi revertida para desbloquear usuários do Azure US Government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Controles giratórios de carregamento do agora estão usando animações CSS para reduzir o uso de GPU pelo Gerenciador de Armazenamento. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Novo
* Anexos de recursos externos, como os de conexões SAS e emuladores, foram significativamente aprimorados. Agora você pode:
   * Personalizar o nome de exibição do recurso que você está anexando. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexar a vários emuladores locais usando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicionar recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Agora o Gerenciador de Armazenamento é compatível com a Exclusão reversível. Você pode:
   * Configurar uma política de Exclusão reversível clicando com o botão direito do mouse no nó de Contêineres de Blob da sua conta de armazenamento.
   * Exibir blobs excluídos de modo reversível no Editor de Blob, selecionando "Blobs ativos e excluídos" na lista suspensa próxima à barra de navegação.
   * Cancelar a exclusão de blobs com exclusão reversível.

### <a name="fixes"></a>Correções
* A ação "Definir configurações de CORS" não está mais disponível em contas de Armazenamento Premium porque as contas de Armazenamento Premium não são compatíveis com CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora há uma propriedade de Assinatura de Acesso Compartilhado para Serviços Anexados de SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Agora a ação "Definir a camada de acesso padrão" está disponível para contas do Armazenamento de Blob e GPV2 que foram fixadas no Acesso Rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Gerenciador de Armazenamento poderia falhar ao mostrar as contas de Armazenamento Clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou Azurite, você precisará tê-los a escutar conexões de suas portas padrão. Caso contrário, o Gerenciador de armazenamento não poderá se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se, ao fazer isso, você não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Versão 1.4.2
24/09/2018

### <a name="hotfixes"></a>Hotfixes
* Atualize a versão da API do Gerenciamento de Recursos do Azure para 2018-07-01, para adicionar compatibilidade com novos tipos de Contas de Armazenamento do Microsoft Azure. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Novo
* Anexos de recursos externos, como os de conexões SAS e emuladores, foram significativamente aprimorados. Agora você pode:
   * Personalizar o nome de exibição do recurso que você está anexando. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexar a vários emuladores locais usando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicionar recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Agora o Gerenciador de Armazenamento é compatível com a Exclusão reversível. Você pode:
   * Configurar uma política de Exclusão reversível clicando com o botão direito do mouse no nó de Contêineres de Blob da sua conta de armazenamento.
   * Exibir blobs excluídos de modo reversível no Editor de Blob, selecionando "Blobs ativos e excluídos" na lista suspensa próxima à barra de navegação.
   * Cancelar a exclusão de blobs com exclusão reversível.

### <a name="fixes"></a>Correções
* A ação "Definir configurações de CORS" não está mais disponível em contas de Armazenamento Premium porque as contas de Armazenamento Premium não são compatíveis com CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora há uma propriedade de Assinatura de Acesso Compartilhado para Serviços Anexados de SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Agora a ação "Definir a camada de acesso padrão" está disponível para contas do Armazenamento de Blob e GPV2 que foram fixadas no Acesso Rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Gerenciador de Armazenamento poderia falhar ao mostrar as contas de Armazenamento Clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou Azurite, você precisará tê-los a escutar conexões de suas portas padrão. Caso contrário, o Gerenciador de armazenamento não poderá se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se, ao fazer isso, você não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Versão 1.4.1
28/08/2018

### <a name="hotfixes"></a>Hotfixes
* Na primeira inicialização, o Gerenciador de Armazenamento não era capaz de gerar a chave usada para criptografar dados confidenciais. Isso causaria problemas ao usar o Acesso Rápido e anexação de recursos. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Se sua conta não exigia MFA para seu locatário inicial, mas exigia para alguns outros locatários, o Gerenciador de Armazenamento não seria capaz de listar assinaturas. Agora, após entrar com essa conta, o Gerenciador de Armazenamento solicitará que você insira novamente suas credenciais e executar a MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* O Gerenciador de Armazenamento não era capaz de anexar os recursos do Azure Alemanha e Azure do Governo dos EUA. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Se você entrasse em duas contas que tivessem o mesmo endereço de email, o Gerenciador de Armazenamento, às vezes, falharia ao mostrar seus recursos em modo de exibição de árvore. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Em computadores Windows mais lentos, a tela inicial poderia levar um tempo significativo para aparecer algumas vezes. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* A caixa de diálogo de conexão seria exibida mesmo que houvesse contas ou serviços anexados. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Novo
* Anexos de recursos externos, como os de conexões SAS e emuladores, foram significativamente aprimorados. Agora você pode:
   * Personalizar o nome de exibição do recurso que você está anexando. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anexar a vários emuladores locais usando portas diferentes. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Adicionar recursos anexados ao Acesso Rápido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Agora o Gerenciador de Armazenamento é compatível com a Exclusão reversível. Você pode:
   * Configurar uma política de Exclusão reversível clicando com o botão direito do mouse no nó de Contêineres de Blob da sua conta de armazenamento.
   * Exibir blobs excluídos de modo reversível no Editor de Blob, selecionando "Blobs ativos e excluídos" na lista suspensa próxima à barra de navegação.
   * Cancelar a exclusão de blobs com exclusão reversível.

### <a name="fixes"></a>Correções
* A ação "Definir configurações de CORS" não está mais disponível em contas de Armazenamento Premium porque as contas de Armazenamento Premium não são compatíveis com CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Agora há uma propriedade de Assinatura de Acesso Compartilhado para Serviços Anexados de SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Agora a ação "Definir a camada de acesso padrão" está disponível para contas do Armazenamento de Blob e GPV2 que foram fixadas no Acesso Rápido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Às vezes, o Gerenciador de Armazenamento poderia falhar ao mostrar as contas de Armazenamento Clássicas. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemas conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou Azurite, você precisará tê-los a escutar conexões de suas portas padrão. Caso contrário, o Gerenciador de armazenamento não poderá se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se, ao fazer isso, você não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Versão 1.3.0
09/07/2018

### <a name="new"></a>Novo
* Agora há suporte para acessar os contêineres $web usados por sites estáticos. Isso permite que você carregue e gerencie arquivos e pastas usadas pelo seu site com facilidade. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* A barra de aplicativos no macOS foi reorganizada. As alterações incluem um menu Arquivo, algumas alterações em teclas de atalho e vários novos comandos no menu do aplicativo. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* O ponto de extremidade de autoridade para entrar no Governo dos EUA para Azure foi alterado para https://login.microsoftonline.us/
* Acessibilidade: quando um leitor de tela está ativo, agora a navegação pelo teclado funciona com as tabelas usadas para exibir itens no lado direito. É possível usar as tecla de direção para navegar pelas linhas e colunas, Enter para invocar ações padrão, a tecla do menu de contexto para abrir o menu de contexto para um item e Shift ou Control para seleção múltipla. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Correções
*  Em alguns computadores, os processos filho estavam levando muito tempo para iniciar. Quando isso acontecer, um erro "falha na inicialização oportuna do processo filho" será exibido. O tempo alocado para um processo filho iniciar agora aumentou de 20 para 90 segundos. Se você ainda for afetado por esse problema, comente no problema do GitHub vinculado. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Ao usar uma SAS que não tinha permissões de leitura, não foi possível carregar um blob grande. A lógica do upload foi modificada para funcionar nesse cenário. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Definir o nível de acesso público para um contêiner removeria todas as políticas de acesso e vice-versa. Agora, o nível de acesso público e as políticas de acesso são preservados ao definir qualquer um dos dois. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" estava truncado na caixa de diálogo Propriedades. Esse problema foi corrigido. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* O prefixo "Gerenciador de Armazenamento do Microsoft Azure –" estava ausente na caixa de diálogo Criar novo diretório. Esse problema foi corrigido. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Acessibilidade: a caixa de diálogo Adicionar entidade era difícil navegar usando o VoiceOver. Foram feitas melhorias. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Acessibilidade: a cor da tela de fundo do botão recolher/expandir do painel Ações e Propriedades estava inconsistente com os controles da interface do usuário semelhantes no tema Preto em alto contraste. A cor mudou. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Acessibilidade: no tema Preto em alto contraste, o estilo do foco do botão "X" na caixa de diálogo Propriedades não estava visível. Esse problema foi corrigido. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Acessibilidade: as guias Ações e Propriedades com vários valores aria ausentes que resultavam em uma experiência de leitor de tela ruim. Agora os valores aria ausentes foram adicionados. [Nº 316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Acessibilidade: os três nós recolhidos do lado esquerdo não receberam um valor aria expandido de falso. Esse problema foi corrigido. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Problemas conhecidos
* Desanexar de um recurso anexado por meio de URI de SAS, como um contêiner de blob, pode causar um erro que impede que outros anexos apareçam corretamente. Para contornar esse problema, basta atualizar o nó do grupo. Consulte [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/537) para obter mais informações.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se, ao fazer isso, você não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* O Azure Stack não é compatível com os seguintes recursos, e tentar usá-los enquanto estiver trabalhando com o Azure Stack poderá resultar em erros inesperados:
   * Compartilhamentos de arquivo
   * Níveis de acesso
   * Exclusão reversível
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Versão 1.2.0
12/06/2018

### <a name="new"></a>Novo
* Se o Gerenciador de armazenamento não conseguir carregar assinaturas em apenas um subconjunto de seus locatários, em seguida, assinaturas carregadas com êxito serão mostradas junto com uma mensagem de erro especificamente para os locatários que falhou. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* No Windows, quando uma atualização está disponível, você agora pode optar por "Atualizar ao fechar". Quando essa opção é selecionada, o instalador da atualização será executado depois de fechar o Gerenciador de armazenamento. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Restaurar instantâneo foi adicionado ao menu de contexto do editor de compartilhamento de arquivo ao exibir um instantâneo de compartilhamento de arquivo. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Botão Limpar fila está sempre habilitado. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Suporte para entrar pilha do ADFS do Azure foi habilitado novamente. A pilha do Azure versão 1804 ou superior é necessária. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Correções
* Se você exibir instantâneos para um compartilhamento de arquivo cujo nome é um prefixo de outro compartilhamento de arquivos na mesma conta de armazenamento, os instantâneos do compartilhamento de arquivos também seriam listados. Esse problema foi corrigido. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Quando conectado por meio de SAS, a restauração de um arquivo de um instantâneo de compartilhamento de arquivo resulta em um erro. Esse problema foi corrigido. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Ao exibir instantâneos para um blob, a ação de promover o instantâneo foi habilitada quando o blob de base e um único instantâneo foram selecionados. A ação está ativada somente se um único instantâneo estiver selecionado. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Se um único trabalho (como baixar um blob) foi iniciado e posteriormente falha, ele não irá automaticamente novamente até o início do outro trabalho do mesmo tipo. Todos os trabalhos agora devem automaticamente tentar de novo, independentemente de quantos trabalhos foram colocados em fila.
* Editores abertos para novos contêineres de blob criados no GPV2 e contas de armazenamento de Blob não tem uma coluna de nível de acesso. Esse problema foi corrigido. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Uma coluna de nível de acesso, às vezes, não aparece quando uma conta de armazenamento ou o contêiner de blob foi conectado via SAS. A coluna será sempre mostrada, mas com um valor vazio se não houver nenhum conjunto de camada de acesso. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Definir o nível de acesso de um blob de bloco carregado recentemente foi desabilitado. Esse problema foi corrigido. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Se o botão “Manter Guia Aberta" foi chamado usando o teclado, o foco do teclado seria perdido. Agora, o foco será movido para a guia que foi mantida aberta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Para uma consulta no Construtor de Consultas, VoiceOver não estava dando uma descrição utilizável do operador atual. Agora é mais descritivo. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Os links de paginação para os vários editores não são descritivos. Eles foram alterados para serem mais descritivos. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Na caixa de diálogo Adicionar Entidade, VoiceOver não foi anunciando de que coluna um elemento de entrada fazia parte. O nome da coluna atual agora está incluído na descrição do elemento. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Caixas de seleção e botões de opção não tem uma borda visível quando focalizados. Esse problema foi corrigido. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Problemas conhecidos
* Ao usar emuladores, como o emulador de armazenamento do Azure ou Azurite, você precisará tê-los a escutar conexões de suas portas padrão. Caso contrário, o Gerenciador de armazenamento não poderá se conectar a eles.
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se, ao fazer isso, você não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Version 1.1.0
09/05/2018

### <a name="new"></a>Novo
* Agora o Gerenciador de Armazenamento é compatível com o uso de Azurite. Observação: a conexão com o Azurite é fixa para os pontos de extremidade de desenvolvimento padrão.
* Agora o Gerenciador de Armazenamento é compatível com Camadas de Acesso para Contas de armazenamento GPV2 e de Blobs apenas. Saiba mais sobre as camadas de acesso [aqui](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Uma hora de início não é mais necessária ao gerar uma SAS.

### <a name="fixes"></a>Correções
* A recuperação de assinaturas para contas do Governo dos EUA foi interrompida. Esse problema foi corrigido. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* O tempo de expiração para políticas de acesso não estava sendo salvo corretamente. Esse problema foi corrigido. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Ao gerar uma URL de SAS para um item em um contêiner, o nome do item não estava sendo acrescentado à URL. Esse problema foi corrigido. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Ao criar uma SAS, os tempos de expiração que estão no passado, às vezes, seriam o valor padrão. Isso ocorria porque o Gerenciador de Armazenamento usava o horário de início e de expiração usado pela última vez como valores padrão. Agora, sempre que você abrir a caixa de diálogo de SAS, um novo conjunto de valores padrão é gerado. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Ao copiar entre contas de armazenamento, será gerado uma SAS de 24 horas. Se a cópia durasse mais do que 24 horas, então ela falharia. Aumentamos as SASs para durarem 1 semana a fim de reduzir a chance de uma falha na cópia devido a uma SAS expirada. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Para algumas atividades, clicar em "Cancelar" nem sempre funcionaria. Esse problema foi corrigido. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Para algumas atividades, a velocidade de transferência estava incorreta. Esse problema foi corrigido. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* A ortografia de "Anterior" no menu Exibir estava incorreta. Agora está escrito corretamente. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* A página final do Windows Installer tinha um botão "Avançar". Ele foi alterado para um botão "Concluir". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* O foco da guia não estava visível para botões em caixas de diálogo ao usar o tema Preto HC. Ele agora está visível. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* As maiúsculas e minúsculas de "Resolver automaticamente" para ações no log de atividades estava errado. Agora isso está correto. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Ao excluir uma entidade de uma tabela, a caixa de diálogo que solicita sua confirmação exibia um ícone de erro. Agora a caixa de diálogo usa um ícone de aviso. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Problemas conhecidos
* Se você usar o VS para Mac e já tiver criado uma configuração do AAD personalizada, talvez não consiga se conectar. Para contornar o problema, exclua o conteúdo de ~/.IdentityService/AadConfigurations. Se, ao fazer isso, você não for desbloqueado, comente sobre [esse problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* O Azurite ainda não implementou totalmente todas as APIs de Armazenamento. Por causa disso, pode haver um comportamento ou erros inesperados ao usar o Azurite para armazenamento de desenvolvimento.
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Carregar da sua pasta do OneDrive não funciona por causa de um bug no NodeJS. O bug foi corrigido, mas ainda não foi integrado ao Electron.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versão 1.0.0
16/04/2018

### <a name="new"></a>Novo
* Autenticação aprimorada que permite que o Gerenciador de Armazenamento use o mesmo repositório de contas que o Visual Studio 2017. Para usar esse recurso, você precisará fazer logon novamente em suas contas e definir novamente as assinaturas filtradas.
* Para contas do Azure Stack com suporte do AAD, o Gerenciador de Armazenamento agora recupera as assinaturas do Azure Stack quando “Azure Stack de Destino” é habilitado. Você não precisa mais criar um ambiente de logon personalizado.
* Vários atalhos foram adicionados para permitir a navegação mais rápida. Eles incluem a alternância entre vários painéis e o movimento entre editores. Consulte o Menu Exibir para obter mais detalhes.
* Os comentários do Gerenciador de Armazenamento agora ficam no GitHub. Você pode acessar nossa página de problemas clicando no botão de comentários na parte inferior esquerda ou acessando [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) . Fique à vontade para fazer sugestões, relatar problemas, fazer perguntas ou deixar qualquer outro tipo de comentário.
* Se você estiver executando problemas de certificado TLS/SSL e não conseguir localizar o certificado transgressor, agora poderá iniciar o Gerenciador de Armazenamento na linha de comando com o `--ignore-certificate-errors` sinalizador. Quando iniciado com esse sinalizador, Gerenciador de Armazenamento ignorará os erros de certificado TLS/SSL.
* Agora há uma opção de 'Download' no menu de contexto para itens de arquivo e blob.
* Acessibilidade aprimorada e suporte para leitor de tela. Se você usa recursos de acessibilidade, confira nossa [documentação de acessibilidade](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) para obter mais informações.
* O Gerenciador de Armazenamento agora usa o Electron 1.8.3

### <a name="breaking-changes"></a>Alterações de quebra
* O Gerenciador de Armazenamento passou a usar uma nova biblioteca de autenticação. Como parte da mudança para a biblioteca, você precisará fazer logon novamente em suas contas e definir novamente as assinaturas filtradas
* O método usado para criptografar dados confidenciais foi alterado. Isso pode fazer com que alguns de seus itens de Acesso Rápido precisem ser adicionados novamente e/ou que alguns de seus recursos anexados precisam ser anexados novamente.

### <a name="fixes"></a>Correções
* Alguns usuários por trás de proxies tinham uploads ou downloads de blobs em grupo interrompidos por uma mensagem de erro “Não é possível resolver”. Esse problema foi corrigido.
* Se fosse necessário se conectar usando um link direto, clicar no prompt “Entrar” faria surgir uma caixa de diálogo em branco. Esse problema foi corrigido.
* No Linux, se o Gerenciador de Armazenamento não puder ser iniciado devido a uma falha no processo de GPU, você será informado da falha, instruído a usar a opção '--disable-gpu' e o Gerenciador de Armazenamento será reiniciado automaticamente com a opção habilitada.
* Políticas de acesso inválidas eram difíceis de identificar na caixa de diálogo Políticas de Acesso. IDs de política de acesso inválidas agora são realçadas em vermelho para melhorar a visibilidade.
* O log de atividades, às vezes, tinha grandes áreas de espaço em branco entre as diferentes partes de uma atividade. Esse problema foi corrigido.
* No editor de consulta de tabela, se você deixasse uma cláusula de carimbo de data/hora em um estado inválido e, em seguida, tentasse modificar outra cláusula, o editor congelaria. Agora, o editor restaura a cláusula de carimbo de data/hora para seu último estado válido quando uma alteração em outra cláusula é detectada.
* Se você pausasse ao digitar sua consulta de pesquisa no modo de exibição de árvore, a pesquisa começaria e foco seria tirado da caixa de texto. Agora, você precisa começar a pesquisar explicitamente pressionando a tecla “Enter” ou clicando no botão Iniciar pesquisa.
* Às vezes, o comando 'Obter Assinatura de Acesso Compartilhado' era desabilitado quando você clicava com o botão direito do mouse em um arquivo em um compartilhamento de arquivos. Esse problema foi corrigido.
* Se o nó da árvore de recursos com foco fosse filtrado durante a pesquisa, você não consegueria acessar a árvore de recursos nem usar as teclas de direção para navegar nela. Agora, se o nó da árvore de recursos em foco estiver oculto, o primeiro nó da árvore de recursos será focado automaticamente.
* Às vezes, um separador extra ficava visível na barra de ferramentas do editor. Esse problema foi corrigido.
* Às vezes, a caixa de texto de trilha estourava. Esse problema foi corrigido.
* Às vezes, os editores de Compartilhamento de Arquivos e Blob eram atualizados constantemente ao carregar muitos arquivos por vez. Esse problema foi corrigido.
* O recurso 'Estatísticas de Pasta' não tinha nenhuma finalidade no modo de exibição de Gerenciamento de Instantâneos do Compartilhamento de Arquivos. Ele foi desabilitado.
* No Linux, o menu Arquivo não era exibido. Esse problema foi corrigido.
* Ao carregar uma pasta em um Compartilhamento de Arquivos, por padrão, somente o conteúdo da pasta era carregado. Agora, o comportamento padrão é carregar o conteúdo da pasta em uma pasta correspondente no Compartilhamento de Arquivos.
* A ordem dos botões em várias caixas de diálogo foi revertida. Esse problema foi corrigido.
* Diversas correções relacionadas à segurança.

### <a name="known-issues"></a>Problemas conhecidos
* Em casos raros, o foco da árvore pode ficar preso no Acesso Rápido. Para liberar o foco, você pode Atualizar Tudo.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários do Linux, você precisará instalar o [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Versão 0.9.6
28/02/2018

### <a name="fixes"></a>Correções
* Um problema impediu que os blobs/arquivos esperados fossem listados no editor. Esse problema foi corrigido.
* Um problema causou a alternância entre as exibições de instantâneos para exibir itens incorretamente. Esse problema foi corrigido.

### <a name="known-issues"></a>Problemas conhecidos
* O Gerenciador de Armazenamento não dá suporte a contas do AD FS.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita [aqui](https://github.com/Azure/azure-storage-node/issues/317).
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Versão 0.9.5
06/02/2018

### <a name="new"></a>Novo

* Suporte para instantâneos de Compartilhamentos de Arquivos:
    * Crie e gerencie instantâneos para seus Compartilhamentos de Arquivos.
    * Alterne entre os modos de exibição dos instantâneos de seus compartilhamentos de arquivos enquanto os explora.
    * Restaure versões anteriores de seus arquivos.
* Versão prévia do suporte ao Azure Data Lake Store:
    * Conecte-se aos recursos do ADLS em várias contas.
    * Conecte-se e compartilhe recursos do ADLS usando URIs do ADL.
    * Execute operações básicas de arquivo/pasta recursivamente.
    * Fixe pastas individuais no Acesso Rápido.
    * Exiba estatísticas de pasta.

### <a name="fixes"></a>Correções
* Melhorias de desempenho da inicialização.
* Várias correções de bugs.

### <a name="known-issues"></a>Problemas conhecidos
* O Gerenciador de Armazenamento não dá suporte a contas do AD FS.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versões 0.9.4 e 0.9.3
21/01/2018

### <a name="new"></a>Novo
* Sua janela existente do Gerenciador de Armazenamento do Azure será reutilizada quando:
    * Abrir links diretos gerados no Gerenciador de Armazenamento.
    * Abrir o Gerenciador de Armazenamento a partir do portal.
    * Abrir o Gerenciador de Armazenamento do Microsoft Azure VS Armazenamento da extensão de código (em breve).
* A capacidade adicional de abrir uma nova janela do Gerenciador de Armazenamento de dentro do Gerenciador de armazenamento.
    * Para o Windows, há uma opção 'Nova janela' sob o Menu Arquivo e no menu de contexto da barra de tarefas.
    * Para o Mac, há uma opção 'Nova janela' sob o Menu Aplicativo.

### <a name="fixes"></a>Correções
* Corrigido um problema de segurança. Atualize para 0.9.4 o mais rápido possível.
* Atividades antigas estavam sendo limpas adequadamente. Isso afetou o desempenho dos trabalhos de longa execução. Agora, elas estão sendo limpas corretamente.
* Ações que envolvem grandes números de arquivos e diretórios podem causar, ocasionalmente, o congelamento do Gerenciador de Armazenamento. As solicitações para o Azure para compartilhamentos de arquivos agora são estranguladas para limitar o uso de recursos do sistema.

### <a name="known-issues"></a>Problemas conhecidos
* O Gerenciador de Armazenamento não dá suporte a contas do AD FS.
* As teclas de atalho para a “Exibir Gerenciador” e “Exibir Gerenciamento de Conta” deverão ser Ctrl/Cmd+Shift+E e Ctrl/Cmd+Shift+A, respectivamente.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Versão 0.9.2
11/01/2017

### <a name="hotfixes"></a>Hotfixes
* Alterações de dados inesperadas eram possíveis ao editar valores de Edm.DateTime para entidades de tabela, dependendo do fuso horário local. O editor agora usa uma caixa de texto sem formatação, oferecendo controle preciso e consistente sobre valores Edm.DateTime.
* O upload/download de um grupo de blobs quando anexado com o nome e a chave não inicia. Esse problema foi corrigido.
* Anteriormente, o Gerenciador de Armazenamento somente solicitaria que você autenticasse novamente uma conta obsoleta se uma ou mais assinaturas da conta estivesse selecionada. Agora, o Gerenciador de Armazenamento solicitará que você faça isso mesmo que a conta esteja totalmente filtrada.
* O domínio de pontos de extremidade para o Azure para o Governo dos EUA estava incorreto. Esse problema foi corrigido.
* O botão Aplicar no painel de Gerenciar Contas às vezes era difícil de clicar. Isso não deve mais ocorrer.

### <a name="new"></a>Novo
* Suporte da versão prévia para Azure Cosmos DB:
    * [Documentação online](./cosmos-db/storage-explorer.md)
    * Criar bancos de dados e coleções
    * Manipular dados
    * Consultar, criar ou excluir documentos
    * Atualizar procedimentos armazenados, funções definidas pelo usuário ou gatilhos
    * Usar cadeias de conexão para se conectar e gerenciar seus bancos de dados
* Melhoria no desempenho de upload/download de muitos blobs pequenos.
* Foi adicionada uma ação "Tentar tudo novamente" em caso de falhas em um grupo de upload de blobs ou o grupo de download de blobs.
* O Gerenciador de Armazenamento agora pausará a iteração durante o upload/download de blobs se ele detectar que sua conexão de rede foi perdida. Você poderá retomar a iteração posteriormente depois que a conexão de rede for restabelecida.
* Foi adicionada a capacidade de “Fechar tudo”, “Fechar outra” e “Fechar” guias por meio do menu de contexto.
* O Gerenciador de Armazenamento agora usa caixas de diálogo e menus de contexto nativos.
* O Gerenciador de Armazenamento agora está mais acessível. As melhorias incluem:
    * Melhoria no suporte ao leitor de tela, para NVDA no Windows e para VoiceOver no Mac
    * Melhoria nos temas de alto contraste
    * Correções na tabulação e foco do teclado

### <a name="fixes"></a>Correções
* Se você tentasse abrir ou baixar um blob com um nome de arquivo inválido do Windows, essa operação falharia. O Gerenciador de Armazenamento agora detecta se um nome de blob é inválido e pergunta se você deseja codificá-lo ou ignorar o blob. O Gerenciador de Armazenamento também detecta se um nome de arquivo parece ser codificado e pergunta se você deseja decodificá-lo antes do carregamento.
* Durante o upload de blob, o editor do contêiner de blobs de destino não era atualizado corretamente durante o upload de blobs. Esse problema foi corrigido.
* O suporte para várias formas de cadeias de conexão e URIs de SAS voltou. Resolvemos todos os problemas conhecidos, mas envie-nos comentários se você encontrar outros problemas.
* A notificação de atualização foi interrompida para alguns usuários em 0.9.0. Esse problema foi corrigido e, para aqueles afetados pelo bug, é possível baixar manualmente a versão mais recente do Gerenciador de Armazenamento [aqui](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemas conhecidos
* O Gerenciador de Armazenamento não dá suporte a contas do AD FS.
* As teclas de atalho para a “Exibir Gerenciador” e “Exibir Gerenciamento de Conta” deverão ser Ctrl/Cmd+Shift+E e Ctrl/Cmd+Shift+A, respectivamente.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versões 0.9.1 e 0.9.0
10/20/2017
### <a name="new"></a>Novo
* Suporte da versão prévia para Azure Cosmos DB:
    * [Documentação online](./cosmos-db/storage-explorer.md)
    * Criar bancos de dados e coleções
    * Manipular dados
    * Consultar, criar ou excluir documentos
    * Atualizar procedimentos armazenados, funções definidas pelo usuário ou gatilhos
    * Usar cadeias de conexão para se conectar e gerenciar seus bancos de dados
* Melhoria no desempenho de upload/download de muitos blobs pequenos.
* Foi adicionada uma ação "Tentar tudo novamente" em caso de falhas em um grupo de upload de blobs ou o grupo de download de blobs.
* O Gerenciador de Armazenamento agora pausará a iteração durante o upload/download de blobs se ele detectar que sua conexão de rede foi perdida. Você poderá retomar a iteração posteriormente depois que a conexão de rede for restabelecida.
* Foi adicionada a capacidade de “Fechar tudo”, “Fechar outra” e “Fechar” guias por meio do menu de contexto.
* O Gerenciador de Armazenamento agora usa caixas de diálogo e menus de contexto nativos.
* O Gerenciador de Armazenamento agora está mais acessível. As melhorias incluem:
    * Melhoria no suporte ao leitor de tela, para NVDA no Windows e para VoiceOver no Mac
    * Melhoria nos temas de alto contraste
    * Correções na tabulação e foco do teclado

### <a name="fixes"></a>Correções
* Se você tentasse abrir ou baixar um blob com um nome de arquivo inválido do Windows, essa operação falharia. O Gerenciador de Armazenamento agora detecta se um nome de blob é inválido e pergunta se você deseja codificá-lo ou ignorar o blob. O Gerenciador de Armazenamento também detecta se um nome de arquivo parece ser codificado e pergunta se você deseja decodificá-lo antes do carregamento.
* Durante o upload de blob, o editor do contêiner de blobs de destino não era atualizado corretamente durante o upload de blobs. Esse problema foi corrigido.
* O suporte para várias formas de cadeias de conexão e URIs de SAS voltou. Resolvemos todos os problemas conhecidos, mas envie-nos comentários se você encontrar outros problemas.
* A notificação de atualização foi interrompida para alguns usuários em 0.9.0. Esse problema foi corrigido e, para aqueles afetados pelo Bug, você pode baixar manualmente a versão mais recente do Gerenciador de Armazenamento [aqui](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Problemas conhecidos
* O Gerenciador de Armazenamento não dá suporte a contas do AD FS.
* As teclas de atalho para a “Exibir Gerenciador” e “Exibir Gerenciamento de Conta” deverão ser Ctrl/Cmd+Shift+E e Ctrl/Cmd+Shift+A, respectivamente.
* Pode ocorrer uma falha ao carregar certos arquivos como blobs acrescentados durante o direcionamento para o Azure Stack.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Isso ocorre porque estamos usando a solução alternativa de filtro de cancelamento descrita aqui.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* O shell Electron usado pelo Gerenciador de Armazenamento tem conflitos com a aceleração de hardware de algumas GPUs (unidade de processamento gráfico). Se o Gerenciador de Armazenamento estiver exibindo uma janela principal em banco (vazia), experimente iniciar o Gerenciador de Armazenamento na linha de comando e desabilitar a aceleração de GPU adicionando a opção `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Versão 0.8.16
8/21/2017

### <a name="new"></a>Novo
* Quando você abre um blob, o Gerenciador de Armazenamento solicitará que você carregue o arquivo baixado, se uma mudança for detectada
* Experiência de aprimorada de conexão no Azure Stack
* Melhoria no desempenho de upload/download de muitos arquivos pequenos ao mesmo tempo


### <a name="fixes"></a>Correções
* Para alguns tipos de blob, escolher "substituir" durante um conflito de upload às vezes resultava no reinício do upload.
* Na versão 0.8.15, os uploads às vezes paralisavam em 99%.
* Ao carregar arquivos em um compartilhamento de arquivos, se você optasse por carregar em um diretório que não ainda não existia, o upload falhava.
* O Gerenciador de Armazenamento estava gerando incorretamente os carimbos de data/hora para assinaturas de acesso compartilhadas e consultas de tabela.


### <a name="known-issues"></a>Problemas conhecidos
* Usar uma cadeia de conexão de nome e chave não funciona atualmente. Isso será corrigido na próxima versão. Até lá, você pode usar anexar com o nome e chave.
* Se você tentar abrir um arquivo com um nome de arquivo inválido do Windows, o download resultará em um erro de Arquivo não encontrado.
* Depois de clicar em "Cancelar" em uma tarefa, talvez demore algum tempo para a tarefa ser cancelada. Esta é uma limitação da biblioteca de Nós de Armazenamento do Azure.
* Depois de concluir o carregamento de um blob, a guia que iniciou o carregamento é atualizada. Isso é uma alteração do comportamento anterior, e também levará você de volta à raiz do contêiner no qual você está.
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão.
* O painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* Para usuários no Ubuntu 14.04, será necessário verificar se o GCC está atualizado – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para usuários no Ubuntu 17.04, será necessário instalar o GConf – isso pode ser feito executando os comandos a seguir e, depois, reiniciando seu computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versão 0.8.14
06/22/2017

### <a name="new"></a>Novo

* Versão do Electron atualizada para 1.7.2, a fim de aproveitar as várias atualizações de segurança críticas
* Agora você pode acessar rapidamente o guia de solução de problemas online no menu de ajuda
* [Guia][2] de solução de problemas do Gerenciador de Armazenamento
* [Instruções][3] sobre como se conectar a uma assinatura do Azure Stack

### <a name="known-issues"></a>Problemas conhecidos

* Os botões na caixa de diálogo de confirmação de exclusão de pasta não registram com os cliques do mouse no Linux. A solução alternativa é usar a tecla Enter
* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros
* Talvez o painel de configurações da conta mostre que você precisa reinserir as credenciais a fim de filtrar as assinaturas
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* A instalação do Ubuntu 14.04 precisa da versão de gcc atualizada ou com upgrade – veja abaixo as etapas para o upgrade:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versão 0.8.13
05/12/2017

#### <a name="new"></a>Novo

* [Guia][2] de solução de problemas do Gerenciador de Armazenamento
* [Instruções][3] sobre como se conectar a uma assinatura do Azure Stack

#### <a name="fixes"></a>Correções

* Corrigido: o carregamento do arquivo tinha uma chance maior de causar um erro de falta de memória
* Corrigido: agora você pode entrar com PIN/Cartão inteligente
* Corrigido: abrir no Portal agora funciona com o Azure China 21Vianet, o Azure Alemanha, o governo dos EUA do Azure e o Azure Stack
* Corrigido: às vezes, durante o carregamento de uma pasta em um contêiner de blob, um erro "Operação ilegal" ocorria
* Corrigido: selecionar tudo foi desabilitado ao gerenciar instantâneos
* Corrigido: os metadados do blob de base podem ser substituídos após a exibição das propriedades de seus instantâneos

#### <a name="known-issues"></a>Problemas conhecidos

* Se você escolher o PIN/Certificado de cartão inteligente incorreto, será necessário reiniciar para que o Gerenciador de Armazenamento se esqueça dessa decisão
* Durante a ampliação ou a redução do zoom, o nível do zoom pode ser redefinido momentaneamente para o nível padrão
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros
* Talvez o painel de configurações da conta mostre que você precisa reinserir as credenciais a fim de filtrar as assinaturas
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* A instalação do Ubuntu 14.04 precisa da versão de gcc atualizada ou com upgrade – veja abaixo as etapas para o upgrade:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versões 0.8.12 e 0.8.11 e 0.8.10
04/07/2017

#### <a name="new"></a>Novo

* Agora, o Gerenciador de Armazenamento será fechado automaticamente quando você instalar uma atualização pela notificação de atualização
* O acesso rápido in-loco fornece uma experiência aprimorada para trabalhar com seus recursos acessados com frequência
* Agora, no editor de Contêiner de Blob, você pode ver um à qual máquina virtual um blob concedido pertence
* Agora você pode recolher o painel do lado esquerdo
* Agora, a Descoberta é executada ao mesmo tempo que o download
* Usar Estatísticas nos editores de Contêiner de Blob, Compartilhamento de Arquivos e de Tabela para ver o tamanho de seus recursos ou seleção
* Agora, você pode entrar em contas do Azure Stack com base no AAD (Azure Active Directory).
* Agora você pode carregar arquivos com mais de 32 MB em contas de armazenamento Premium
* Suporte a acessibilidade aprimorado
* Agora você pode adicionar certificados de TLS/SSL codificados em base de 64 confiáveis, acessando Edit- &gt; SSL Certificates- &gt; Import Certificates

#### <a name="fixes"></a>Correções

* Corrigido: depois de atualizar as credenciais de uma conta, às vezes o modo de exibição de árvore não atualizava automaticamente
* Corrigido: a geração de um SAS para tabelas e filas do emulador poderia resultar em uma URL inválida
* Corrigido: agora, as contas de armazenamento Premium podem ser expandidas enquanto um proxy está habilitado
* Corrigido: o botão aplicar na página de gerenciamento de contas não funcionava se você tivesse uma ou nenhuma conta selecionada
* Corrigido: o carregamento de blobs que exigem resoluções de conflitos pode falhar – corrigido no 0.8.11
* Corrigido: o envio de comentários foi interrompido no 0.8.11 – corrigido no 0.8.12

#### <a name="known-issues"></a>Problemas conhecidos

* Após a atualização para 0.8.10, você precisará atualizar todas as suas credenciais.
* Durante a ampliação ou redução do zoom, o nível do zoom pode ser redefinido momentaneamente para o nível padrão.
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros.
* Talvez o painel de configurações da conta mostre que você precisa reinserir as credenciais a fim de filtrar as assinaturas.
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação.
* Embora o Azure Stack não dê suporte no momento a Compartilhamentos de Arquivos, um nó de Compartilhamentos de Arquivos ainda aparece em uma conta de armazenamento do Azure Stack anexada.
* A instalação do Ubuntu 14.04 precisa da versão de gcc atualizada ou com upgrade – veja abaixo as etapas para o upgrade:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Versões 0.8.9 e 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Novo

* O Gerenciador de Armazenamento 0.8.9 baixará automaticamente a versão mais recente das atualizações.
* Hotfix: usar um URI SAS gerado pelo portal para anexar uma conta de armazenamento resultaria em um erro.
* Agora você pode criar, gerenciar e promover os instantâneos de blob.
* Agora você pode entrar em contas do Azure China 21Vianet, Azure Alemanha e o Azure US Government.
* Agora você pode alterar o nível de zoom. Use as opções no menu Exibir para Ampliar, Reduzir e Redefinir o Zoom.
* Agora há suporte para caracteres Unicode nos metadados do usuário para blobs e arquivos.
* Aprimoramentos na acessibilidade.
* As notas de versão da próxima versão podem ser exibidas na notificação da atualização. Você também pode exibir as notas de versão atuais no menu Ajuda.

#### <a name="fixes"></a>Correções

* Corrigido: agora, o número da versão é exibido corretamente no Painel de Controle do Windows
* Corrigido: a pesquisa não está mais limitada a 50.000 nós
* Corrigido: o carregamento em um compartilhamento de arquivos continuava indefinidamente se o diretório de destino ainda não existisse
* Corrigido: mais estabilidade para downloads e uploads longos

#### <a name="known-issues"></a>Problemas conhecidos

* Durante a ampliação ou redução do zoom, o nível do zoom pode ser redefinido momentaneamente para o nível padrão.
* O Acesso Rápido só funciona com itens baseados em assinatura. Recursos locais ou conectados por meio de chave ou o token SAS de recursos não têm suporte nesta versão.
* Acesso rápido pode demorar um pouco para navegar para o recurso de destino, dependendo de quantos recursos.
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros.

12/16/2016
### <a name="version-087"></a>Versão 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Novo

* Você pode escolher como resolver conflitos no início de uma atualização, download ou sessão de cópia na janela Atividades
* Passe o mouse sobre uma guia para ver o caminho completo do recurso de armazenamento
* Quando você clica em uma guia, sincronizar com seu local no painel de navegação do lado esquerdo

#### <a name="fixes"></a>Correções

* Corrigido: agora, o Gerenciador de Armazenamento é um aplicativo confiável no Mac
* Corrigido: o Ubuntu 14.04 tem suporte novamente
* Corrigido: às vezes, a interface do usuário de adição de conta pisca ao carregar assinaturas
* Corrigido: às vezes, nem todos os recursos de armazenamento foram listados no painel de navegação do lado esquerdo
* Corrigido: o painel de ações, às vezes, exibido ações vazias
* Corrigido: o tamanho da janela da última sessão fechada agora é retido
* Corrigido: você pode abrir várias guias para o mesmo recurso usando o menu de contexto

#### <a name="known-issues"></a>Problemas conhecidos

* O Acesso Rápido só funciona com itens baseados em assinatura. Recursos locais ou conectados por meio de chave ou o token SAS de recursos não têm suporte nesta versão
* Acesso rápido pode demorar um pouco para navegar para o recurso de destino, dependendo de quantos recursos
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros
* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 – depois disso, o desempenho pode ser afetado ou pode causar exceções sem tratamento
* Pela primeira vez usando o Gerenciador de armazenamento em macOS, você poderá ver vários prompts solicitando permissão do usuário acessar o conjunto de chaves. Sugerimos que você selecione Permitir Sempre para que o prompt não seja exibido novamente

11/18/2016
### <a name="version-086"></a>Versão 0.8.6

#### <a name="new"></a>Novo

* Você pode agora pin usados com mais frequência serviços para o acesso rápido para facilitar a navegação
* Agora você pode abrir vários editores em guias diferentes. Clique único para abrir uma guia temporária; Clique duas vezes para abrir uma guia permanente. Você também pode clicar na guia temporária para torná-la uma guia permanente
* Fizemos aperfeiçoamentos de desempenho e estabilidade para carregamentos e downloads, especialmente para grandes arquivos em máquinas rápidas
* Pastas "virtuais" vazias agora podem ser criadas em contêineres de blob
* Introduzimos novamente a pesquisa com escopo com nossa nova pesquisa avançada de subcadeia de caracteres, e agora você tem duas opções de pesquisa:
    * Pesquisa global – basta inserir um termo de pesquisa na caixa de texto de pesquisa
    * Pesquisa com escopo – clique no ícone de lupa ao lado de um nó, adicione um termo de pesquisa ao final do caminho, ou clique com o botão direito e selecione "Pesquisar aqui"
* Adicionamos vários temas: luz (padrão), escuro, preto em alto contraste e branco em alto contraste. Acesse Editar –&gt; Temas para alterar sua preferência de temas
* Você pode modificar as propriedades do Blob e do arquivo
* Agora, damos suporte mensagens em fila codificadas (base64) e não codificadas
* No Linux, um sistema operacional de 64 bits agora é necessário. Para esta versão, só há suporte para Ubuntu 16.04.1 LTS de 64 bits
* Atualizamos nosso logotipo!

#### <a name="fixes"></a>Correções

* Corrigido: Tela problemas congelamento
* Corrigido: Segurança aprimorada
* Corrigido: às vezes, contas anexadas duplicadas apareciam
* Corrigido: um blob com um tipo de conteúdo indefinido pode gerar uma exceção
* Corrigido: a abertura do Painel de Consulta em uma tabela vazia não era possível
* Corrigido: vários bugs na Pesquisa
* Corrigido: aumento do número de recursos carregados de 50 para 100 ao clicar em "Carregar Mais"
* Corrigido: agora, na primeira execução, se uma conta for conectada, selecionamos todas as assinaturas para essa conta por padrão

#### <a name="known-issues"></a>Problemas conhecidos

* Esta versão do Gerenciador de Armazenamento não é executada no Ubuntu 14.04
* Para abrir várias guias para o mesmo recurso, não clique continuamente no mesmo recurso. Clique em outro recurso e, depois, volte e clique no recurso original para abri-lo novamente em outra guia
* O Acesso Rápido só funciona com itens baseados em assinatura. Recursos locais ou conectados por meio de chave ou o token SAS de recursos não têm suporte nesta versão
* Acesso rápido pode demorar um pouco para navegar para o recurso de destino, dependendo de quantos recursos
* Ter mais de três grupos de blobs ou arquivos carregando ao mesmo tempo pode causar erros
* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 – depois disso, o desempenho pode ser afetado ou pode causar exceções sem tratamento

03/10/2016
### <a name="version-085"></a>Versão 0.8.5

#### <a name="new"></a>Novo

* Agora pode usar as chaves SAS geradas pelo Portal para anexar a Contas de Armazenamento e recursos

#### <a name="fixes"></a>Correções

* Corrigido: às vezes, a condição de corrida durante a pesquisa causava a não expansão dos nós
* Corrigido: "Usar HTTP" não funciona ao conectar-se às Contas de Armazenamento com a chave e o nome da conta
* Corrigido: chaves SAS (especialmente as geradas pelo Portal) retornam um erro de "barra à direita"
* Corrigido: problemas de importação de tabela
    * Às vezes, a chave de partição e chave de linha eram invertidas
    * Não é possível ler as Chaves de Partição "nulas"

#### <a name="known-issues"></a>Problemas conhecidos

* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 – depois disso, o desempenho pode ser afetado
* O Azure Stack atualmente não dá suporte a Arquivos, então tentar expandir o Arquivos resultará em um erro

09/12/2016
### <a name="version-084"></a>Versão 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Novo

* Gera links diretos para contas de armazenamento ou contêineres, filas, tabelas ou arquivo para compartilhar e acessar facilmente os recursos – suporte a Windows e Mac OS
* Pesquise seus contêineres de blob, tabelas, filas, compartilhamentos de arquivos ou contas de armazenamento da caixa de pesquisa
* Agora você pode agrupar cláusulas no construtor de consultas de tabela
* Renomear e copiar/colar contêineres de blob, compartilhamentos de arquivos, tabelas, blobs, pastas de blob, arquivos e diretórios de contas e contêineres anexadas a SAS
* Agora, ao renomear e copiar contêineres de blob e compartilhamentos de arquivos, as propriedades e metadados são preservados

#### <a name="fixes"></a>Correções

* Corrigido: não é possível editar entidades de tabela se elas contiverem propriedades boolianas ou binárias

#### <a name="known-issues"></a>Problemas conhecidos

* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 – depois disso, o desempenho pode ser afetado

08/03/2016
### <a name="version-083"></a>Versão 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Novo

* Renomear os contêineres, tabelas, compartilhamentos de arquivos
* Experiência aprimorada do Construtor de consulta
* Capacidade de salvar e carregar consultas
* Compartilha links diretos para contas de armazenamento ou contêineres, filas, tabelas ou arquivo para compartilhar e acessar facilmente os recursos (somente para Windows – suporte para macOS em breve!)
* Capacidade de gerenciar e configurar regras de CORS

#### <a name="fixes"></a>Correções

* Corrigido: as Contas da Microsoft exigem reautenticação a cada 8 a 12 horas

#### <a name="known-issues"></a>Problemas conhecidos

* Às vezes, a interface do usuário pode parecer congelada – maximizar a janela ajuda a resolver esse problema
* A instalação do macOS pode exigir permissões elevadas
* Talvez o painel de configurações da conta mostre que você precisa reinserir as credenciais a fim de filtrar as assinaturas
* Renomear compartilhamentos de arquivo, contêineres de blob e tabelas não preserva metadados ou outras propriedades no contêiner, como a cota de compartilhamento de arquivo, o nível de acesso público ou políticas de acesso
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação
* A cópia ou a renomeação de recursos não funciona em contas anexadas a SAS

07/07/2016
### <a name="version-082"></a>Versão 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Novo

* As Contas de Armazenamento são agrupadas por assinaturas; armazenamento de desenvolvimento e recursos anexados via chave ou SAS são mostrados sob o nó (Local e Anexado)
* Saia das contas no painel "Configurações de Conta do Azure"
* Definir configurações de proxy para habilitar e gerenciar a entrada
* Criar e quebrar concessões de blob
* Abrir contêineres de blob, filas, tabelas e arquivos com um único clique

#### <a name="fixes"></a>Correções

* Corrigido: mensagens em fila inseridas com bibliotecas .NET ou Java não são corretamente decodificadas com base64
* Corrigido: tabelas $metrics não são exibidas para contas de Armazenamento de Blobs
* Corrigido: o nó tabelas não funciona para o armazenamento local (Desenvolvimento)

#### <a name="known-issues"></a>Problemas conhecidos

* A instalação do macOS pode exigir permissões elevadas

06/15/2016
### <a name="version-080"></a>Versão 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Novo

* Suporte para compartilhamento de arquivos: exibição, upload, download, cópia de arquivos e diretórios, URIs SAS (criar e conectar)
* Melhor experiência do usuário para se conectar ao Armazenamento com URIs SAS ou chaves de conta
* Exportar resultados da consulta de tabela
* Personalização e reordenação da coluna Tabela
* Exibição de contêineres de blob $logs e tabelas $metrics para Contas de Armazenamento com métricas habilitadas
* Comportamento de exportação e importação aprimorado, agora inclui o tipo de valor da propriedade

#### <a name="fixes"></a>Correções

* Corrigido: o upload ou download de blobs grandes pode resultar em uploads/downloads incompletos
* Corrigido: edição, adição ou importação de uma entidade com um valor de cadeia de caracteres numérico ("1") converterá esse valor em duplo
* Corrigido: não é possível expandir o nó Tabela no ambiente de desenvolvimento local

#### <a name="known-issues"></a>Problemas conhecidos

* As tabelas $metrics não são visíveis para contas de Armazenamento de Blobs
* Mensagens em fila adicionadas por meio de programação podem não ser exibidas corretamente se as mensagens forem codificadas usando a codificação Base64

05/17/2016
### <a name="version-07201605090"></a>Versão 0.7.20160509.0

#### <a name="new"></a>Novo

* Melhor tratamento de erros para falhas de aplicativo

#### <a name="fixes"></a>Correções

* Correção de bug no qual as mensagens da Barra de Informações às vezes não apareciam quando as credenciais de entrada eram exigidas

#### <a name="known-issues"></a>Problemas conhecidos

* Tabelas: adição, edição ou importação de uma entidade que tem uma propriedade com um valor numérico ambíguo, como "1" ou "1.0", e o usuário tenta enviá-lo como um `Edm.String`, o valor voltará por meio da API do cliente como um Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Versão 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Novo

* Suporte à tabela: exibição, consulta, exportação, importação e operações CRUD para entidades
* Suporte à fila: exibição, adição, remoção de mensagens da fila
* Gerar URIs SAS para Contas de Armazenamento
* Conectando-se a contas de armazenamento com URIs SAS
* Notificações de atualização para atualizações futuras do Gerenciador de Armazenamento
* Aparência atualizada

#### <a name="fixes"></a>Correções

* Aprimoramento de desempenho e confiabilidade

### <a name="known-issues-amp-mitigations"></a>Problemas &amp; mitigações conhecidos

* O download de arquivos de blob grandes não funciona corretamente – recomendamos usar AzCopy enquanto resolvemos esse problema
* As credenciais da conta não serão recuperadas nem armazenadas em cache se a pasta base não puder ser encontrada ou não puder receber gravação
* Se adicionarmos, editarmos ou importamos uma entidade que tem uma propriedade com um valor numérico ambíguo, como "1" ou "1.0", e o usuário tentar enviá-lo como um `Edm.String`, o valor voltará por meio da API do cliente como um Edm.Double
* Ao importar arquivos CSV com registros de várias linhas, os dados podem ser cortados ou embaralhados

02/03/2016

### <a name="version-07201601291"></a>Versão 0.7.20160129.1

#### <a name="fixes"></a>Correções

* Desempenho geral aprimorado ao carregar, baixar e copiar blobs

01/14/2016

### <a name="version-07201601050"></a>Versão 0.7.20160105.0

#### <a name="new"></a>Novo

* Suporte a Linux (recursos de paridade para OSX)
* Adicionar contêineres de blob com chave SAS (Assinaturas de Acesso Compartilhado)
* Adicionar Contas de Armazenamento para o Azure China 21Vianet
* Adicionar Contas de Armazenamento com pontos de extremidade personalizados
* Abrir e exibir os blobs de texto e imagem de conteúdo
* Exibir e editar propriedades e metadados de blob

#### <a name="fixes"></a>Correções

* Corrigido: o upload ou download de uma grande quantidade de blobs (+ de 500) pode, às vezes, fazer com que o aplicativo apresente uma tela branca
* Corrigido: ao definir o nível de acesso público do contêiner de blob, o novo valor não é atualizado até que você defina novamente o foco no contêiner. Além disso, a caixa de diálogo sempre assume o padrão "Nenhum acesso público", e não o valor real atual.
* Suporte geral aprimorado de teclado/acessibilidade e interface do usuário
* A disposição do texto do histórico de trilha quebra automaticamente quando o texto é longo com espaços em branco
* A caixa de diálogo SAS dá suporte à validação de entrada
* O armazenamento local continua disponível mesmo se as credenciais do usuário tiverem expirado
* Quando um contêiner de blob aberto for excluído, o gerenciador de blob no lado direito será fechado

#### <a name="known-issues"></a>Problemas conhecidos

* A instalação do Linux precisa da versão de gcc atualizada ou com upgrade – veja abaixo as etapas para o upgrade:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Versão 0.7.20151116.0

#### <a name="new"></a>Novo

* Versões para macOS e Windows
* Entre para exibir suas Contas de Armazenamento – use sua Conta institucional, Conta da Microsoft, 2FA etc.
* Armazenamento de desenvolvimento local (use o emulador de armazenamento, somente para Windows)
* Suporte ao Azure Resource Manager e ao recurso Clássico
* Criar e excluir blobs, filas ou tabelas
* Procurar blobs, filas ou tabelas específicas
* Explorar o conteúdo de contêineres de blob
* Exibir e navegar pelos diretórios
* Carregar, baixar e excluir blobs e pastas
* Exibir e editar propriedades e metadados de blob
* Gerar as chaves SAS
* Gerenciar e criar SAP (Políticas de Acesso armazenado)
* Procure blobs pelo prefixo
* Arraste e solte arquivos para carregar ou baixar

#### <a name="known-issues"></a>Problemas conhecidos

* Ao definir o nível de acesso público do contêiner de blob, o novo valor não é atualizado até que você defina novamente o foco no contêiner
* Quando você abre a caixa de diálogo para definir o nível de acesso público, ela sempre mostra "Nenhum acesso público" como o padrão, e não o valor real atual
* Não é possível renomear blobs baixados
* Às vezes, as entradas de log de atividade ficam "presas" em um estado em andamento quando ocorre um erro, e o erro não é exibido
* Às vezes falha ou fica completamente branca ao tentar carregar ou baixar uma grande quantidade de blobs
* Às vezes, cancelar uma operação de cópia não funciona
* Durante a criação de um contêiner (blob/fila/tabela), se você inserir um nome inválido e continuar com a criação de outro com um tipo de contêiner diferente, não será possível definir o foco no novo tipo
* Não é possível criar uma nova pasta ou renomear a pasta




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
