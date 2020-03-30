---
title: Guia de referência de operações do Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve tomar para proteger e manter o gerenciamento de identidade e acesso, autenticação, governança e operações
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535308"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Guia de referência de operações do Azure Active Directory

Este guia de referência de operações descreve as verificações e ações que você deve tomar para proteger e manter as seguintes áreas:

- **[Gerenciamento de identidade e acesso](active-directory-ops-guide-iam.md)** - capacidade de gerenciar o ciclo de vida das identidades e seus direitos.
- **[Gerenciamento de autenticação](active-directory-ops-guide-auth.md)** - capacidade de gerenciar credenciais, definir experiência de autenticação, delegar atribuição, medir o uso e definir políticas de acesso com base na postura de segurança corporativa.
- **[Governança](active-directory-ops-guide-govern.md)** - capacidade de avaliar e atestar o acesso concedido a identidades não privilegiadas e privilegiadas, auditar e controlar mudanças no ambiente.
- **[Operações](active-directory-ops-guide-ops.md)** - otimizar as operações Azure Active Directory (Azure AD).

Algumas recomendações aqui podem não ser aplicáveis ao ambiente de todos os clientes, por exemplo, as práticas recomendadas do AD FS podem não se aplicar se sua organização usar sincronia de hash de senha.

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar com o tempo. As organizações devem avaliar continuamente suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo. As recomendações podem mudar quando as organizações assinam uma licença Azure AD Premium diferente. Por exemplo, o Azure AD Premium P2 incluirá mais recomendações de governança.

## <a name="stakeholders"></a>Interessados

Cada seção neste guia de referência recomenda atribuir às partes interessadas para planejar e implementar tarefas-chave com sucesso. A tabela a seguir descreve a lista de todas as partes interessadas neste guia:

| Stakeholder | Descrição |
| :- | :- |
| Equipe de Operações IAM | Esta equipe lida com a gestão das operações diárias do sistema de Gestão de Identidade e Acesso |
| Equipe de Produtividade | Essa equipe possui e gerencia os aplicativos de produtividade, como e-mail, compartilhamento e colaboração de arquivos, mensagens instantâneas e conferências. |
| Proprietário do Aplicativo | Essa equipe possui a aplicação específica de um negócio e geralmente uma perspectiva técnica em uma organização. |
| Equipe de Arquitetura InfoSec | Esta equipe planeja e projeta as práticas de Segurança da Informação de uma organização. |
| Equipe de Operações InfoSec | Esta equipe executa e monitora as práticas implementadas de Segurança da Informação da equipe de Arquitetura InfoSec. |

## <a name="next-steps"></a>Próximas etapas

Comece com as [verificações e ações de gerenciamento de identidade e acesso.](active-directory-ops-guide-iam.md)
