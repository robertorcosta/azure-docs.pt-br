---
title: Visão geral da segurança
titleSuffix: Azure Cognitive Search
description: O Azure Pesquisa Cognitiva é compatível com SOC 2, HIPAA e outras certificações. Conexão e criptografia de dados, autenticação e acesso de identidade por meio de identificadores de segurança de usuário e grupo em expressões de filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: c9f0f496bfdb31e0c7cb45a07c87ea238d031e34
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928761"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Segurança no Azure Pesquisa Cognitiva-visão geral

Este artigo descreve os principais recursos de segurança do Azure Pesquisa Cognitiva que podem proteger o conteúdo e as operações.

+ Na camada de armazenamento, a criptografia em repouso é interna para todo o conteúdo gerenciado pelo serviço salvo em disco, incluindo índices, mapas de sinônimos e as definições de indexadores, fontes de dados e habilidades. O Azure Pesquisa Cognitiva também dá suporte à adição de chaves gerenciadas pelo cliente (CMK) para criptografia complementar de conteúdo indexado. Para serviços criados após 1 2020 de agosto, a criptografia CMK se estende aos dados em discos temporários, para criptografia dupla completa de conteúdo indexado.

+ A segurança de entrada protege o ponto de extremidade do serviço de pesquisa em níveis crescentes de segurança: de chaves de API na solicitação, às regras de entrada no firewall, para pontos de extremidade privados que protegem totalmente o serviço da Internet pública.

+ A segurança de saída se aplica a indexadores que efetuam pull de conteúdo de fontes externas. Para solicitações de saída, configure uma identidade gerenciada para tornar a pesquisa um serviço confiável ao acessar dados do armazenamento do Azure, SQL do Azure, Cosmos DB ou outras fontes de dados do Azure. Uma identidade gerenciada é um substituto de credenciais ou chaves de acesso na conexão. A segurança de saída não é abordada neste artigo. Para obter mais informações sobre esse recurso, consulte [conectar-se a uma fonte de dados usando uma identidade gerenciada](search-howto-managed-identities-data-sources.md).

Assista a este vídeo rápido do ritmo para obter uma visão geral da arquitetura de segurança e de cada categoria de recurso.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>Armazenamento e transmissões criptografados

No Azure Pesquisa Cognitiva, a criptografia começa com conexões e transmissões e se estende ao conteúdo armazenado em disco. Para serviços de pesquisa na Internet pública, o Azure Pesquisa Cognitiva escuta na porta HTTPS 443. Todas as conexões de cliente para serviço usam a criptografia TLS 1,2. Não há suporte para versões anteriores (1,0 ou 1,1).

Para dados manipulados internamente pelo serviço de pesquisa, a tabela a seguir descreve os [modelos de criptografia de dados](../security/fundamentals/encryption-models.md). Alguns recursos, como armazenamento de conhecimento, enriquecimento incremental e indexação baseada em indexador, leitura ou gravação em estruturas de dados em outros serviços do Azure. Esses serviços têm seus próprios níveis de suporte de criptografia separados do Azure Pesquisa Cognitiva.

| Modelo | Novas&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Requirement&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Restrições | Aplica-se a |
|------------------|-------|-------------|--------------|------------|
| criptografia do lado do servidor | Chaves gerenciadas pela Microsoft | Nenhum (interno) | Nenhum, disponível em todas as camadas, em todas as regiões, para o conteúdo criado após janeiro de 24 2018. | Conteúdo (índices e mapas de sinônimos) e definições (indexadores, fontes de dados, habilidades) |
| criptografia do lado do servidor | chaves gerenciadas pelo cliente | Cofre de Chave do Azure | Disponível em camadas faturáveis, em todas as regiões, para conteúdo criado depois de janeiro de 2019. | Conteúdo (mapas e índices de sinônimos) em discos de dados |
| criptografia dupla do lado do servidor | chaves gerenciadas pelo cliente | Cofre de Chave do Azure | Disponível em camadas faturáveis, em regiões selecionadas, em serviços de pesquisa depois de agosto de 1 2020. | Conteúdo (mapas e índices de sinônimos) em discos de dados e discos temporários |

### <a name="service-managed-keys"></a>Chaves gerenciadas pelo serviço

A criptografia gerenciada por serviço é uma operação interna da Microsoft, com base no [Azure criptografia do serviço de armazenamento](../storage/common/storage-service-encryption.md), usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits. Ele ocorre automaticamente em toda a indexação, incluindo em atualizações incrementais para índices que não são totalmente criptografados (criados antes de janeiro de 2018).

### <a name="customer-managed-keys-cmk"></a>Chaves gerenciadas pelo cliente (CMK)

Chaves gerenciadas pelo cliente exigem um serviço Faturável adicional, Azure Key Vault, que pode estar em uma região diferente, mas na mesma assinatura, como o Azure Pesquisa Cognitiva. Habilitar a criptografia CMK aumentará o tamanho do índice e reduzirá o desempenho da consulta. Com base nas observações até a data, você pode esperar um aumento de 30%-60% nos tempos de consulta, embora o desempenho real varie dependendo da definição de índice e dos tipos de consultas. Devido a esse impacto no desempenho, recomendamos que você habilite apenas esse recurso em índices que realmente o exigem. Para obter mais informações, consulte [Configurar chaves de criptografia gerenciadas pelo cliente no Azure pesquisa cognitiva](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Criptografia dupla

No Azure Pesquisa Cognitiva, a criptografia dupla é uma extensão de CMK. Ele é entendido como criptografia de duas dobras (uma vez por CMK e novamente pelas chaves gerenciadas pelo serviço) e abrangente no escopo, abrangendo o armazenamento de longo prazo que é gravado em um disco de dados e o armazenamento de curto prazo gravado em discos temporários. A diferença entre CMK antes de agosto de 1 2020 e posteriores e o que torna o CMK um recurso de criptografia dupla no Azure Pesquisa Cognitiva é a criptografia adicional de dados em repouso em discos temporários.

A criptografia dupla está disponível atualmente em novos serviços que são criados nessas regiões após 1º de agosto:

+ Oeste dos EUA 2
+ Leste dos EUA
+ Centro-Sul dos Estados Unidos
+ Gov. dos EUA – Virgínia
+ Governo dos EUA do Arizona

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

Como alternativa, você pode usar as APIs REST de gerenciamento. A versão da API 2020-03-13, com o parâmetro [IpRule](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) , permite restringir o acesso ao serviço identificando endereços IP, individualmente ou em um intervalo, que você deseja conceder acesso ao serviço de pesquisa.

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

| Abordagem | DESCRIÇÃO |
|----------|-------------|
|[Filtragem de segurança com base nos filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para implementar o controle de acesso de identidade do usuário. Ele aborda a adição de identificadores de segurança a um índice e explica a filtragem em relação a esse campo para cortar resultados de conteúdo proibido. |
|[Filtragem de segurança com base em Identidades do Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo aprofunda o artigo anterior, fornecendo etapas para recuperar identidades do Azure Active Directory (AAD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma de nuvem do Azure. |

## <a name="administrative-rights"></a>Direitos administrativos

O Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) do Azure é um sistema de autorização criado em [Azure Resource Manager](../azure-resource-manager/management/overview.md) para o provisionamento de recursos do Azure. No Azure Pesquisa Cognitiva, o Resource Manager é usado para criar ou excluir o serviço, gerenciar chaves de API e dimensionar o serviço. Dessa forma, as atribuições de função do Azure determinarão quem pode executar essas tarefas, independentemente de estarem usando o [portal](search-manage.md), o [PowerShell](search-manage-powershell.md)ou as [APIs REST de gerenciamento](/rest/api/searchmanagement/search-howto-management-rest-api).

Por outro lado, os direitos de administrador sobre o conteúdo hospedado no serviço, como a capacidade de criar ou excluir um índice, são conferidos por meio de chaves de API, conforme descrito na [seção anterior](#index-access).

> [!TIP]
> Usando mecanismos do Azure, você pode bloquear uma assinatura ou um recurso para impedir a exclusão acidental ou não autorizada de seu serviço de pesquisa por usuários com direitos de administrador. Para obter mais informações, consulte [Bloquear recursos para evitar a exclusão inesperada](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certificações e conformidade

O Azure Pesquisa Cognitiva foi certificado em conformidade com vários padrões globais, regionais e específicos do setor para a nuvem pública e o Azure governamental. Para obter a lista completa, baixe o [White Paper de **ofertas de conformidade Microsoft Azure** ](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) na página de relatórios oficiais de auditoria.

Para fins de conformidade, você pode usar [Azure Policy](../governance/policy/overview.md) para implementar as práticas recomendadas de alta segurança do [benchmark de segurança do Azure](../security/benchmarks/introduction.md). O benchmark de segurança do Azure é uma coleção de recomendações de segurança, codificados em controles de segurança que são mapeados para as principais ações que você deve executar para reduzir as ameaças a serviços e dados. Atualmente, há 11 controles de segurança, incluindo [segurança de rede](../security/benchmarks/security-control-network-security.md), [registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md)e [proteção de dados](../security/benchmarks/security-control-data-protection.md) para citar alguns.

Azure Policy é um recurso interno do Azure que ajuda a gerenciar a conformidade de vários padrões, incluindo os do benchmark de segurança do Azure. Para benchmarks conhecidos, o Azure Policy fornece definições internas que fornecem critérios, bem como uma resposta acionável que resolve a não conformidade.

Para o Azure Pesquisa Cognitiva, há atualmente uma definição interna. É para o log de diagnóstico. Com esse interno, você pode atribuir uma política que identifica qualquer serviço de pesquisa que não tem o log de diagnóstico e, em seguida, ativa-o. Para obter mais informações, consulte [Azure Policy controles de conformidade regulatória para o pesquisa cognitiva do Azure](security-controls-policy.md).

## <a name="see-also"></a>Confira também

+ [Conceitos básicos de segurança do Azure](../security/fundamentals/index.yml)
+ [Segurança do Azure](https://azure.microsoft.com/overview/security)
+ [Central de Segurança do Azure](../security-center/index.yml)