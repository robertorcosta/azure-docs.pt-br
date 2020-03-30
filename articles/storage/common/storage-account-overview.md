---
title: Visão geral da conta de armazenamento
titleSuffix: Azure Storage
description: Entenda as opções para criar e usar uma conta do Armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371539"
---
# <a name="storage-account-overview"></a>Visão geral da conta de armazenamento

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do Azure Storage que são acessíveis de qualquer lugar do mundo em HTTP ou HTTPS. Os dados em sua conta de armazenamento Azure são duráveis e altamente disponíveis, seguros e maciçamente escaláveis.

Para saber como criar uma conta de armazenamento do Azure, confira [Criar uma conta de armazenamento](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Contas para uso geral v2

As contas de armazenamento para uso geral v2 são compatíveis com os recursos mais recentes do Armazenamento do Azure e incorporam todas as funcionalidades das contas de armazenamento de blobs e para uso geral v1. As contas de armazenamento para uso geral v2 têm os menores preços de capacidade por gigabyte para o Armazenamento do Azure, bem como os preços de transação competitivos no setor. As contas de armazenamento para uso geral v2 são compatíveis com estes serviços do Armazenamento do Azure:

- BLOBs (todos os tipos: bloco, acrescentar, página)
- Data Lake Gen2
- Arquivos
- Discos
- Filas
- Tabelas

> [!NOTE]
> A Microsoft recomenda o uso de contas de armazenamento para uso geral v2 na maioria dos cenários. É possível atualizar facilmente de uma conta de armazenamento de blobs ou para uso geral v1 para uma v2 sem tempo de inatividade e sem a necessidade de copiar dados.
>
> Para saber mais sobre o upgrade para uma conta para uso geral v2, confira [Atualizar para uma conta de armazenamento de para geral v2](storage-account-upgrade.md).

As contas de armazenamento para uso geral v2 oferecem várias camadas de acesso para armazenamento de dados com base nos seus padrões de uso. Para saber mais, confira [Camadas de acesso para dados de blob de blocos](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Contas para uso geral v1

Contas de armazenamento v1 de uso geral fornecem acesso a todos os serviços de armazenamento do Azure, mas podem não ter os recursos mais recentes ou os preços mais baixos por gigabyte. As contas de armazenamento para uso geral v1 são compatíveis com estes serviços do Armazenamento do Azure:

- Blobs (todos os tipos)
- Arquivos
- Discos
- Filas
- Tabelas

Você deve usar contas v2 de uso geral na maioria dos casos. Você pode usar contas v1 de uso geral para esses cenários:

- O modelo de implantação clássico do Azure é obrigatório para os aplicativos. As contas para uso geral v2 e de Armazenamento de Blobs só são compatíveis com o modelo de implantação do Azure Resource Manager.

- Seus aplicativos são intensivos em transações ou usam largura de banda de georeplicação significativa, mas não exigem grande capacidade. Nesse caso, o uso geral v1 pode ser a opção mais econômica.

- Você usa uma versão da [API REST](https://msdn.microsoft.com/library/azure/dd894041.aspx) dos Serviços de Armazenamento que é anterior a 2014-02-14 ou uma biblioteca de clientes com uma versão inferior a 4.x. Você não pode atualizar sua aplicação.

### <a name="blockblobstorage-accounts"></a>Contas blockblobarmazenamento

Uma conta BlockBlobStorage é uma conta de armazenamento especializada no nível de desempenho premium para armazenar dados de objetos não estruturados como blobs de bloco ou blobs de apêndice. Em comparação com contas v2 e BlobStorage de uso geral, as contas BlockBlobStorage fornecem baixa, latência consistente e taxas de transação mais altas.

As contas do BlockBlobStorage não suportam atualmente o hierárquico para níveis de acesso quentes, legais ou de arquivamento. Esse tipo de conta de armazenamento não suporta bolhas de página, tabelas ou filas.

### <a name="filestorage-accounts"></a>Contas de armazenamento de arquivos

Uma conta filestorage é uma conta de armazenamento especializada usada para armazenar e criar compartilhamentos de arquivos premium. Esse tipo de conta de armazenamento suporta arquivos, mas não blobs de bloco, blobs de apêndice, blobs de página, tabelas ou filas.

As contas de armazenamento de arquivos oferecem características exclusivas dedicadas ao desempenho, como estouro do IOPS. Para obter mais informações sobre essas características, consulte a seção [Arquivo compartilhar camadas](../files/storage-files-planning.md#storage-tiers) de armazenamento do guia de planejamento Arquivos.

## <a name="naming-storage-accounts"></a>Nomear contas de armazenamento

Ao nomear sua conta de armazenamento, lembre-se dessas regras:

- Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.
- O nome da sua conta de armazenamento deve ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="performance-tiers"></a>Níveis de desempenho

Dependendo do tipo de conta de armazenamento que você cria, você pode escolher entre níveis de desempenho padrão e premium.

### <a name="general-purpose-storage-accounts"></a>Contas de armazenamento de uso geral

As contas de armazenamento para uso geral podem ser configuradas para qualquer um dos seguintes níveis de desempenho:

- Um nível de desempenho padrão para armazenamento de blobs, arquivos, tabelas, filas e discos da máquina virtual do Azure. Para obter mais informações sobre metas de escalabilidade para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](scalability-targets-standard-account.md).
- Um nível de desempenho premium para armazenar discos de máquina virtual não gerenciados. A Microsoft recomenda o uso de discos gerenciados com máquinas virtuais Azure em vez de discos não gerenciados. Para obter mais informações sobre metas de escalabilidade para o nível de desempenho premium, consulte [metas de escalabilidade para contas de armazenamento blob de página premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Contas de armazenamento blockblobarmazenamento

As contas de armazenamento BlockBlobStorage fornecem um nível de desempenho premium para armazenar blobs de blocos e blobs de apêndice. Para obter mais informações, consulte [metas de escalabilidade para contas de armazenamento de blob de bloco premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Contas de armazenamento de armazenamento de arquivos

As contas de armazenamento de armazenamento de arquivos fornecem um nível de desempenho premium para compartilhamentos de arquivos Do Zure. Para obter mais informações, consulte [as metas de escalabilidade e desempenho do Azure Files](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Camadas de acesso para dados de blob de blocos

O Armazenamento do Azure oferece diferentes opções para acessar dados de blob de blocos baseados em padrões de uso. Cada camada de acesso no Armazenamento do Azure é otimizada para um determinado padrão de uso de dados. Ao selecionar a camada de acesso correta para suas necessidades, é possível armazenar seus dados de blob de blocos da maneira mais econômica.

As camadas de acesso disponíveis são:

- A camada de acesso **quente.** Esse nível é otimizado para acesso frequente de objetos na conta de armazenamento. Acessar dados no nível quente é mais econômico, enquanto os custos de armazenamento são mais altos. Por padrão, as novas contas de armazenamento são criadas na camada de acesso quente.
- A camada de acesso **Cool.** Este nível é otimizado para armazenar grandes quantidades de dados que são raramente acessados e armazenados por pelo menos 30 dias. Armazenar dados no nível legal é mais econômico, mas acessar esses dados pode ser mais caro do que acessar dados no nível quente.
- O **arquivo.** Este nível está disponível apenas para bolhas de bloco individuais. O nível de arquivo é otimizado para dados que podem tolerar várias horas de latência de recuperação e que permanecerão no nível de arquivamento por pelo menos 180 dias. O nível de arquivamento é a opção mais econômica para armazenar dados. No entanto, acessar esses dados é mais caro do que acessar dados nas camadas quente ou legal.

Se houver uma mudança no padrão de uso de seus dados, você pode alternar entre esses níveis de acesso a qualquer momento. Para obter mais informações sobre os níveis de acesso, consulte [o armazenamento Azure Blob: níveis de acesso quentes, legais e de arquivamento](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Alterar a camada de acesso para uma conta de armazenamento existente ou um blob pode resultar em cobranças adicionais. Para obter mais informações, consulte a [Seção de cobrança de conta de armazenamento](#storage-account-billing).

## <a name="redundancy"></a>Redundância

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Criptografia

Todos os dados da sua conta de armazenamento são criptografados no lado do serviço. Para saber mais sobre a criptografia, confira [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Pontos de extremidade da conta de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo no Azure para seus dados. Todos os objetos armazenados no Armazenamento do Azure têm um endereço que inclui o nome exclusivo da conta. A combinação do nome da conta e do ponto de extremidade de serviço do Armazenamento do Azure forma os pontos de extremidade da sua conta de armazenamento.

Por exemplo, se a conta de armazenamento para uso geral chamar *mystorageaccount*, os pontos de extremidade padrão da conta serão:

- Armazenamento blob:`https://*mystorageaccount*.blob.core.windows.net`
- Armazenamento de mesa:`https://*mystorageaccount*.table.core.windows.net`
- Armazenamento na fila:`https://*mystorageaccount*.queue.core.windows.net`
- Arquivos Azure:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> Bloquear contas de armazenamento blob e blob expõem apenas o ponto final do serviço Blob.

Construa a URL para acessar um objeto em uma conta de armazenamento, anexando a localização do objeto na conta de armazenamento até o ponto final. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também é possível configurar um domínio personalizado para blobs para usar com a conta de armazenamento. Para saber mais, confira [Configurar um nome de domínio personalizado para a conta do Armazenamento do Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controlar o acesso aos dados da conta

Por padrão, os dados em sua conta estão disponíveis somente para você, o proprietário da conta. Você controla quem pode acessar os dados e quais permissões essas pessoas têm.

Todas as solicitações feitas na conta de armazenamento precisam ser autorizadas. No nível do serviço, a solicitação deve incluir um cabeçalho de *autorização* válido. Especificamente, este cabeçalho inclui todas as informações necessárias para que o serviço valide a solicitação antes de executá-la.

É possível permitir acesso aos dados da conta de armazenamento usando uma das seguintes abordagens:

- **Diretório ativo do Azure:** Use credenciais do Azure Active Directory (Azure AD) para autenticar um usuário, grupo ou outra identidade para acesso a dados de blob e fila. Se a autenticação de uma identidade for bem-sucedida, o Azure AD retornará um token para usar ao autorizar a solicitação de acesso ao armazenamento de blobs ou de filas do Azure. Para saber mais, confira [Autenticar o acesso ao Armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).
- **Autorização de chave compartilhada:** use a chave de acesso da conta de armazenamento para criar uma cadeia de conexão para o aplicativo usar em runtime para acessar o Armazenamento do Azure. Os valores na cadeia de conexão são usados para criar o cabeçalho de *autorização* passado para o Armazenamento do Azure. Para saber mais, confira [Configurar cadeias de conexão do Armazenamento do Azure](storage-configure-connection-string.md).
- **Assinatura de acesso compartilhado:** Use uma assinatura de acesso compartilhado para delegar o acesso aos recursos em sua conta de armazenamento, se você não estiver usando a autorização do Azure AD. Uma assinatura de acesso compartilhado é um token que encapsula todas as informações necessárias para autorizar uma solicitação para o Armazenamento do Azure na URL. É possível especificar o recurso de armazenamento, as permissões concedidas e o intervalo em que as permissões serão válidas como parte da assinatura de acesso compartilhado. Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](storage-sas-overview.md) para saber mais.

> [!NOTE]
> Autenticar usuários ou aplicativos usando as credenciais do Azure AD oferece mais segurança e facilidade ao usar os meios de autorização. Enquanto você pode continuar a usar a autorização de chave compartilhada com seus aplicativos, usando o AD do Azure evita a necessidade de armazenar sua chave de acesso da conta com o seu código. Também é possível continuar a usar assinaturas de acesso compartilhado (SAS) para conceder acesso refinado a recursos em sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar sobre revogar uma SAS comprometida.
>
> A Microsoft recomenda o uso da autorização do Azure AD para seus aplicativos de blob e fila do Azure Storage, quando possível.

## <a name="copying-data-into-a-storage-account"></a>Copiar dados para uma conta de armazenamento

A Microsoft oferece utilitários e bibliotecas para importar dados de dispositivos de armazenamento local ou provedores de armazenamento em nuvem de terceiros. Qual solução você usa depende da quantidade de dados que você está transferindo.

Ao atualizar uma conta para uso geral v1 para uma v2 ou de Armazenamento de Blobs, os dados são automaticamente migrados. A Microsoft recomenda seguir esse caminho para fazer upgrade da sua conta. No entanto, se você decidir mover dados de uma conta v1 de uso geral para uma conta de armazenamento Blob, então você migrará seus dados manualmente, usando as ferramentas e bibliotecas descritas abaixo.

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comando do Windows desenvolvido para cópia de dados de alto desempenho para dentro e para fora do Armazenamento do Azure. É possível usar o AzCopy para copiar dados para uma conta de armazenamento de blobs de uma conta de armazenamento para uso geral existente ou carregar dados de dispositivos de armazenamento local. Para obter mais informações, consulte [Transferir dados com o Utilitário de Linha de Comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de movimentação de dados

A biblioteca de movimentação de dados do Armazenamento do Azure para .NET baseia-se no framework de movimentação de dados de núcleo que habilita o AzCopy. A biblioteca foi projetada para operações de transferência de dados de alto desempenho, confiáveis e fáceis, de forma semelhante ao AzCopy. Você pode usar a biblioteca de movimentação de dados para tirar proveito dos recursos do AzCopy nativamente. Para obter mais informações, consulte [a Biblioteca de movimentação de dados do azure para .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Você pode criar um aplicativo personalizado para migrar seus dados de uma conta de armazenamento v1 de uso geral para uma conta de armazenamento Blob. Use uma das bibliotecas de clientes do Azure ou os serviços de armazenamento do Azure REST API. O Armazenamento do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas, como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem recursos avançados, como lógica de recuperação, registro em log e carregamentos paralelos. Você também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que faça solicitações HTTP/HTTPS.

Para saber mais sobre a API REST do Armazenamento do Azure, confira a [Referência da API REST dos Serviços do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> Blobs criptografados usando metadados relacionados à criptografia de armazenamento no lado do cliente com o blob. Se você copiar um blob que é criptografado com criptografia do lado do cliente, verifique se que a operação de cópia preserva os metadados de blob e especialmente os metadados relacionados à criptografia. Se você copiar um blob sem os metadados de criptografia, o conteúdo do blob não poderá ser recuperado novamente. Para obter mais detalhes sobre os metadados relacionados à criptografia, confira [Criptografia no Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Cobrança de conta de armazenamento

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta de armazenamento](storage-account-create.md)
- [Criar uma conta de armazenamento de blobs de blocos](../blobs/storage-blob-create-account-block-blob.md)
