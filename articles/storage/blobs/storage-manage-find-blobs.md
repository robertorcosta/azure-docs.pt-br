---
title: Gerenciar e localizar dados no armazenamento de BLOBs do Azure com índice de BLOB (versão prévia)
description: Saiba como usar marcas de índice de BLOB para categorizar, gerenciar e consultar para descobrir objetos de BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: f1a4d9af8a1b1095527078dd790e80ef45a5ee9a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722889"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>Gerenciar e localizar dados no armazenamento de BLOBs do Azure com índice de BLOB (versão prévia)

À medida que os conjuntos de dados ficam maiores e maiores, encontrar um objeto específico em um mar de informações pode ser difícil e frustrante. O índice de blob fornece recursos de descoberta e gerenciamento de dados usando atributos de marca de índice de chave-valor, que permitem categorizar e localizar objetos em um único contêiner ou em todos os contêineres em sua conta de armazenamento. Posteriormente, como os requisitos de alterações de dados, os objetos podem ser recategorizados dinamicamente atualizando suas marcas de índice enquanto permanecem no local com sua organização de contêiner atual. A utilização do índice de blob pode simplificar o desenvolvimento consolidando os dados de BLOB e os atributos de índice associados no mesmo serviço; permitindo que você crie aplicativos eficientes e escalonáveis usando recursos nativos. 

O índice de blob permite que você:

- Categorizar dinamicamente seus BLOBs usando marcas de índice de chave-valor para gerenciamento de dados
- Localizar rapidamente BLOBs marcados específicos em um único contêiner ou em uma conta de armazenamento inteira
- Especificar comportamentos condicionais para APIs de blob com base na avaliação de marcas de índice
- Utilizar marcas de índice para controles avançados em recursos da plataforma de BLOB, como o [Gerenciamento do ciclo de vida](storage-lifecycle-management-concepts.md)

Considere o cenário em que você tem milhões de BLOBs em sua conta de armazenamento gravados e acessados por vários aplicativos diferentes. Você deseja localizar todos os dados relacionados de um único projeto, mas não tem certeza do que está no escopo, pois os dados podem ser distribuídos em vários contêineres com diferentes convenções de nomenclatura de BLOB. No entanto, você sabe que seus aplicativos carregam todos os dados com marcas com base em seu respectivo projeto e descrição de identificação. Em vez de Pesquisar por milhões de BLOBs e comparar nomes e propriedades, você pode simplesmente `Project = Contoso` usar como seus critérios de descoberta. O índice de blob filtrará todos os contêineres em toda a conta de armazenamento para localizar e retornar rapidamente apenas o conjunto `Project = Contoso`de blobs de 50 de. 

Para começar com exemplos de como usar o índice de BLOB, consulte [utilizar o índice de BLOB para gerenciar e localizar dados](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Marcas e Gerenciamento de Dados de índice de BLOB

Os prefixos de nome de contêiner e blob são uma categorização unidimensional para seus dados armazenados. O índice de blob agora permite categorização de várias dimensões para todos os [tipos de dados de BLOB (bloco, acréscimo ou página)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) com marcas de atributo aplicadas. Essa categorização multidimensional é nativamente indexada e disponibilizada para você consultar e localizar seus dados rapidamente.

Considere os cinco BLOBs a seguir em sua conta de armazenamento:
>
> Container1/Transaction. csv  
> container2/campaign. docx  
> fotos/bannerphoto. png  
> arquivos mortos/concluídos/2019review. pdf  
> logs/2020/01/01/logfile. txt  
>

Esses BLOBs estão separados no momento usando um prefixo de contêiner/pasta virtual/nome do blob. Com o índice de BLOB, você pode definir um atributo de `Project = Contoso` marca de índice de nesses cinco BLOBs para categorizá-los ao mesmo tempo em que mantém sua organização de prefixo atual. Isso elimina a necessidade de mover dados expondo a capacidade de filtrar e localizar dados usando o índice multidimensional da plataforma de armazenamento.

## <a name="setting-blob-index-tags"></a>Definindo marcas de índice de BLOB

Marcas de índice de blob são atributos de chave-valor que podem ser aplicados a objetos novos ou existentes em sua conta de armazenamento. Você pode especificar as marcas de índice durante o processo de carregamento usando as operações PutBlob, PutBlockList ou CopyBlob e o cabeçalho x-MS-Tags opcional. Se você já tiver BLOBs em sua conta de armazenamento, poderá chamar SetBlobTags com um documento XML formatado especificando os atributos de marca de índice de blob no corpo da solicitação. 

Considere as seguintes marcas de exemplos que podem ser definidas

Você pode aplicar uma única marca em seu blob para descrever quando seus dados concluíram o processamento.
>
> "processedDate" = ' 2020-01-01 '
>
Você pode aplicar várias marcas em seu blob para que sejam mais descritivas dos dados.
>
> "Projeto" = ' contoso '  
> "Classified" = ' true '  
> "Status" = ' não processado '  
> "Priority" = ' 01 ' 
>

Para modificar os atributos de marca de índice existentes, você deve primeiro recuperar os atributos de marca existentes, modificar os atributos de marca e substituir pela operação SetBlobTags. Para remover todas as marcas de índice do blob, chame a operação SetBlobTags sem atributos de marca especificados. Como as marcas de índice de blob são um subrecurso para o conteúdo de dados de BLOB, o SetBlobTags não modifica nenhum conteúdo subjacente e não altera a hora da última modificação do blob.

Os seguintes limites se aplicam a marcas de índice de blob:
- Cada blob pode ter até 10 marcas de índice de BLOB
- As chaves de marca devem ter entre 1 e 128 caracteres
- Os valores de marca devem ter entre 0 e 256 caracteres
- Chaves e valores de marca diferenciam maiúsculas de minúsculas
- Chaves e valores de marca dão suporte apenas a tipos de dados de cadeia de caracteres; qualquer número ou caractere especial será salvo como cadeias de caracteres
- As chaves e os valores de marca devem aderir às seguintes regras de nomenclatura:
  - Caracteres alfanuméricos: a-z, A-Z, 0-9
  - Caracteres especiais: espaço, mais, menos, ponto, dois-pontos, igual, sublinhado, barra invertida

## <a name="getting-and-listing-blob-index-tags"></a>Obtendo e listando marcas de índice de BLOB

As marcas de índice de blob são armazenadas como um subrecurso ao lado dos dados de BLOB e podem ser recuperadas independentemente do conteúdo de dados de blob subjacente. Uma vez definido, as marcas de índice de BLOB para um único blob podem ser recuperadas e revisadas imediatamente com a operação GetBlobTags. A operação ListBlobs com `include:tags` o parâmetro também retornará todos os BLOBs dentro de um contêiner, juntamente com suas marcas de índice de blob aplicadas. 

Para qualquer blob com pelo menos uma marca de índice de BLOB, a contagem x-MS-tag-Count é retornada nas operações ListBlobs, getBlob e getblobproperties que indicam a contagem de marcas de índice de BLOB que existem no BLOB.

## <a name="finding-data-using-blob-index-tags"></a>Localizando dados usando marcas de índice de BLOB

Com as marcas de índice de blob definidas em seus BLOBs, o mecanismo de indexação expõe esses atributos de chave/valor em um índice multidimensional. Embora as marcas de índice existam no BLOB e possam ser recuperadas imediatamente, pode levar algum tempo até que o índice de blob seja atualizado com os atributos de marca de índice atualizados. Depois que o índice de blob é atualizado, agora você pode aproveitar os recursos nativos de consulta e descoberta oferecidos pelo armazenamento de BLOBs.

A operação FindBlobsByTags permite que você obtenha um conjunto de retornos filtrado de BLOBs cujas marcas de índice correspondem a uma determinada expressão de consulta de índice de BLOB. O índice de blob dá suporte à filtragem em todos os contêineres em sua conta de armazenamento ou você pode fazer o escopo da filtragem para apenas um único contêiner. Como todas as chaves e valores de marca de índice de blob são cadeias de caracteres, os operadores relacionais com suporte usam uma classificação lexicográfica nos valores de marca de índice.

Os critérios a seguir se aplicam à filtragem de índice de blob:
-   As chaves de marca devem ser colocadas entre aspas duplas (")
-   Os valores de marcação e os nomes de contêiner devem ser colocados entre aspas simples (')
-   O caractere @ só é permitido para filtragem em um nome de contêiner específico (ou @container seja, = ' ContainerName ')
- Os filtros são aplicados com a classificação lexicográfica em cadeias de caracteres
-   As mesmas operações de intervalo colaterais na mesma chave são inválidas (ou seja, "Rank" > ' 10 ' e "Rank" >= ' 15 ')
- Ao usar REST para criar uma expressão de filtro, os caracteres devem ser codificados em URI

A tabela abaixo mostra todos os operadores válidos para FindBlobsByTags:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual a     | "Status" = ' em andamento ' | 
|     >      |  Maior que |  "Date" > ' 2018-06-18 ' |
|     >=     |  Maior ou igual a | "Priority" >= ' 5 ' | 
|     <      |  Menor que    | "Idade" < ' 32 ' |
|     <=     |  Menor ou igual a  | "Empresa" <= ' contoso ' |
|    AND     |  And lógico  | "Rank" >= ' 010 ' e "Rank" < ' 100 ' |
| @container |  Escopo para um contêiner específico   | @container= ' videofiles ' e "status" = ' done ' |

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Operações de blob condicional com marcas de índice de BLOB
Nas versões REST 2019-10-10 e superior, a maioria das [APIs de serviço blob](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) agora dá suporte a um cabeçalho condicional, x-MS-If-Tags, de modo que a operação só terá sucesso se a condição de índice de blob especificada for atendida. Se a condição não for atendida, você receberá `error 412: The condition specified using HTTP conditional header(s) is not met`.

O cabeçalho x-MS-If-Tags pode ser combinado com os outros cabeçalhos condicionais HTTP existentes (If-Match, If-None-Match, etc.).  Se vários cabeçalhos condicionais forem fornecidos em uma solicitação, todos eles deverão ser avaliados como verdadeiros para que a operação tenha sucesso.  Todos os cabeçalhos condicionais são efetivamente combinados com AND lógico. 

A tabela abaixo mostra todos os operadores válidos para operações condicionais:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual a     | "Status" = ' em andamento ' |
|     <>     |   Não igual   | "Status"  <>  ' done '  | 
|     >      |  Maior que |  "Date" > ' 2018-06-18 ' |
|     >=     |  Maior ou igual a | "Priority" >= ' 5 ' | 
|     <      |  Menor que    | "Idade" < ' 32 ' |
|     <=     |  Menor ou igual a  | "Empresa" <= ' contoso ' |
|    AND     |  And lógico  | "Rank" >= ' 010 ' e "Rank" < ' 100 ' |
|     OU     |  OR lógico   | "Status" = ' done ' ou "Priority" >= ' 05 ' |

> [!NOTE]
> Há dois operadores adicionais, não iguais e lógicos ou, que são permitidos no cabeçalho x-MS-If-Tags condicional para a operação de BLOB, mas não existem na operação FindBlobsByTags.

## <a name="platform-integrations-with-blob-index-tags"></a>Integrações de plataforma com marcas de índice de BLOB

As marcas do índice de BLOB não apenas ajudam a categorizar, gerenciar e pesquisar nos dados do blob, mas também fornecem integrações com outros recursos do serviço BLOB, como o [Gerenciamento do ciclo de vida](storage-lifecycle-management-concepts.md). 

### <a name="lifecycle-management"></a>Gerenciamento do ciclo de vida

Usando o novo blobIndexMatch como um filtro de regra no gerenciamento do ciclo de vida, você pode mover dados para camadas mais frias ou excluir dados com base nas marcas de índice aplicadas aos seus BLOBs. Isso permite que você seja mais granular em suas regras e só mova ou exclua dados se eles corresponderem aos critérios de marcas especificados.

Você pode definir uma correspondência de índice de blob como um conjunto de filtros autônomo em uma regra de ciclo de vida para aplicar ações em dados marcados. Ou você pode combinar uma correspondência de prefixo e uma correspondência de índice de BLOB para corresponder a conjuntos de dados mais específicos. A aplicação de vários filtros a uma regra de ciclo de vida trata-se de uma operação e lógica de forma que a ação seja aplicada somente se todos os critérios de filtro forem correspondentes. 

A regra de gerenciamento do ciclo de vida de exemplo a seguir se aplica a blobs de blocos no contêiner ' videofiles ' e blobs de camadas para armazenamento de arquivo morto somente se ```"Status" = 'Processed' AND "Source" == 'RAW'```os dados corresponderem aos critérios de marca de índice de blob de.

# <a name="portal"></a>[Portal](#tab/azure-portal)
![Exemplo de regra de correspondência de índice de BLOB para gerenciamento de ciclo de vida no portal do Azure](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>Permissões e autorização

Você pode autorizar o acesso ao índice de BLOB usando uma das seguintes abordagens:

- Usando o RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança do Azure Active Directory (Azure AD). A Microsoft recomenda usar o Azure AD para segurança superior e facilidade de uso. Para obter mais informações sobre como usar o Azure AD com operações de BLOB, consulte [autorizar o acesso a BLOBs e filas usando o Azure Active Directory](../common/storage-auth-aad.md).
- Usando uma SAS (assinatura de acesso compartilhado) para delegar o acesso ao índice de BLOB. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).
- Usando as chaves de acesso da conta para autorizar operações com chave compartilhada. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key).

As marcas de índice de blob são um subrecurso para os dados de BLOB. Um usuário com permissões ou um token SAS para ler ou gravar BLOBs pode não ter acesso às marcas de índice de BLOB. 

### <a name="role-based-access-control"></a>Controle de Acesso Baseado em Função 
Os chamadores usando uma [identidade do AAD](../common/storage-auth-aad.md) podem receber as seguintes permissões para operar em marcas de índice de BLOB. 

|   Operações de BLOB   |  Ação RBAC   |
|---------------------|----------------|
| Localizar blobs por marcas  | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/filtro |
| Definir marcas de BLOB         | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/marcas/gravação | 
| Obter marcas de BLOB         | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/marcas/leitura |

São necessárias permissões adicionais separadas dos dados de blob subjacentes para operar nas marcas. A função colaborador de dados de armazenamento de blob receberá todas as três permissões. O leitor de dados de blob de armazenamento receberá as permissões somente localizar blobs por marcas e obter marcas de BLOB.

### <a name="sas-permissions"></a>Permissões de SAS 
Os chamadores que usam uma [SAS (assinatura de acesso compartilhado)](../common/storage-sas-overview.md) podem receber permissões no escopo para operar em marcas de BLOB.
#### <a name="blob-sas"></a>SAS do blob
As permissões a seguir podem ser concedidas em uma SAS do serviço blob para permitir o acesso a marcas de índice de BLOB. Somente permissões de leitura e gravação de BLOB não são suficientes para permitir a leitura ou gravação de suas marcas de índice.

|  Permissão  |  Símbolo de URI  | Operações permitidas |
|--------------|--------------|--------------------|
|  Marcas de índice  |      t         | Obter e definir marcas de índice de BLOB para um blob |

#### <a name="container-sas"></a>SAS do contêiner
As permissões a seguir podem ser concedidas na SAS do serviço de contêiner para permitir a filtragem em marcas de BLOB.  A permissão de lista de BLOBs não é suficiente para permitir a filtragem de BLOBs por suas marcas de índice.

|  Permissão  |  Símbolo de URI  | Operações permitidas |
|--------------|--------------|--------------------|
| Marcas de índice     |      f       | Localizar BLOBs com marcas de índice de BLOB | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Escolhendo entre as marcas de índice de BLOB e metadados 
As marcas de índice de BLOB e os metadados fornecem a capacidade de armazenar propriedades de chave/valor arbitrárias definidas pelo usuário junto com um recurso de BLOB. Ambos podem ser recuperados e definidos diretamente, sem retornar ou alterar o conteúdo do blob. É possível utilizar ambas as marcas de índice e metadados.

No entanto, somente marcas de índice de blob são indexadas automaticamente e tornam-se consultáveis pelo serviço blob nativo. Os metadados não podem ser indexados nativamente e consultados a menos que você utilize um serviço separado, como [Azure Search](../../search/search-blob-ai-integration.md). Marcas de índice de blob também têm permissões adicionais para leitura/filtragem e gravação que são separadas dos dados de blob subjacentes. Os metadados utilizam as mesmas permissões que o blob e são retornados como cabeçalhos HTTP nas operações getBlob ou getblobproperties. As marcas de índice de blob são criptografadas em repouso usando uma [chave gerenciada pela Microsoft](../common/storage-service-encryption.md) , enquanto os metadados são criptografados em repouso usando a mesma chave de criptografia especificada para dados de BLOB. 

A tabela a seguir resume as diferenças entre as marcas de índice de BLOB e metadados:

|              |   Metadados   |   Marcas de índice de BLOB  |
|--------------|--------------|--------------------|
| **limites**         | Sem limite numérico; total de 8 KB; não diferencia maiúsculas de minúsculas | 10 marcas por blob máx. 768 bytes por marca; diferencia maiúsculas de minúsculas |
| **Atualizações**      | Não permitido na camada de arquivo morto; SetBlobMetadata substitui todos os metadados existentes; SetBlobMetadata altera a hora da última modificação do blob | Permitido para todas as camadas de acesso; SetBlobTags substitui todas as marcas existentes; SetBlobTags não altera a hora da última modificação do blob |
| **Storage**        | Armazenados com os dados do blob |  O subrecurso para os dados de BLOB | 
| **Indexação & consulta** | N/A nativamente; deve usar um serviço separado, como Azure Search | Sim, recursos de indexação e consulta nativos incorporados ao armazenamento de BLOBs |
| **Encripta** | Criptografado em repouso com a mesma chave de criptografia usada para dados de BLOB |  Criptografado em repouso com uma chave de criptografia gerenciada pela Microsoft |
| **Preços**   | O tamanho dos metadados está incluído nos custos de armazenamento de um blob |    Custo fixo por marca de índice | 
| **Resposta de cabeçalho** | Metadados retornados como cabeçalhos em getBlob e getblobproperties | TagCount retornado em getBlob ou getblobproperties; Marcas retornadas somente em GetBlobTags e ListBlobs |
| **Permissões**  |    Permissões de leitura ou gravação para dados de BLOB se estende aos metadados |    Permissões adicionais são necessárias para ler/filtrar ou gravar marcas |

## <a name="pricing"></a>Preços
O preço do índice de blob está atualmente em visualização pública e está sujeito a alterações para disponibilidade geral. Os clientes são cobrados pelo número total de marcas de índice de blob dentro de uma conta de armazenamento, com média durante o mês. Não há nenhum custo para o mecanismo de indexação. As solicitações para SetBlobTags, GetBlobTags e FindBlobsByTags são cobradas de acordo com seus respectivos tipos de operação. Consulte [preços do blob de blocos para saber mais](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Disponibilidade regional e suporte à conta de armazenamento

O índice de blob está disponível atualmente somente com contas Uso Geral v2 (GPv2). No portal do Azure, você pode atualizar uma conta de Uso Geral (GPv1) existente para uma conta do GPv2. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

Na visualização pública, o índice de blob está disponível no momento apenas nas seguintes regiões selecionadas:
- França Central
- Sul da França

Para começar, consulte [utilizar o índice de BLOB para gerenciar e localizar dados](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Consulte a seção condições deste artigo. Para se registrar na versão prévia, consulte a seção registrar sua assinatura deste artigo. Você deve registrar sua assinatura para poder usar o índice de BLOB em suas contas de armazenamento.

### <a name="register-your-subscription-preview"></a>Registrar sua assinatura (versão prévia)
Como o índice de blob está apenas em visualização pública, você precisará registrar sua assinatura antes de poder usar o recurso. Para enviar uma solicitação, execute os seguintes comandos da CLI ou do PowerShell.

#### <a name="register-by-using-powershell"></a>Registrar-se usando o PowerShell
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registrar usando CLI do Azure
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>Condições e problemas conhecidos (versão prévia)
Esta seção descreve os problemas e condições conhecidos na visualização pública atual do índice de BLOB. Assim como acontece com a maioria das visualizações, esse recurso não deve ser usado para cargas de trabalho de produção até alcançar GA, pois os comportamentos podem mudar.

-   Para visualização, você deve primeiro registrar sua assinatura antes de poder usar o índice de BLOB para sua conta de armazenamento nas regiões de visualização.
-   Atualmente, somente contas GPv2 têm suporte na versão prévia. As contas do datalake Gen2 do blob, BlockBlobStorage e do HNS habilitadas não têm suporte no momento com o índice de BLOB.
-   O carregamento de blobs de página com marcas de índice atualmente não mantém as marcas. Você deve definir as marcas depois de carregar um blob de páginas.
-   Quando a filtragem estiver no escopo de um único contêiner, @container o só poderá ser passado se todas as marcas de índice na expressão de filtro forem verificações de igualdade (chave = valor). 
-   Ao usar o operador Range com a condição AND, você só pode especificar o mesmo nome de chave de marca de índice (age > ' 013 ' e age < ' 100 ').
-   No momento, não há suporte para controle de versão e índice de BLOB. As marcas de índice de blob são preservadas para versões, mas atualmente não são passadas para o mecanismo de índice de BLOB.
-   Não há suporte para o failover de conta no momento. O índice de blob pode não ser atualizado corretamente após o failover.
-   O gerenciamento do ciclo de vida só dá suporte a verificações de igualdade com correspondência de índice de BLOB.
-   CopyBlob não copia marcas de índice de blob do blob de origem para o novo BLOB de destino. Você pode especificar as marcas que deseja aplicar ao blob de destino durante a operação de cópia. 
-   As marcas são persistidas na criação do instantâneo; no entanto, a promoção de um instantâneo não tem suporte no momento e pode resultar em um conjunto de marcas vazio.

## <a name="faq"></a>Perguntas frequentes

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>O índice de BLOBs pode me ajudar a filtrar e consultar conteúdo dentro de meus BLOBs? 
Não, as marcas de índice de blob podem ajudá-lo a encontrar os blobs que você está procurando. Se você precisar pesquisar em seus BLOBs, use aceleração de consulta ou Azure Search.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Marcas de índice de BLOB e marcas de Azure Resource Manager estão relacionadas?
Não, Azure Resource Manager marcas ajudam a organizar os recursos do plano de controle, como assinaturas, grupos de recursos e contas de armazenamento. As marcas de índice de blob fornecem gerenciamento e descoberta de objetos em recursos de plano de dados, como BLOBs em uma conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Consulte um exemplo de como utilizar o índice de BLOB. Consulte [utilizar o índice de BLOB para gerenciar e localizar dados](storage-blob-index-how-to.md)

