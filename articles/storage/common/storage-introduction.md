---
title: Introdução ao Armazenamento do Azure – Armazenamento em nuvem no Azure | Microsoft Docs
description: O Armazenamento do Azure é uma solução de armazenamento em nuvem. O Armazenamento do Azure oferece armazenamento para objetos de dados altamente disponível, seguro, durável, altamente escalonável e redundante.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ddd4f45e88c0bcc63fb62cc816fae4dbad255841
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157577"
---
# <a name="introduction-to-azure-storage"></a>Introdução ao Armazenamento do Azure

O armazenamento do Azure é uma solução de armazenamento em nuvem da Microsoft para cenários de armazenamento de dados modernos. O Armazenamento do Azure oferece um armazenamento de objetos altamente escalonável para objetos de dados, um serviço de sistema de arquivos para a nuvem, armazenamento de mensagens para um sistema de mensagens confiável e armazenamento de NoSQL. O Armazenamento do Azure é:

- **Durável e altamente disponível.** A redundância garante a segurança dos seus dados no caso de falhas de hardware transitórias. Você também pode optar por replicar dados em datacenters ou regiões geográficas para obter mais proteção contra catástrofes ou desastres naturais. Os dados replicados dessa maneira permanecem altamente disponíveis no caso de uma interrupção inesperada.
- **Proteger.** Todos os dados gravados no Armazenamento do Azure são criptografados pelo serviço. O Armazenamento do Azure oferece um controle refinado sobre quem possui acesso aos seus dados.
- **Escalonável.** O Armazenamento do Azure foi concebido para ser altamente escalonável e atender às necessidades de desempenho e armazenamento de dados dos aplicativos atuais.
- **Gerenciado.** O Microsoft Azure lida com a manutenção, com atualizações e com questões críticas de hardware para você.
- **Acessível.** Os dados no Armazenamento do Azure são acessíveis de qualquer lugar no mundo por HTTP ou HTTPS. A Microsoft fornece bibliotecas de cliente para o armazenamento do Azure em uma variedade de linguagens, incluindo .NET, Java, Node. js, Python, PHP, Ruby, Go e outros, bem como uma API REST madura. O Armazenamento do Azure oferece suporte para scripts no Azure PowerShell ou na CLI do Azure. E o Portal do Azure e o Gerenciador de Armazenamento do Azure oferecem soluções visualmente fáceis para o trabalho com os seus dados.  

## <a name="azure-storage-services"></a>Serviços do Armazenamento do Azure

O Armazenamento do Azure inclui esses serviços de dados:

- [Blobs do Azure](../blobs/storage-blobs-introduction.md): um repositório de objetos altamente escalonável para texto e dados binários.
- [Arquivos do Azure](../files/storage-files-introduction.md): compartilhamentos de arquivos gerenciados para implantações locais e em nuvem.
- [Filas do Azure](../queues/storage-queues-introduction.md): um armazenamento de mensagens para um sistema de mensagens confiável entre componentes do aplicativo.
- [Tabelas do Azure](../tables/table-storage-overview.md): Um armazenamento NoSQL para um armazenamento sem esquemas de dados estruturados.

Cada serviço é acessado através de uma conta de armazenamento. Para usá-los, consulte [Criar uma conta de armazenamento](storage-account-create.md).

## <a name="blob-storage"></a>Armazenamento de blob

O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. O armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários.

O armazenamento de Blobs é ideal para:

- Fornecimento de imagens ou de documentos diretamente a um navegador.
- Armazenamento de arquivos para acesso distribuído.
- Transmissão por streaming de áudio e vídeo.
- Armazenamento de dados de backup e restauração, recuperação de desastres e arquivamento.
- Armazenamento de dados para análise por um serviço local ou hospedado no Azure.

Os objetos no armazenamento de Blobs podem ser acessados de qualquer lugar no mundo via HTTP ou HTTPS. Usuários ou aplicativos cliente podem acessar blobs por meio de URLs, da [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), do [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), da [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou de uma biblioteca de cliente de Armazenamento do Azure. As bibliotecas de clientes de armazenamento estão disponíveis para várias linguagens, incluindo [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Para mais informações sobre o Armazenamento de Blobs, confira [Introdução ao Armazenamento de Blobs](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Arquivos do Azure

Os [Arquivos do Azure](../files/storage-files-introduction.md) permite que você configure compartilhamentos de arquivo por rede altamente disponíveis que podem ser acessados usando o protocolo SMB (Server Message Block) padrão. Isso significa que várias VMs podem compartilhar os mesmos arquivos com acesso de leitura e gravação. Você também pode ler os arquivos usando a interface REST ou as bibliotecas de cliente de armazenamento.

Uma coisa que diferencia os Arquivos do Azure dos arquivos em um compartilhamento de arquivos corporativo é a capacidade de acessar os arquivos de qualquer lugar do mundo usando uma URL que aponte para o arquivo, e que inclua um token SAS (Assinatura de Acesso Compartilhado). Você pode gerar tokens SAS; eles permitem o acesso específico a um ativo privado durante período especificado.

Os compartilhamentos de arquivos podem ser usados para muitos cenários comuns:

- Muitos aplicativos locais usam compartilhamentos de arquivos. Esse recurso facilita a migração dos aplicativos que compartilham dados com o Azure. Se você montar o compartilhamento de arquivos na mesma letra de unidade usada pelo aplicativo local, a parte de seu aplicativo que acessa o compartilhamento de arquivos deverá funcionar com o mínimo de, ou nenhuma, alteração.

- Os arquivos de configuração podem ser armazenados em um compartilhamento de arquivos e acessados de várias VMs. As ferramentas e utilitários usados por vários desenvolvedores em um grupo podem ser armazenados em um compartilhamento de arquivos, garantindo que todas as pessoas possam encontrá-los, e que usem a mesma versão.

- Logs de diagnóstico, métricas e despejos de memória são apenas três exemplos de dados que podem ser gravados em um compartilhamento de arquivos e processados ou analisados posteriormente.

No momento, a autenticação baseada no Active Directory e as ACLs (Listas de controle de acesso) não têm suporte, mas terão em algum momento futuro. As credenciais da conta de armazenamento são usadas para fornecer a autenticação para acesso ao compartilhamento de arquivos. Isso significa que qualquer pessoa com o compartilhamento montado terá acesso completo de leitura/gravação no compartilhamento.

Para obter mais informações sobre Arquivos do Azure, consulte [Introdução aos Arquivos do Azure](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Armazenamento de filas

O serviço Fila do Azure é usado para armazenar e recuperar mensagens. A fila de mensagens pode ser de até 64 KB de tamanho e uma fila pode conter milhões de mensagens. Filas são geralmente usadas para armazenar as listas de mensagens a serem processadas de forma assíncrona.

Por exemplo, digamos que você deseja que seus clientes possam carregar imagens e você deseja criar miniaturas para cada imagem. Você pode fazer com que o cliente aguarde que você crie miniaturas ao carregar as imagens. Uma alternativa seria usar uma fila. Quando o cliente concluir o carregamento, grave uma mensagem na fila. Em seguida, faça com que uma Função do Azure recupere a mensagem da fila e crie as miniaturas. Cada uma das partes desse processamento pode ser dimensionada separadamente oferecendo mais controle durante o ajuste para seu uso.

Para obter mais informações sobre Filas do Azure, consulte [Introdução às Filas do Azure](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Armazenamento de tabela

O armazenamento de Tabelas do Azure agora faz parte do Azure Cosmos DB. Para ver a documentação do armazenamento de Tabelas do Azure, veja a [Visão geral do Armazenamento de Tabelas do Azure](../tables/table-storage-overview.md). Além do serviço de armazenamento de Tabelas do Azure existente, há uma nova oferta de API de Tabela do Azure Cosmos DB que fornece tabelas otimizadas de taxa de transferência, distribuição global e índices secundários automáticos. Para saber mais e experimentar a nova experiência premium, confira [API de Tabela do Azure Cosmos DB](https://aka.ms/premiumtables).

Para saber mais informações sobre o armazenamento de tabelas, consulte [Visão geral do armazenamento de Tabelas do Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Armazenamento em disco

Um disco gerenciado do Azure é um disco rígido virtual (VHD). Você pode pensar nisso como um disco físico em um servidor local, mas virtualizado. Os discos gerenciados do Azure são armazenados como blobs de página, que são um objeto de armazenamento de E/S aleatório no Azure. Chamamos um disco gerenciado de “gerenciado” porque é uma abstração sobre blobs de páginas, contêineres de blob e contas de armazenamento do Azure. Com discos gerenciados, tudo o que você precisa fazer é provisionar o disco e o Azure cuidará do resto.

Para obter mais informações sobre discos gerenciados, consulte [introdução aos Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Para obter mais informações sobre os tipos de conta de armazenamento, consulte [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>Protegendo o acesso a contas de armazenamento

Cada solicitação para o armazenamento do Azure deve ser autorizada. O armazenamento do Azure dá suporte aos seguintes métodos de autorização:

- **Integração do Azure Active Directory (Azure AD) para dados de BLOB e fila.** O armazenamento do Azure dá suporte à autenticação e autorização com o Azure AD para os serviços BLOB e fila por meio do controle de acesso baseado em função (RBAC). A autorização de solicitações com o Azure AD é recomendada para segurança superior e facilidade de uso. Para obter mais informações, consulte [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).
- **Autorização do Azure AD sobre SMB para arquivos do Azure (versão prévia).** Os arquivos do Azure oferecem suporte à autorização baseada em identidade sobre SMB (protocolo de mensagem de servidor) por meio de Azure Active Directory Domain Services. Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando as credenciais do Azure AD. Para obter mais informações, consulte [visão geral da autorização de Azure Active Directory sobre SMB para arquivos do Azure (versão prévia)](../files/storage-files-active-directory-overview.md).
- **Autorização com chave compartilhada.** Os serviços Azure Storage Blob, fila e tabela e arquivos do Azure dão suporte à autorização com chave compartilhada. um cliente que usa a autorização de chave compartilhada passa um cabeçalho com cada solicitação que é assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autorização usando SAS (assinaturas de acesso compartilhado).** Uma SAS (assinatura de acesso compartilhado) é uma cadeia de caracteres que contém um token de segurança que pode ser anexado ao URI para um recurso de armazenamento. O token de segurança encapsula restrições como permissões e o intervalo de acesso. Para obter mais informações, consulte [usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).
- **Acesso anônimo a contêineres e blobs.** Um contêiner e seus BLOBs podem estar publicamente disponíveis. Quando você especifica que um contêiner ou BLOB é público, qualquer pessoa pode lê-lo anonimamente; nenhuma autenticação é necessária. Para obter mais informações, consulte [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>Criptografia

Há dois tipos básicos de criptografia disponíveis para os serviços de Armazenamento. Para saber mais sobre segurança e criptografia, consulte o [Guia de segurança do Armazenamento do Azure](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Criptografia em repouso

A criptografia de armazenamento do Azure protege e protege seus dados para atender aos compromissos de segurança e conformidade da organização. O armazenamento do Azure criptografa automaticamente todos os dados antes de persistir para a conta de armazenamento e descriptografá-los antes da recuperação. Os processos de criptografia, descriptografia e gerenciamento de chaves são totalmente transparentes para os usuários. Os clientes também podem optar por gerenciar suas próprias chaves usando Azure Key Vault. Para obter mais informações, consulte [criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Criptografia do cliente

As bibliotecas de cliente de armazenamento do Azure fornecem métodos para criptografar dados da biblioteca de cliente antes de enviá-los pela rede e descriptografar a resposta. Os dados criptografados por meio da criptografia do lado do cliente também são criptografados em repouso pelo armazenamento do Azure. Para obter mais informações sobre criptografia do lado do cliente, consulte [criptografia do lado do cliente com .net para o armazenamento do Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundância

Para garantir que seus dados sejam duráveis, o armazenamento do Azure armazena várias cópias de seus dados. Quando você configura sua conta de armazenamento, você seleciona uma opção de redundância.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferência de dados do Armazenamento do Azure e para ele

Você tem várias opções para movimentar dados para dentro ou para fora do Armazenamento do Azure. A opção escolhida depende do tamanho de seu conjunto de dados e da largura de banda da rede. Para obter mais informações, consulte [Escolher uma solução do Azure para transferência de dados](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Preços

Para obter informações detalhadas sobre os preços do Armazenamento do Azure, consulte a [Página de preços](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>APIs, bibliotecas e ferramentas de armazenamento

Os recursos do Armazenamento do Azure podem ser acessados por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Armazenamento do Azure oferece bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam muitos aspectos do trabalho com o Armazenamento do Azure manipulando detalhes, como invocação síncrona e assíncrona, processamento em lotes de operações, gerenciamento de exceções, novas tentativas automáticas, comportamento operacional e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas, com outras no pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>Bibliotecas de referência e API de dados do Armazenamento do Azure

- [API REST de Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/)
- [Biblioteca de cliente de armazenamento do Azure para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Biblioteca de cliente de armazenamento do Azure para Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Biblioteca de cliente de armazenamento do Azure para node. js](https://docs.microsoft.com/javascript/api/overview/azure/storage)
- [Biblioteca de cliente de armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python)
- [Biblioteca de cliente de armazenamento do Azure para PHP](https://github.com/Azure/azure-storage-php)
- [Biblioteca de cliente de armazenamento do Azure para Ruby](https://github.com/Azure/azure-storage-ruby)
- [Biblioteca de cliente de armazenamento do Azure paraC++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Bibliotecas de referência e API de gerenciamento do Armazenamento do Azure

- [API REST do Provedor de Recursos de Armazenamento](https://docs.microsoft.com/rest/api/storagerp/)
- [Biblioteca do Cliente do Provedor de Recursos de Armazenamento para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [API REST do Gerenciamento de Serviços de Armazenamento (clássico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Bibliotecas de referência e API de movimentação de dados do Armazenamento do Azure

- [API REST do Serviço de Importação/Exportação do Armazenamento](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Biblioteca do Cliente de Movimentação de Dados do Armazenamento para .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários

- [Cmdlets do Azure PowerShell para Armazenamento](https://docs.microsoft.com/powershell/module/az.storage)
- [Cmdlets da CLI do Azure para Armazenamento](https://docs.microsoft.com/cli/azure/storage)
- [Utilitário de linha de comando AzCopy](https://aka.ms/downloadazcopy)
- [O Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
- [Ferramentas de Cliente do Armazenamento do Azure](../storage-explorers.md)
- [Ferramentas para desenvolvedores do Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Próximas etapas

Para colocar o Armazenamento do Azure em funcionamento, confira [Criar uma conta de armazenamento](storage-account-create.md).
