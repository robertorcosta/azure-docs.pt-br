---
title: Níveis de acesso quentes, legais e de arquivamento para blobs - Azure Storage
description: Níveis de acesso quentes, legais e de arquivamento para contas de armazenamento do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: f2f6be1022a7100a23f49534f2c18fc951d56284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255503"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo

O armazenamento do Azure oferece diferentes níveis de acesso, que permitem armazenar dados de objetos blob da maneira mais econômica. Os níveis de acesso disponíveis incluem:

- **Hot** - Otimizado para armazenar dados que são acessados com freqüência.
- **Cool** - Otimizado para armazenar dados que são pouco acessados e armazenados por pelo menos 30 dias.
- **Arquivamento** - Otimizado para armazenar dados que raramente são acessados e armazenados por pelo menos 180 dias com requisitos flexíveis de latência (na ordem das horas).

As seguintes considerações se aplicam aos diferentes níveis de acesso:

- Apenas os níveis de acesso quentes e legais podem ser definidos no nível da conta. O nível de acesso ao arquivo não está disponível no nível da conta.
- Os níveis de arquivo quente, legal e de arquivamento podem ser definidos no nível de bolha durante o upload ou após o upload.
- Os dados no nível de acesso legal podem tolerar uma disponibilidade ligeiramente menor, mas ainda exigem alta durabilidade, latência de recuperação e características de throughput semelhantes a dados quentes. Para dados legais, um contrato de nível de serviço de disponibilidade ligeiramente menor (SLA) e custos de acesso mais altos em comparação com dados quentes são compensações aceitáveis para custos de armazenamento mais baixos.
- O armazenamento de arquivos armazena dados offline e oferece os menores custos de armazenamento, mas também os maiores custos de rehidratação de dados e acesso.

Os dados armazenados na nuvem aumentam em ritmo exponencial. Para gerenciar os custos de suas necessidades cada vez maiores de armazenamento, é útil organizar seus dados com base em atributos como frequência de acesso e período de retenção planejado para otimizar os custos. Os dados armazenados na nuvem podem ser diferentes com base na forma como são gerados, processados e acessados ao longo de sua vida útil. Alguns dados são ativamente acessados e modificados durante seu ciclo de vida. Alguns dados são acessados com frequência no início do seu tempo de vida, mas esse acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente são, se nunca, acessados após serem armazenados.

Cada um desses cenários de acesso a dados se beneficia de um nível de acesso diferente que é otimizado para um padrão de acesso específico. Com níveis de acesso quentes, legais e de arquivamento, o armazenamento Azure Blob aborda essa necessidade de níveis de acesso diferenciados com modelos de preços separados.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que dão suporte a camadas

A hierarquiagem de dados de armazenamento de objetos entre hot, cool e archive é suportada apenas em contas Blob storage e General Purpose v2 (GPv2). As contas de Propósito Geral v1 (GPv1) não suportam hierarquida. Os clientes podem facilmente converter suas contas de armazenamento GPv1 ou Blob existentes em contas GPv2 através do portal Azure. O GPv2 fornece novos preços e recursos para blobs, arquivos e filas. Alguns recursos e cortes de preços são oferecidos apenas em contas GPv2. Avalie usando contas GPv2 depois de revisar os preços de forma abrangente. Algumas cargas de trabalho podem ser mais caras no GPv2 do que no GPv1. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

O armazenamento Blob e as contas GPv2 expõem o atributo **Access Tier** no nível da conta. Este atributo permite especificar o nível de acesso padrão para qualquer bolha que não o tenha definido explicitamente no nível do objeto. Para objetos com o nível definido no nível do objeto, o nível da conta não será aplicado. O nível de arquivo só pode ser aplicado no nível do objeto. Você pode alternar entre esses níveis de acesso a qualquer momento.

## <a name="hot-access-tier"></a>Camada de acesso quente

O nível de acesso quente tem custos de armazenamento mais altos do que os níveis de legal e arquivo, mas os custos de acesso mais baixos. Exemplos de cenários de uso para o nível de acesso quente incluem:

- Dados que estão em uso ativo ou que devem ser acessados (lidos e escritos) com freqüência.
- Dados que são encenados para processamento e eventual migração para o nível de acesso legal.

## <a name="cool-access-tier"></a>Camada de acesso frio

O nível de acesso legal tem custos de armazenamento mais baixos e custos de acesso mais altos em comparação com o armazenamento quente. Essa camada destina-se aos dados que permanecerão na camada esporádica por pelo menos 30 dias. Exemplos de cenários de uso para o nível de acesso legal incluem:

- Conjuntos de dados de recuperação de desastre e de backup de curto prazo.
- Conteúdo de mídia mais antigo que não é mais exibido frequentemente, mas ainda deve estar disponível imediatamente quando acessado.
- Grandes conjuntos de dados que precisam ser armazenados de forma econômica enquanto mais dados estão sendo obtidos para processamento futuro. (*por exemplo*, armazenamento de longo prazo de dados científicos; dados brutos de telemetria de uma instalação de manufatura)

## <a name="archive-access-tier"></a>Camada de acesso ao arquivo

O nível de acesso ao arquivo tem o menor custo de armazenamento. Mas tem custos de recuperação de dados mais altos em comparação com os níveis quente e legal. Os dados no nível de arquivo podem levar várias horas para serem recuperados. Os dados devem permanecer no nível de arquivamento por pelo menos 180 dias ou estar sujeitos a uma taxa de exclusão antecipada.

Enquanto uma bolha está no armazenamento de arquivos, os dados blob estão offline e não podem ser lidos, substituídos ou modificados. Para ler ou baixar uma bolha no arquivo, você deve primeiro reidratá-lo para um nível on-line. Você não pode tirar instantâneos de uma bolha no armazenamento de arquivos. No entanto, os metadados de blob permanecem online e disponíveis, permitindo que você liste o blob e suas propriedades. Para blobs no arquivo, as únicas operações válidas são GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob e DeleteBlob. Consulte [Reidratar os dados do blob do nível de arquivo](storage-blob-rehydration.md) para saber mais.

Exemplos de cenários de uso para o nível de acesso ao arquivamento incluem:

- Backup de longo prazo, backup secundário e conjuntos de dados de arquivamento 
- Dados originais (brutos) que devem ser preservados, mesmo após serem processados em formato utilizável final.
- Dados de conformidade e arquivamento que precisam ser armazenados por um longo tempo e quase nunca são acessados.

## <a name="account-level-tiering"></a>Hierarquiagem no nível da conta

Blobs em todos os três níveis de acesso podem coexistir dentro da mesma conta. Qualquer bolha que não tenha um nível explicitamente atribuído infere o nível da configuração de nível de acesso da conta. Se o nível de acesso vier da conta, você verá a propriedade blob **Inferred access tier** definida como "true", e a propriedade **Blob Access Tier** corresponde à camada da conta. No portal Azure, a propriedade _inferida da camada de acesso_ é exibida com a camada de acesso blob como Hot **(inferido)** ou **Cool (inferida)**.

Alterar o nível de acesso da conta aplica-se a todos os objetos _inferidos do nível de acesso_ armazenados na conta que não possuem um conjunto de níveis explícito. Se você alternar o nível da conta de quente para esfriar, você será cobrado por operações de gravação (por 10.000) para todas as bolhas sem um nível definido apenas em contas GPv2. Não há cobrança por essa mudança nas contas de armazenamento Blob. Você será cobrado tanto por operações de leitura (por 10.000) quanto por recuperação de dados (por GB) se você alternar de frio para quente em contas Blob ou GPv2.

## <a name="blob-level-tiering"></a>Camada no nível do blob

A hierarquação em nível blob permite que você carregue dados para o nível de acesso de sua escolha usando as operações [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block List](/rest/api/storageservices/put-block-list) e altere o nível de seus dados no nível do objeto usando a operação Set [Blob Tier](/rest/api/storageservices/set-blob-tier) ou o recurso de gerenciamento do ciclo de [vida.](#blob-lifecycle-management) Você pode carregar dados para o seu nível de acesso necessário e, em seguida, alterar facilmente o nível de acesso blob entre os níveis quente, legal ou de arquivamento à medida que os padrões de uso mudam, sem ter que mover dados entre contas. Todas as solicitações de alteração de nível acontecem imediatamente e as alterações de nível entre quente e frio são instantâneas. No entanto, reidratar um blob dos arquivos pode levar várias horas.

A hora da última alteração na camada de blob é exposta por meio da propriedade do blob **Acessar Hora de Alteração da Camada**. Ao substituir uma bolha no nível quente ou legal, a bolha recém-criada herda o nível da bolha que foi substituído a menos que o novo nível de acesso blob seja explicitamente definido na criação. Se uma bolha está no nível de arquivo, não pode ser sobregravada, então carregar a mesma bolha não é permitido neste cenário. 

> [!NOTE]
> O armazenamento de arquivos e as camadas no nível do blob só oferecem suporte aos blobs de bloco. Você também não pode alterar atualmente o nível de uma bolha de bloco que tenha instantâneos.

### <a name="blob-lifecycle-management"></a>Gerenciamento de ciclo de vida de blob

O gerenciamento do ciclo de vida do Blob Storage oferece uma política rica e baseada em regras que você pode usar para fazer a transição de seus dados para o melhor nível de acesso e para expirar dados no final de seu ciclo de vida. Ver [gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md) para saber mais.  

> [!NOTE]
> Os dados armazenados em uma conta de armazenamento de blob de bloco (desempenho Premium) não podem ser atualizados atualmente para quente, legal ou arquivamento usando [o Set Blob Tier](/rest/api/storageservices/set-blob-tier) ou usando o gerenciamento do ciclo de vida do Azure Blob Storage.
> Para mover dados, você deve copiar sincronizadamente blobs da conta de armazenamento block blob para o nível de acesso quente em uma conta diferente usando a [API put block from URL](/rest/api/storageservices/put-block-from-url) ou uma versão do AzCopy que suporta essa API.
> A API *Colocar bloco pela URL* copia dados sincronicamente no servidor, o que significa que a chamada é concluída apenas depois que todos os dados são movidos do local do servidor de origem para o local de destino.

### <a name="blob-level-tiering-billing"></a>Cobrança da camada no nível do blob

Quando uma bolha é carregada ou movida para o nível quente, frio ou de arquivo, ela é cobrada na taxa correspondente imediatamente após a alteração de nível.

Quando um blob é movido para uma camada mais esporádica (frequente -> esporádico, frequente -> arquivos ou esporádico -> arquivos), a operação é cobrada como uma operação de gravação na camada de destino, em que os encargos pela operação de gravação (por 10.000) e gravação de dados (por GB) da camada de destino são aplicados.

Quando uma bolha é movida para um nível mais quente (>de arquivo legal, >de arquivo quente ou >quente), a operação é cobrada como uma leitura do nível de origem, onde as cargas de operação de leitura (por 10.000) e de recuperação de dados (por GB) do nível de origem se aplicam. Encargos de exclusão antecipada para qualquer blob tirado das camadas esporádica ou de arquivo também podem incorrer. [Reidratar dados do arquivo](storage-blob-rehydration.md) leva tempo e os dados serão cobrados preços de arquivamento até que os dados sejam restaurados on-line e as alterações de nível de bolha para quente ou legal. A tabela a seguir resume como as alterações de nível são cobradas:

| | **Encargos de gravação (operação + acesso)** | **Encargos de leitura (operação + acesso)**
| ---- | ----- | ----- |
| **Direção de SetBlobTier** | hot->legal,<br> arquivo hot->,<br> arquivo >legal | arquivo->legal,<br> arquivo->quente,<br> cool->quente

### <a name="cool-and-archive-early-deletion"></a>Exclusão antecipada esporádica e de arquivo

Qualquer bolha que seja movida para o nível legal (apenas contas GPv2) está sujeita a um período de exclusão antecipada legal de 30 dias. Qualquer bolha que seja movida para o nível de arquivo está sujeita a um período de exclusão antecipada de 180 dias. A cobrança é proporcional. Por exemplo, se uma bolha for movida para arquivamento e, em seguida, excluída ou movida para o nível quente após 45 dias, você será cobrado uma taxa de exclusão antecipada equivalente a 135 (180 menos 45) dias de armazenamento dessa bolha no arquivo.

Você pode calcular a exclusão antecipada usando a propriedade blob, **Last-Modified**, se não houver alterações no nível de acesso. Caso contrário, você pode usar quando o nível de acesso foi modificado pela última vez para esfriar ou arquivar visualizando a propriedade blob: **access-tier-change-time**. Para obter mais informações sobre as propriedades de blob, confira [Obter Propriedades de Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparando opções de armazenamento de blob de blocos

A tabela a seguir mostra uma comparação do armazenamento de blob de bloco de desempenho premium e os níveis de acesso quente, frio e de arquivamento.

|                                           | **Desempenho premium**   | **Nível quente** | **Camada legal**       | **Nível de arquivamento**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilidade**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Disponibilidade** <br> **(Leituras de RA-GRS)**  | N/D                       | 99,99%       | 99,9%               | Offline           |
| **Encargos de uso**                         | Custos de armazenamento mais altos, menor acesso e custo de transação | Custos de armazenamento maiores, custos de acesso e de transações menores | Custos de armazenamento menores, custos de acesso e de transações maiores | Custos de armazenamento mais baixos, custos de acesso e de transações mais altos |
| **Tamanho mínimo de objeto**                   | N/D                       | N/D          | N/D                 | N/D               |
| **Duração mínima de armazenamento**              | N/D                       | N/D          | 30 dias<sup>1</sup> | 180 dias
| **Latência** <br> **(Tempo até o primeiro byte)** | Milissegundos de um dígito | milissegundos | milissegundos        | horas<sup>2</sup> |

<sup>1</sup> Objetos no nível legal nas contas GPv2 têm uma duração mínima de retenção de 30 dias. As contas de armazenamento Blob não têm uma duração mínima de retenção para o nível legal.

<sup>2</sup> O Armazenamento de Arquivos atualmente suporta 2 prioridades de reidratação, High e Standard, que oferece diferentes latências de recuperação. Para obter mais informações, consulte [Rehydrate blob dados do nível de arquivo](storage-blob-rehydration.md).

> [!NOTE]
> As contas de armazenamento Blob suportam os mesmos alvos de desempenho e escalabilidade que contas de armazenamento v2 de uso geral. Para obter mais informações, confira [Escalabilidade e metas de desempenho do Armazenamento de Blobs](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta seção, os seguintes cenários são demonstrados usando o portal Azure e o powershell:

- Como alterar a camada de acesso de conta padrão de uma conta de Armazenamento de Blobs ou de GPv2.
- Como alterar a camada de um blob em uma conta de Armazenamento de Blobs ou de GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso de conta padrão de uma conta de Armazenamento de Blobs ou de GPv2

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Faça login no [portal Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos**.

1. Selecione sua conta de armazenamento.

1. Em **Configurações,** **selecione Configuração** para visualizar e alterar a configuração da conta.

1. Selecione o nível de acesso certo para suas necessidades: Defina o **nível de acesso** para **Legal** ou **Hot**.

1. Clique em **Salvar** na parte superior.

![Alterar o nível da conta de armazenamento](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
O script PowerShell a seguir pode ser usado para alterar o nível da conta. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Altere o nível de uma bolha em uma conta de armazenamento GPv2 ou Blob
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Faça login no [portal Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos**.

1. Selecione sua conta de armazenamento.

1. Selecione seu recipiente e selecione sua bolha.

1. Nas **propriedades Blob,** selecione **Alterar nível**.

1. Selecione o nível de acesso **Hot,** **Cool**ou **Archive.** Se a sua bolha estiver atualmente no arquivo e você quiser reidratar para um nível on-line, você também pode selecionar uma Prioridade reidratar de **padrão** ou **alto**.

1. Selecione **Salvar** na parte inferior.

![Alterar o nível da conta de armazenamento](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
O script PowerShell a seguir pode ser usado para alterar o nível blob. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. A `$containerName` variável deve ser inicializada com o nome do recipiente. A `$blobName` variável deve ser inicializada com o seu nome blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Preços e cobrança

Todas as contas de armazenamento usam um modelo de preços para armazenamento block blob com base no nível de cada bolha. Tenha em mente as seguintes considerações de cobrança:

- **Custos de armazenamento**: Além da quantidade de dados armazenados, o custo de armazenamento de dados varia dependendo da camada de acesso. O custo por gigabyte diminui conforme a camada fica mais esporádica.
- **Custos de acesso a dados**: os encargos de acesso a dados aumentam conforme a camada fica mais esporádica. Para dados na camada de acesso legal e de arquivamento, é cobrada uma taxa de acesso de dados por gigabyte para leituras.
- **Custos de transação**: Há uma taxa por transação para todos os níveis que aumenta à medida que a camada fica mais fria.
- **Custos de transferência de dados de replicação geográfica**: isso só se aplica a contas com replicação geográfica configurada, incluindo GRS e RA-GRS. A transferência de dados de replicação geográfica acarreta um encargo por gigabyte.
- **Custos de transferência de dados de saída**: transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) acarretam a cobrança por uso de largura de banda por gigabyte, de forma consistente com as contas de armazenamento de finalidade geral.
- **Alterando o nível de acesso**: Alterar o nível de acesso da conta resultará em taxas de alteração de nível para blobs _inferidos de nível_ de acesso armazenados na conta que não têm um conjunto de níveis explícito. Para obter informações sobre a alteração do nível de acesso para uma única bolha, consulte [o faturamento de nível Blob](#blob-level-tiering-billing).

> [!NOTE]
> Para obter mais informações sobre preços para blobs de bloco, consulte a página [preços de armazenamento do Azure.](https://azure.microsoft.com/pricing/details/storage/blobs/) Para saber mais informações sobre os encargos de transferência de dados de saída, confira a página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Perguntas frequentes

**Devo usar contas de Armazenamento de Blobs ou de GPv2 se quiser colocar meus dados em camadas?**

Recomendamos que você use o GPv2 em vez de contas de Armazenamento de Blobs para camadas. O GPv2 dá suporte a todos os recursos aos quais as contas de Armazenamento de Blobs dão suporte e muito mais. O preço entre o Armazenamento de Blobs e o GPv2 são quase idênticos, mas alguns novos recursos e cortes de preços só estarão disponíveis em contas de GPv2. As contas GPv1 não suportam hierarquidões.

A estrutura de preços entre contas de GPv1 e GPv2 é diferente, e os clientes devem avaliar cuidadosamente antes de decidir usar contas de GPv2. Você pode converter facilmente uma conta existente de Armazenamento de Blobs ou de GPv1 em GPv2 por meio de um processo simples de um clique. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

**Posso armazenar objetos em todas as três camadas de acesso (quente, legal e arquivo) na mesma conta?**

Sim. O atributo **Access Tier** definido no nível da conta é o nível de conta padrão que se aplica a todos os objetos dessa conta sem um nível definido explícito. O hierarquidamento de nível blob permite definir o nível de acesso no nível do objeto, independentemente da configuração do nível de acesso na conta. Blobs em qualquer um dos três níveis de acesso (quente, legal ou arquivo) podem existir dentro da mesma conta.

**Posso alterar o nível de acesso padrão da minha conta de armazenamento Blob ou GPv2?**

Sim, você pode alterar o nível de conta padrão definindo o atributo **access tier** na conta de armazenamento. Alterar o nível da conta aplica-se a todos os objetos armazenados na conta que não possuem um conjunto de camadas explícito (por exemplo, **Hot (inferido)** ou **Cool (inferido).** Alternar o nível da conta de operações de gravação quentes para frias incurs (por 10.000) para todas as bolhas sem um nível definido apenas em contas GPv2 e alternando de frio para quente incursas operações de leitura (por 10.000) e taxas de recuperação de dados (por GB) para todas as bolhas no armazenamento Blob e contas GPv2.

**Posso definir meu nível de acesso padrão da conta para arquivar?**

Não. Apenas os níveis de acesso quentes e legais podem ser definidos como o nível de acesso padrão da conta. A camada arquivo só pode ser definida no nível do objeto. No upload do blob, você especifica o nível de acesso de sua escolha para ser quente, legal ou arquivamento, independentemente do nível de conta padrão. Essa funcionalidade permite que você escreva dados diretamente no nível de arquivamento para realizar economias de custos a partir do momento em que você cria dados no armazenamento blob.

**Em quais regiões estão disponíveis os níveis de acesso quentes, legais e de arquivamento?**

As camadas de acesso quente e legal, juntamente com a hierarquiçação de nível blob estão disponíveis em todas as regiões. O armazenamento de arquivo estará disponível, inicialmente, apenas em regiões selecionadas. Para obter uma lista completa, consulte [Produtos do Azure por região](https://azure.microsoft.com/regions/services/).

**As bolhas na camada de acesso legal se comportam de forma diferente das da camada de acesso quente?**

Blobs no nível de acesso quente têm a mesma latência que blobs em contas de armazenamento GPv1, GPv2 e Blob. Blobs no nível de acesso legal têm uma latência semelhante (em milissegundos) como blobs em contas de armazenamento GPv1, GPv2 e Blob. Os blobs no nível de acesso ao arquivo têm várias horas de latência nas contas de armazenamento GPv1, GPv2 e Blob.

Blobs no nível de acesso legal têm um nível de serviço de disponibilidade ligeiramente menor (SLA) do que as bolhas armazenadas na camada de acesso quente. Para obter mais informações, veja [SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre os níveis quente, legal e de arquivo são as mesmas?**

Todas as operações entre frequente e esporádica são 100% consistentes. Todas as operações de arquivamento válidas, incluindo GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob são 100% consistentes com quente e legal. Os dados blob não podem ser lidos ou modificados enquanto estiverem no nível de arquivo até que sejam rehidratados; apenas as operações de leitura de metadados blob são suportadas enquanto estão em arquivo.

**Ao reidratar um blob da camada de arquivo para a camada frequente ou esporádica, como vou saber quando a reidratação estiver concluída?**

Durante a reidratação, você pode usar a operação get blob properties para sondar o atributo **Status do arquivo** e confirmar quando a alteração de nível estiver concluída. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade de status do arquivo é removida, e a propriedade **Access Tier** blob reflete o novo nível quente ou legal. Consulte [Reidratar os dados do blob do nível de arquivo](storage-blob-rehydration.md) para saber mais.

**Depois de definir o nível de um blob, quando vai começar a ser cobrada a taxa apropriada?**

Cada blob é sempre cobrado de acordo com a camada indicada pelo propriedade **Camada de Acesso** do blob. Quando você define um novo nível on-line para uma bolha, a propriedade **Access Tier** reflete imediatamente o novo nível para todas as transições. No entanto, reidratar uma bolha do nível de arquivo offline para um nível quente ou frio pode levar várias horas. Neste caso, você é cobrado a taxas de arquivamento até que a reidratação esteja completa, momento em que a propriedade **Access Tier** reflete o novo nível. Uma vez hidratado para o nível on-line, você é cobrado por essa bolha na taxa quente ou legal.

**Como determino se incorrerei em uma taxa de exclusão antecipada ao excluir ou mover uma bolha para fora do nível legal ou de arquivo?**

Qualquer blob que for excluído ou tirado da camada esporádica (somente para contas de GPv2) ou de arquivo morto antes de 30 e 180 dias respectivamente incorrerá em uma carga de exclusão antecipada proporcional. É possível determinar quanto tempo um blob está na camada esporádica ou de arquivos verificando a propriedade **Hora da Alteração da Camada de Acesso** do blob, que fornece um selo da última alteração da camada. Se o nível da bolha nunca foi alterado, você pode verificar a propriedade **blob Última Modificação.** Para obter mais informações, consulte [Legal e arquive a exclusão antecipada](#cool-and-archive-early-deletion).

**Quais ferramentas e SDKs do Azure dão suporte a armazenamento de camada no nível do blob e de arquivo?**

As ferramentas do portal do Azure, do PowerShell e da CLI e as bibliotecas de cliente .NET, Java, Python e Node.js, todas dão suporte ao armazenamento de camada no nível do blob e de arquivo.  

**Qual a quantidade de dados que posso armazenar nas camadas frequente, esporádica ou de arquivo?**

O armazenamento de dados, juntamente com outros limites, são definidos no nível da conta e não por nível de acesso. Você pode optar por usar todo o seu limite em um nível ou em todos os três níveis. Para obter mais informações, consulte [metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

Avalie contas de armazenamento quentes, legais e de arquivamento em contas de armazenamento GPv2 e Blob

- [Verifique a disponibilidade de hot, cool e arquivo por região](https://azure.microsoft.com/regions/#services)
- [Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)
- [Saiba mais sobre a reidratar dados blob do nível de arquivo](storage-blob-rehydration.md)
- [Determine se o desempenho premium beneficiaria seu aplicativo](storage-blob-performance-tiers.md)
- [Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)
- [Verificar o preço de frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
- [Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
