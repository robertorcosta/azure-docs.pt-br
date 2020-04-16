---
title: Segurança e privacidade de dados
titleSuffix: Azure Cognitive Search
description: O Azure Cognitive Search está em conformidade com as certificações SOC 2, HIPAA e outras. Criptografia de conexão e dados, autenticação e acesso à identidade através de identificadores de segurança de usuários e grupos em expressões de filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: fe7d076fab6a70736843fc644cd56bef44a55df2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415118"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Segurança e privacidade de dados na Pesquisa Cognitiva do Azure

Recursos abrangentes de segurança e controles de acesso são incorporados ao Azure Cognitive Search para garantir que o conteúdo privado permaneça assim. Este artigo enumera os recursos de segurança e a conformidade de padrões incorporados na Pesquisa Cognitiva do Azure.

A arquitetura de segurança do Azure Cognitive Search abrange segurança física, transmissões criptografadas, armazenamento criptografado e conformidade de padrões em toda a plataforma. Operacionalmente, o Azure Cognitive Search só aceita solicitações autenticadas. Opcionalmente, é possível adicionar controles de acesso por usuário ao conteúdo por meio de filtros de segurança. Este artigo aborda a segurança em cada camada, mas é focado principalmente em como os dados e operações são protegidos na Pesquisa Cognitiva do Azure.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformidade com padrões: ISO 27001, SOC 2, HIPAA

O Azure Cognitive Search é certificado para os seguintes padrões, conforme [anunciado em junho de 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Conformidade com SOC 2 Tipo 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Para o relatório completo, acesse [Azure - e relatório do Microsoft Azure Governamental SOC 2 Tipo II](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Lei americana HIPAA (Health Insurance Portability and Accountability Act)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Parte 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS Nível 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

A conformidade com padrões se aplica a recursos geralmente disponíveis. As versões prévias dos recursos são certificadas quando mudam para disponibilidade geral e não devem ser utilizadas em soluções com requisitos de padrões estritos. A certificação de conformidade está documentada em [Visão geral de conformidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e na [Central de Confiabilidade](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Armazenamento e transmissão criptografados

A criptografia se estende por todo o pipeline de indexação: desde conexões, passando pela transmissão, até dados indexados armazenados na Pesquisa Cognitiva do Azure.

| Camada de segurança | Descrição |
|----------------|-------------|
| Criptografia em trânsito <br>(HTTPS/SSL/TLS) | A Pesquisa Cognitiva do Azure ouve na porta HTTPS 443. Na plataforma, as conexões com os serviços do Azure são criptografadas. <br/><br/>Todas as interações de pesquisa cognitiva do Cliente para serviço são capazes de SSL/TLS 1.2.  Certifique-se de usar o TLSv1.2 para conexões SSL ao seu serviço.|
| Criptografia em repouso <br>Chaves gerenciadas pela Microsoft | A criptografia é totalmente internalizada no processo de indexação, sem nenhum impacto mensurável no tempo para conclusão da indexação ou no tamanho do índice. Isso ocorre automaticamente em toda a indexação, incluindo em atualizações incrementais em um índice que não está totalmente criptografado (criado antes de janeiro de 2018).<br><br>Internamente, a criptografia é baseada na [Criptografia do Serviço de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) do 256 bits.<br><br> A criptografia é interna para o Azure Cognitive Search, com certificados e chaves de criptografia gerenciados internamente pela Microsoft, e aplicados universalmente. Não é possível ativar ou desativar a criptografia, gerenciar ou substituir suas próprias chaves ou exibir configurações de criptografia no portal ou de forma programática.<br><br>A criptografia em repouso foi anunciada em 24 de janeiro de 2018 e se aplica a todos os níveis de serviço, incluindo o nível gratuito, em todas as regiões. Para a criptografia completa, os índices criados antes dessa data precisam ser removidos e recompilados para que a criptografia ocorra. Caso contrário, somente os novos dados adicionados após 24 de janeiro são criptografados.|
| Criptografia em repouso <br>Chaves gerenciadas do cliente | A criptografia com chaves gerenciadas pelo cliente agora está geralmente disponível para serviços de pesquisa criados em ou após janeiro de 2019. Não é suportado em serviços gratuitos (compartilhados).<br><br>Os índices de pesquisa cognitiva do Azure e os mapas de sinônimos agora podem ser criptografados em repouso com chaves gerenciadas pelo cliente no Azure Key Vault. Para saber mais, consulte [Gerenciar chaves de criptografia na Pesquisa Cognitiva do Azure](search-security-manage-encryption-keys.md).<br><br>Esse recurso não está substituindo a criptografia padrão em repouso, mas sim aplicada além dela.<br><br>A habilitação desse recurso aumentará o tamanho do índice e degradará o desempenho da consulta. Com base em observações até o momento, você pode esperar ver um aumento de 30%-60% nos tempos de consulta, embora o desempenho real varie dependendo da definição do índice e tipos de consultas. Devido a esse impacto de desempenho, recomendamos que você só habilite esse recurso em índices que realmente o requerem.

## <a name="azure-wide-user-access-controls"></a>Controles de acesso do usuário em todo o Azure

Vários mecanismos de segurança estão disponíveis em todo o Azure e, portanto, automaticamente disponíveis para os recursos de Pesquisa Cognitiva do Azure que você cria.

+ [Bloqueios no nível de recurso ou da assinatura para impedir a exclusão](../azure-resource-manager/management/lock-resources.md)
+ [RBAC (controle de acesso baseado em função) para controlar o acesso a informações e operações administrativas](../role-based-access-control/overview.md)

Todos os serviços do Azure oferecem suporte a RBAC para definir níveis de acesso consistentes em todos os serviços. Por exemplo, a visualização de dados confidenciais, como a chave de admin, é restrita às funções Proprietário e Contribuinte. No entanto, a visualização do status do serviço está disponível para membros de qualquer função. O RBAC fornece funções de Leitor, Colaborador e Proprietário. Por padrão, todos os administradores de serviço são membros da função Proprietário.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Acesso ao ponto final

### <a name="public-access"></a>Acesso público

O Azure Cognitive Search herda as salvaguardas de segurança da plataforma Azure e fornece sua própria autenticação baseada em chaves. Uma chave de api é uma cadeia de caracteres composta de letras e números gerados aleatoriamente. O tipo de chave (administrador ou consulta) determina o nível de acesso. O envio de uma chave válida é considerado uma prova de que a solicitação se origina de uma entidade confiável. 

Há dois níveis de acesso ao seu serviço de pesquisa, habilitado por dois tipos de chaves:

* Acesso admin (válido para qualquer operação de leitura e gravação do serviço)
* Acesso à consulta (válido para operações somente leitura, como consultas, contra a coleta de documentos de um índice)

*As chaves de admin* são criadas quando o serviço é provisionado. Há duas chaves de administração, designadas como *primária* e *secundária* para mantê-las de forma linear, mas na verdade elas são intercambiáveis. Cada serviço tem duas chaves admin para que você possa derrubar uma sem perder o acesso ao seu serviço. Você pode [regenerar a chave de administrador](search-security-api-keys.md#regenerate-admin-keys) periodicamente por práticas recomendadas de segurança do Azure, mas não pode adicionar à contagem total de tecla de administrador. Há um máximo de duas chaves de admin por serviço de pesquisa.

*As chaves de consulta* são criadas conforme necessário e são projetadas para aplicativos clientes que emitem consultas. Você pode criar até 50 chaves de consulta. No código do aplicativo, você especifica a URL de pesquisa e uma api-key de consulta para permitir acesso somente à coleção de documentos de um índice específico. Juntos, o ponto de extremidade, uma chave de api para acesso somente leitura e um índice de destino definem o nível de acesso e escopo da conexão de seu aplicativo cliente.

A autenticação é necessária em cada solicitação, em que cada solicitação é composta por uma chave obrigatória, uma operação e um objeto. Quando encadeados, os dois níveis de permissão (completo e somente leitura) e o contexto (por exemplo, uma operação de consulta em um índice) são suficientes para fornecer segurança completa nas operações de serviço. Para obter mais informações sobre chaves, consulte [Criar e gerenciar api-keys](search-security-api-keys.md).

### <a name="restricted-access"></a>Acesso restrito

Quando você tem um serviço público e deseja restringir o uso do serviço, você pode usar a regra de restrição de IP na versão da API Management REST: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). O IpRule permite que você restrinja o acesso ao seu serviço identificando endereços IP, individualmente ou em uma faixa, que você deseja conceder acesso ao seu serviço de pesquisa. 

### <a name="private-access"></a>Acesso privado

[Os pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para a Pesquisa Cognitiva do Azure permitem que um cliente em uma rede virtual acesse com segurança dados em um índice de pesquisa sobre um [Link Privado](https://docs.microsoft.com/azure/private-link/private-link-overview). O ponto final privado usa um endereço IP do espaço de endereço de rede virtual para o seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de busca atravessa a rede virtual e um link privado na rede backbone da Microsoft, eliminando a exposição da internet pública.

[A Rede Virtual Azure (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) permite uma comunicação segura entre os recursos, com sua rede local e também com a Internet. 

## <a name="index-access"></a>Acesso de índice

Na Pesquisa Cognitiva do Azure, um índice individual não é um objeto securável. Em vez disso, o acesso a um índice é determinado na camada de serviço (acesso de leitura ou gravação), junto com o contexto de uma operação.

Para acesso do usuário final, é possível estruturar solicitações de consulta para conectar usando uma chave de consulta, o que torna qualquer solicitação somente leitura e inclui o índice específico usado pelo aplicativo. Em uma solicitação de consulta, não há o conceito de adicionar índices ou acessar vários índices simultaneamente. Portanto, todas as solicitações têm um único índice de destino por definição. Como tal, a construção da solicitação de consulta em si (uma chave mais um único índice de destino) define o limite de segurança.

O acesso de administrador e de desenvolvedor aos índices não é diferenciado: ambos precisam de acesso de gravação para criar, excluir e atualizar os objetos gerenciados pelo serviço. Qualquer pessoa com uma chave de administrador para seu serviço pode ler, modificar ou excluir um índice no mesmo serviço. Para proteção contra exclusão de índices acidental ou mal-intencionada, o controle do código-fonte interno para ativos de código é a solução para reverter uma modificação ou exclusão de índice indesejada. O Azure Cognitive Search tem failover dentro do cluster para garantir a disponibilidade, mas não armazena ou executa seu código proprietário usado para criar ou carregar índices.

Para soluções de multilocação que exigem limites de segurança no nível do índice, essas soluções normalmente incluem uma camada intermediária que os clientes usam para lidar com isolamento de índice. Para obter mais informações sobre o caso de uso de vários [inquilinos, consulte padrões de design para aplicações SaaS multilocatários e Pesquisa Cognitiva Azure](search-modeling-multitenant-saas-applications.md).

## <a name="authentication"></a>Autenticação

### <a name="admin-access"></a>Acesso ao admin

[O RBAC (Role-based Access, acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview) determina se você tem acesso a controles sobre o serviço e seu conteúdo. Se você é proprietário ou colaborador em um serviço de pesquisa cognitiva do Azure, você pode usar o portal ou o módulo PowerShell **Az.Search** para criar, atualizar ou excluir objetos no serviço. Você também pode usar a [API REST do Azure Cognitive Search Management](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="user-access"></a>Acesso do usuário

Por padrão, o acesso de usuário a um índice é determinado pela chave de acesso na solicitação de consulta. A maioria dos desenvolvedores cria e atribui [*chaves de consulta*](search-security-api-keys.md) para solicitações de pesquisa no lado do cliente. Uma chave de consulta concede acesso de leitura para todo o conteúdo do índice.

Se você preferir o acesso granular, o controle do conteúdo por usuário, pode construir filtros de segurança em suas consultas, retornando os documentos associados a uma identidade de segurança fornecida. Em vez de funções e atribuições predefinidas, o controle de acesso baseado em identidade é implementado como um *filtro* que corta resultados de pesquisa de documentos e conteúdo com base em identidades. A tabela a seguir descreve duas abordagens para cortar resultados da pesquisa com conteúdo não autorizado.

| Abordagem | Descrição |
|----------|-------------|
|[Filtragem de segurança com base nos filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para implementar o controle de acesso de identidade do usuário. Ele aborda a adição de identificadores de segurança a um índice e explica a filtragem em relação a esse campo para cortar resultados de conteúdo proibido. |
|[Filtragem de segurança com base em Identidades do Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo aprofunda o artigo anterior, fornecendo etapas para recuperar identidades do Azure Active Directory (AAD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma de nuvem do Azure. |

## <a name="table-permissioned-operations"></a>Tabela: operações permitidas

A tabela a seguir resume as operações permitidas na Pesquisa Cognitiva do Azure e qual chave desbloqueia o acesso a uma determinada operação.

| Operação | Permissões |
|-----------|-------------------------|
| Criar um serviço | Titular da assinatura do Azure|
| Dimensionar um serviço | Tecla de admin, Proprietário de RBAC ou Contribuinte no recurso  |
| Excluir um serviço | Tecla de admin, Proprietário de RBAC ou Contribuinte no recurso |
| Criar, modificar e excluir objetos no serviço: <br>Índices e partes do componente (incluindo definições do analisador, perfis de pontuação, opções de CORS), indexadores, fontes de dados, sinônimos, sugestores. | Tecla de admin, Proprietário de RBAC ou Contribuinte no recurso  |
| Consultar um índice | Chave de consulta ou de administrador (RBAC não aplicável) |
| Informações do sistema de consulta como: retornar estatísticas, contagens e listas de objetos. | Chave de administração, RBAC no recurso (Proprietário, Colaborador, Leitor) |
| Gerenciar chaves de administrador | Chave de Administrador, Proprietário ou Colaborador de RBAC no recurso. |
| Gerenciar chaves de consulta |  Chave de Administrador, Proprietário ou Colaborador de RBAC no recurso.  |

## <a name="physical-security"></a>Segurança física

Os data centers da Microsoft fornecem segurança física líder no setor e são compatíveis com um portfólio abrangente de padrões e regulamentos. Para saber mais, acesse a página [Data centers globais](https://www.microsoft.com/cloud-platform/global-datacenters) ou assista a um breve vídeo sobre segurança de data center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Confira também

+ [Introdução ao .NET (demonstra o uso de uma chave de administrador para criar um índice)](search-create-index-dotnet.md)
+ [Introdução ao REST (demonstra o uso de uma chave de administrador para criar um índice)](search-create-index-rest-api.md)
+ [Controle de acesso baseado em identidade usando filtros de pesquisa cognitiva do Azure](search-security-trimming-for-azure-search.md)
+ [Controle de acesso baseado em identidade do Active Directory usando filtros de pesquisa cognitiva do Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)