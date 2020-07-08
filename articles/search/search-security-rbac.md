---
title: Definir funções RBAC para acesso administrativo do Azure
titleSuffix: Azure Cognitive Search
description: RBAC (controle administrativo baseado em função) no portal do Azure para controlar e delegar tarefas administrativas para o gerenciamento de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 402fae5622219b14cfdab921ebe1a78ad5dd111e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84462831"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Definir funções RBAC para acesso administrativo ao Azure Pesquisa Cognitiva

O Azure fornece um [modelo global de autorização baseado em funções](../role-based-access-control/role-assignments-portal.md) para todos os serviços gerenciados por meio do portal ou nas APIs do Gerenciador de Recursos. As funções proprietário, colaborador e leitor determinam o nível de *Administração de serviço* para Active Directory usuários, grupos e entidades de segurança atribuídos a cada função. 

> [!Note]
> Não há controles de acesso baseados em função para proteger partes de um índice ou um subconjunto de documentos. Para o acesso baseado em identidade sobre os resultados da pesquisa, é possível criar filtros de segurança para cortar resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [Filtros de segurança](search-security-trimming-for-azure-search.md) e [Proteger com Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tarefas de gerenciamento por função

Para Pesquisa Cognitiva do Azure, as funções são associadas a níveis de permissão que dão suporte às seguintes tarefas de gerenciamento:

| Função | Tarefa |
| --- | --- |
| Proprietário |Criar ou excluir o serviço ou qualquer objeto no serviço, incluindo chaves de api, índices, indexadores, fontes de dados do indexador e agendas do indexador.<p>Exibir o status do serviço, incluindo o tamanho de armazenamento e contagens.<p>Adicionar ou excluir a associação de função (somente um Proprietário pode gerenciar a associação de função).<p>Os administradores de assinatura e proprietários de serviço possuem associação automática na função Proprietários. |
| Colaborador |Mesmo nível de acesso como Proprietário, menos gerenciamento de funções RBAC. Por exemplo, um Colaborador pode criar ou excluir objetos ou exibir e regenerar [chaves de API](search-security-api-keys.md), mas não pode modificar associações de função. |
| [Função interna do Colaborador do Serviço de Pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Equivalente à função Colaborador. |
| Leitor |Exibe métricas e conceitos básicos do serviço. Os membros dessa função não podem exibir índice, indexador, fonte de dados ou informações importantes.  |

As funções não concedem direitos de acesso para o ponto de extremidade de serviço. As operações do serviço Search, como gerenciamento de índices, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de api, não funções. Para mais informações, consulte [Gerenciar chaves de API](search-security-api-keys.md).

## <a name="permissions-table"></a>Tabela de permissões

A tabela a seguir resume as operações permitidas no Azure Pesquisa Cognitiva e qual chave desbloqueia o acesso a uma determinada operação.

| Operação | Permissões |
|-----------|-------------------------|
| Criar um serviço | Titular da assinatura do Azure |
| Dimensionar um serviço | Chave de administração, proprietário do RBAC ou colaborador no recurso  |
| Excluir um serviço | Chave de administração, proprietário do RBAC ou colaborador no recurso |
| Criar, modificar e excluir objetos no serviço: <br>Índices e partes de componentes (incluindo definições do analisador, perfis de pontuação, opções de CORS), indexadores, fontes de dados, sinônimos, sugestores | Chave de administração, proprietário do RBAC ou colaborador no recurso |
| Consultar um índice | Chave de consulta ou de administrador (RBAC não aplicável) |
| Consultar informações do sistema, como retornar estatísticas, contagens e listas de objetos | Chave de administração, RBAC no recurso (Proprietário, Colaborador, Leitor) |
| Gerenciar chaves de administrador | Chave de administrador, Proprietário ou Colaborador RBAC no recurso |
| Gerenciar chaves de consulta |  Chave de administrador, Proprietário ou Colaborador RBAC no recurso  |

## <a name="see-also"></a>Consulte também

+ [Gerenciar usando o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização no Azure Pesquisa Cognitiva](search-performance-optimization.md)
+ [Introdução ao controle de acesso baseado em função no portal do Azure](../role-based-access-control/overview.md).