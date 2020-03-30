---
title: Defina funções rbac para acesso administrativo do Azure
titleSuffix: Azure Cognitive Search
description: Controle administrativo baseado em funções (RBAC) no portal Azure para controle e delegar tarefas administrativas para o gerenciamento da Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112563"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Defina funções de RBAC para acesso administrativo à Pesquisa Cognitiva do Azure

O Azure fornece um [modelo global de autorização baseado em funções](../role-based-access-control/role-assignments-portal.md) para todos os serviços gerenciados por meio do portal ou nas APIs do Gerenciador de Recursos. As funções proprietário, contribuinte e leitor determinam o nível de administração de *serviços* para usuários, grupos e diretores de segurança do Active Directory atribuídos a cada função. 

> [!Note]
> Não há controles de acesso baseados em função para proteger partes de um índice ou um subconjunto de documentos. Para o acesso baseado em identidade sobre os resultados da pesquisa, é possível criar filtros de segurança para cortar resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [Filtros de segurança](search-security-trimming-for-azure-search.md) e [Proteger com Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tarefas de gerenciamento por função

Para a Pesquisa Cognitiva do Azure, as funções estão associadas a níveis de permissão que suportam as seguintes tarefas de gerenciamento:

| Função | Tarefa |
| --- | --- |
| Proprietário |Criar ou excluir o serviço ou qualquer objeto no serviço, incluindo chaves de api, índices, indexadores, fontes de dados do indexador e agendas do indexador.<p>Exibir o status do serviço, incluindo o tamanho de armazenamento e contagens.<p>Adicionar ou excluir a associação de função (somente um Proprietário pode gerenciar a associação de função).<p>Os administradores de assinatura e proprietários de serviço possuem associação automática na função Proprietários. |
| Colaborador |Mesmo nível de acesso como Proprietário, menos gerenciamento de funções RBAC. Por exemplo, um Colaborador pode criar ou excluir objetos ou exibir e regenerar [chaves de API](search-security-api-keys.md), mas não pode modificar associações de função. |
| [Função interna do Colaborador do Serviço de Pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Equivalente à função Colaborador. |
| Leitor |Exibe métricas e conceitos básicos do serviço. Os membros dessa função não podem exibir índice, indexador, fonte de dados ou informações importantes.  |

As funções não concedem direitos de acesso para o ponto de extremidade de serviço. As operações do serviço Search, como gerenciamento de índices, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de api, não funções. Para mais informações, consulte [Gerenciar chaves de API](search-security-api-keys.md).

## <a name="see-also"></a>Confira também

+ [Gerenciar usando o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização na Pesquisa Cognitiva do Azure](search-performance-optimization.md)
+ [Comece com o Controle de Acesso Baseado em Role no portal Azure](../role-based-access-control/overview.md).