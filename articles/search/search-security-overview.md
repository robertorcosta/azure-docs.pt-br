---
title: Visão geral da segurança
titleSuffix: Azure Cognitive Search
description: O Azure Pesquisa Cognitiva é compatível com SOC 2, HIPAA e outras certificações. Conexão e criptografia de dados, autenticação e acesso de identidade por meio de identificadores de segurança de usuário e grupo em expressões de filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: cc02890cb5293e48a8065b63f4f9c799c5dda7f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85081032"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Segurança no Azure Pesquisa Cognitiva-visão geral

Este artigo descreve os principais recursos de segurança do Azure Pesquisa Cognitiva que podem proteger o conteúdo e as operações. 

+ Na camada de armazenamento, a criptografia em repouso é fornecida no nível da plataforma, mas Pesquisa Cognitiva também oferece uma opção de "criptografia dupla" para os clientes que desejam a proteção dupla das chaves gerenciadas pelo usuário e pela Microsoft.

+ A segurança de entrada protege o ponto de extremidade do serviço de pesquisa em níveis crescentes de segurança: de chaves de API na solicitação, às regras de entrada no firewall, para pontos de extremidade privados que protegem totalmente o serviço da Internet pública.

+ A segurança de saída se aplica a indexadores que efetuam pull de conteúdo de fontes externas. Para solicitações de saída, configure uma identidade gerenciada para tornar a pesquisa um serviço confiável ao acessar dados do armazenamento do Azure, SQL do Azure, Cosmos DB ou outras fontes de dados do Azure. Uma identidade gerenciada é um substituto de credenciais ou chaves de acesso na conexão. A segurança de saída não é abordada neste artigo. Para obter mais informações sobre esse recurso, consulte [conectar-se a uma fonte de dados usando uma identidade gerenciada](search-howto-managed-identities-data-sources.md).

Assista a este vídeo rápido do ritmo para obter uma visão geral da arquitetura de segurança e de cada categoria de recurso.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="encrypted-transmissions-and-storage"></a>Armazenamento e transmissões criptografados

A criptografia é generalizada no Azure Pesquisa Cognitiva, começando com conexões e transmissões, estendendo para conteúdo armazenado em disco. Para serviços de pesquisa na Internet pública, o Azure Pesquisa Cognitiva escuta na porta HTTPS 443. Todas as conexões de cliente para serviço usam a criptografia TLS 1,2. Não há suporte para versões anteriores (1,0 ou 1,1).

### <a name="data-encryption-at-rest"></a>Criptografia de dados em repouso

O Azure Pesquisa Cognitiva armazena definições de índice e conteúdo, definições de fonte de dados, definições de indexador, definições de Skills e mapas de sinônimos.

Na camada de armazenamento, os dados são criptografados em disco usando chaves gerenciadas pela Microsoft. Você não pode ativar ou desativar a criptografia ou exibir as configurações de criptografia no portal ou programaticamente. A criptografia é totalmente internalizada, sem impacto mensurável na indexação do tempo de conclusão ou do tamanho do índice. Isso ocorre automaticamente em toda a indexação, incluindo em atualizações incrementais em um índice que não está totalmente criptografado (criado antes de janeiro de 2018).

Internamente, a criptografia é baseada na [Criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) do 256 bits.

> [!NOTE]
> A criptografia em repouso foi anunciada em 24 de janeiro de 2018 e se aplica a todas as camadas de serviço, incluindo a camada gratuita, em todas as regiões. Para a criptografia completa, os índices criados antes dessa data precisam ser removidos e recompilados para que a criptografia ocorra. Caso contrário, somente os novos dados adicionados após 24 de janeiro são criptografados.

### <a name="customer-managed-key-cmk-encryption"></a>Criptografia de chave gerenciada pelo cliente (CMK)

Os clientes que desejam proteção de armazenamento adicional podem criptografar dados e objetos antes de serem armazenados e criptografados em disco. Essa abordagem se baseia em uma chave de Propriedade do usuário, gerenciada e armazenada por meio de Azure Key Vault, independentemente da Microsoft. A criptografia de conteúdo antes de ser criptografada no disco é conhecida como "criptografia dupla". No momento, você pode criptografar seletivamente os índices e os mapas de sinônimos. Para obter mais informações, consulte [chaves de criptografia gerenciadas pelo cliente no Azure pesquisa cognitiva](search-security-manage-encryption-keys.md).

> [!NOTE]
> A criptografia CMK está geralmente disponível para os serviços de pesquisa criados após janeiro de 2019. Não há suporte para serviços gratuitos (compartilhados). 
>
>Habilitar esse recurso aumentará o tamanho do índice e diminuirá o desempenho da consulta. Com base nas observações até a data, você pode esperar um aumento de 30%-60% nos tempos de consulta, embora o desempenho real varie dependendo da definição de índice e dos tipos de consultas. Devido a esse impacto no desempenho, recomendamos que você habilite apenas esse recurso em índices que realmente o exigem.

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Segurança de entrada e proteção de ponto de extremidade

Os recursos de segurança de entrada protegem o ponto de extremidade do serviço de pesquisa por meio de níveis crescentes de segurança e complexidade. Primeiro, todas as solicitações exigem uma chave de API para acesso autenticado. Em segundo lugar, opcionalmente, você pode definir regras de firewall que limitam o acesso a endereços IP específicos. Para proteção avançada, uma terceira opção é habilitar o link privado do Azure para proteger seu ponto de extremidade de serviço de todo o tráfego da Internet.

### <a name="public-access-using-api-keys"></a>Acesso público usando chaves de API

Por padrão, um serviço de pesquisa é acessado por meio da nuvem pública, usando a autenticação baseada em chave para acesso de administrador ou de consulta ao ponto de extremidade do serviço de pesquisa. Uma chave de api é uma cadeia de caracteres composta de letras e números gerados aleatoriamente. O tipo de chave (administrador ou consulta) determina o nível de acesso. O envio de uma chave válida é considerado uma prova de que a solicitação se origina de uma entidade confiável. 

Há dois níveis de acesso ao serviço de pesquisa, habilitados pelas seguintes chaves de API:

+ Chave de administração (permite acesso de leitura/gravação para operações [criar-ler-atualizar-excluir](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) no serviço de pesquisa)

+ Chave de consulta (permite acesso somente leitura à coleção de documentos de um índice)

*As chaves de administração* são criadas quando o serviço é provisionado. Há duas chaves de administração, designadas como *primária* e *secundária* para mantê-las de forma linear, mas na verdade elas são intercambiáveis. Cada serviço tem duas chaves admin para que você possa derrubar uma sem perder o acesso ao seu serviço. Você pode [regenerar a chave de administração](search-security-api-keys.md#regenerate-admin-keys) periodicamente por práticas recomendadas de segurança do Azure, mas não pode adicionar à contagem total de chaves de administração. Há um máximo de duas chaves de administração por serviço de pesquisa.

*As chaves de consulta* são criadas conforme necessário e são projetadas para aplicativos cliente que emitem consultas. Você pode criar até 50 chaves de consulta. No código do aplicativo, você especifica a URL de pesquisa e uma chave de API de consulta para permitir acesso somente leitura à coleção de documentos de um índice específico. Juntos, o ponto de extremidade, uma chave de api para acesso somente leitura e um índice de destino definem o nível de acesso e escopo da conexão de seu aplicativo cliente.

A autenticação é necessária em cada solicitação, em que cada solicitação é composta por uma chave obrigatória, uma operação e um objeto. Quando encadeados, os dois níveis de permissão (completo e somente leitura) e o contexto (por exemplo, uma operação de consulta em um índice) são suficientes para fornecer segurança completa nas operações de serviço. Para obter mais informações sobre chaves, consulte [Criar e gerenciar api-keys](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>Acesso restrito por IP

Para controlar ainda mais o acesso ao serviço de pesquisa, você pode criar regras de firewall de entrada que permitem o acesso a um endereço IP específico ou a um intervalo de endereços IP. Todas as conexões de cliente devem ser feitas por meio de um endereço IP permitido ou a conexão é negada.

Você pode usar o portal para [Configurar o acesso de entrada](service-configure-firewall.md). 

Como alternativa, você pode usar as APIs REST de gerenciamento. A versão da API 2020-03-13, com o parâmetro [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) , permite restringir o acesso ao serviço identificando endereços IP, individualmente ou em um intervalo, que você deseja conceder acesso ao serviço de pesquisa. 

### <a name="private-endpoint-no-internet-traffic"></a>Ponto de extremidade privado (sem tráfego de Internet)

Um [ponto de extremidade privado](../private-link/private-endpoint-overview.md) para o Azure pesquisa cognitiva permite que um cliente em uma [rede virtual](../virtual-network/virtual-networks-overview.md) acesse dados com segurança em um índice de pesquisa por um [link privado](../private-link/private-link-overview.md). 

O ponto de extremidade privado usa um endereço IP do espaço de endereço de rede virtual para conexões com o serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública. Uma VNET permite a comunicação segura entre os recursos, com sua rede local, bem como com a Internet. 

Embora essa solução seja a mais segura, usar serviços adicionais é um custo adicional, portanto, certifique-se de ter uma compreensão clara dos benefícios antes de mergulhar no. ou mais informações sobre os custos, consulte a [página de preços](https://azure.microsoft.com/pricing/details/private-link/). Para obter mais informações sobre como esses componentes funcionam em conjunto, Assista ao vídeo na parte superior deste artigo. A cobertura da opção de ponto de extremidade particular começa em 5:48 no vídeo. Para obter instruções sobre como configurar o ponto de extremidade, consulte [criar um ponto de extremidade privado para o Azure pesquisa cognitiva](service-create-private-endpoint.md).

## <a name="index-access"></a>Acesso de índice

No Azure Pesquisa Cognitiva, um índice individual não é um objeto protegível. Em vez disso, o acesso a um índice é determinado na camada de serviço (acesso de leitura ou gravação ao serviço), juntamente com o contexto de uma operação.

Para acesso do usuário final, é possível estruturar solicitações de consulta para conectar usando uma chave de consulta, o que torna qualquer solicitação somente leitura e inclui o índice específico usado pelo aplicativo. Em uma solicitação de consulta, não há o conceito de adicionar índices ou acessar vários índices simultaneamente. Portanto, todas as solicitações têm um único índice de destino por definição. Como tal, a construção da solicitação de consulta em si (uma chave mais um único índice de destino) define o limite de segurança.

O acesso de administrador e de desenvolvedor aos índices não é diferenciado: ambos precisam de acesso de gravação para criar, excluir e atualizar os objetos gerenciados pelo serviço. Qualquer pessoa com uma chave de administrador para seu serviço pode ler, modificar ou excluir um índice no mesmo serviço. Para proteção contra exclusão de índices acidental ou mal-intencionada, o controle do código-fonte interno para ativos de código é a solução para reverter uma modificação ou exclusão de índice indesejada. O Azure Pesquisa Cognitiva tem um failover no cluster para garantir a disponibilidade, mas não armazena ou executa o código proprietário usado para criar ou carregar índices.

Para soluções de multilocação que exigem limites de segurança no nível do índice, essas soluções normalmente incluem uma camada intermediária que os clientes usam para lidar com isolamento de índice. Para obter mais informações sobre o caso de uso multilocatário, consulte [padrões de design para aplicativos SaaS multilocatários e pesquisa cognitiva do Azure](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Acesso do usuário

Como um usuário acessa um índice e outros objetos é determinado pelo tipo de chave de API na solicitação. A maioria dos desenvolvedores cria e atribui [*chaves de consulta*](search-security-api-keys.md) para solicitações de pesquisa no lado do cliente. Uma chave de consulta concede acesso somente leitura ao conteúdo pesquisável dentro do índice.

Se você precisar de controle granular por usuário sobre os resultados da pesquisa, poderá criar filtros de segurança em suas consultas, retornando documentos associados a uma determinada identidade de segurança. Em vez de funções predefinidas e atribuições de função, o controle de acesso baseado em identidade é implementado como um *filtro* que corta os resultados da pesquisa de documentos e conteúdo com base em identidades. A tabela a seguir descreve duas abordagens para cortar resultados da pesquisa com conteúdo não autorizado.

| Abordagem | Descrição |
|----------|-------------|
|[Filtragem de segurança com base nos filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para implementar o controle de acesso de identidade do usuário. Ele aborda a adição de identificadores de segurança a um índice e explica a filtragem em relação a esse campo para cortar resultados de conteúdo proibido. |
|[Filtragem de segurança com base em Identidades do Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo aprofunda o artigo anterior, fornecendo etapas para recuperar identidades do Azure Active Directory (AAD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma de nuvem do Azure. |

## <a name="administrative-rights"></a>Direitos administrativos

O [RBAC (acesso baseado em função)](../role-based-access-control/overview.md) é um sistema de autorização criado em [Azure Resource Manager](../azure-resource-manager/management/overview.md) para o provisionamento de recursos do Azure. No Azure Pesquisa Cognitiva, o Resource Manager é usado para criar ou excluir o serviço, gerenciar chaves de API e dimensionar o serviço. Dessa forma, as atribuições de função do RBAC determinarão quem pode executar essas tarefas, independentemente de estarem usando o [portal](search-manage.md), o [PowerShell](search-manage-powershell.md)ou as [APIs REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

Por outro lado, os direitos de administrador sobre o conteúdo hospedado no serviço, como a capacidade de criar ou excluir um índice, são conferidos por meio de chaves de API, conforme descrito na [seção anterior](#index-access).

> [!TIP]
> Usando mecanismos do Azure, você pode bloquear uma assinatura ou um recurso para impedir a exclusão acidental ou não autorizada de seu serviço de pesquisa por usuários com direitos de administrador. Para obter mais informações, consulte [Bloquear recursos para evitar a exclusão inesperada](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certificações e conformidade

O Azure Pesquisa Cognitiva foi certificado em conformidade com vários padrões globais, regionais e específicos do setor para a nuvem pública e o Azure governamental. Para obter a lista completa, baixe o [White Paper de **ofertas de conformidade Microsoft Azure** ](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) na página de relatórios oficiais de auditoria.

## <a name="see-also"></a>Consulte também

+ [Conceitos básicos de segurança do Azure](../security/fundamentals/index.yml)
+ [Segurança do Azure](https://azure.microsoft.com/overview/security)
+ [Central de Segurança do Azure](../security-center/index.yml)