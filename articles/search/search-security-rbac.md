---
title: Definir funções RBAC para acesso administrativo do Azure
titleSuffix: Azure Cognitive Search
description: RBAC (controle administrativo baseado em função) no portal do Azure para controlar e delegar tarefas administrativas para o gerenciamento de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112563"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Definir funções RBAC para acesso administrativo ao Azure Pesquisa Cognitiva

O Azure fornece um [modelo global de autorização baseado em funções](../role-based-access-control/role-assignments-portal.md) para todos os serviços gerenciados por meio do portal ou nas APIs do Gerenciador de Recursos. As funções de Leitor, Colaborador e Proprietário determinam o nível de *administração de serviço* para usuários, grupos e entidades de segurança do Active Directory para cada função. 

> [!Note]
> Não há controles de acesso baseados em função para proteger partes de um índice ou um subconjunto de documentos. Para o acesso baseado em identidade sobre os resultados da pesquisa, é possível criar filtros de segurança para cortar resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [Filtros de segurança](search-security-trimming-for-azure-search.md) e [Proteger com Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tarefas de gerenciamento por função

Para Pesquisa Cognitiva do Azure, as funções são associadas a níveis de permissão que dão suporte às seguintes tarefas de gerenciamento:

| Função | tarefa |
| --- | --- |
| Proprietário |Criar ou excluir o serviço ou qualquer objeto no serviço, incluindo chaves de api, índices, indexadores, fontes de dados do indexador e agendas do indexador.<p>Exibir o status do serviço, incluindo o tamanho de armazenamento e contagens.<p>Adicionar ou excluir a associação de função (somente um Proprietário pode gerenciar a associação de função).<p>Os administradores de assinatura e proprietários de serviço possuem associação automática na função Proprietários. |
| Colaborador |Mesmo nível de acesso como Proprietário, menos gerenciamento de funções RBAC. Por exemplo, um Colaborador pode criar ou excluir objetos ou exibir e regenerar [chaves de API](search-security-api-keys.md), mas não pode modificar associações de função. |
| [Função interna do Colaborador do Serviço de Pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Equivalente à função Colaborador. |
| leitor |Exibe métricas e conceitos básicos do serviço. Os membros dessa função não podem exibir índice, indexador, fonte de dados ou informações importantes.  |

As funções não concedem direitos de acesso para o ponto de extremidade de serviço. As operações do serviço Search, como gerenciamento de índices, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de api, não funções. Para mais informações, consulte [Gerenciar chaves de API](search-security-api-keys.md).

## <a name="see-also"></a>Consulte também

+ [Gerenciar usando o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização no Azure Pesquisa Cognitiva](search-performance-optimization.md)
+ [Introdução ao Controle de Acesso Baseado em Função no Portal do Azure](../role-based-access-control/overview.md).