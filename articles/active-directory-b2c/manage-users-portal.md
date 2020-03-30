---
title: Criar & excluir contas de usuários do Azure AD B2C no portal Azure
description: Saiba como usar o portal Azure para criar e excluir usuários de consumidores em seu diretório Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187212"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Use o portal Azure para criar e excluir usuários de consumidores no Azure AD B2C

Pode haver cenários em que você deseja criar manualmente contas de consumidores em seu diretório ativo Do Zure B2C (Azure B2C). Embora as contas de consumo em um diretório Azure AD B2C sejam mais comumente criadas quando os usuários se inscrevem para usar um de seus aplicativos, você pode criá-los de forma programática e usando o portal Azure. Este artigo se concentra no método do portal Azure de criação e exclusão do usuário.

Para adicionar ou excluir usuários, sua conta deve ser atribuída ao administrador do *Usuário* ou à função *de administrador Global.*

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Tipos de conta de usuário

Como descrito em [Visão Geral das contas de usuário no Azure AD B2C,](user-overview.md)existem três tipos de contas de usuário que podem ser criadas em um diretório Azure AD B2C:

* Work
* Convidado
* Consumidor

Este artigo se concentra em trabalhar com **contas de consumidores** no portal Azure. Para obter informações sobre como criar e excluir contas de Trabalho e Convidado, consulte [Adicionar ou excluir usuários usando o Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Criar um usuário consumidor

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Selecione **Novo usuário**.
1. Selecione **Criar usuário AD B2C do Azure**.
1. Escolha um **método de login** e digite um endereço de **e-mail** ou um **nome de usuário** para o novo usuário. O método de login selecionado aqui deve corresponder à configuração especificada para o provedor local de identidade de *conta do* seu inquilino Azure AD B2C (consulte **Gerenciar** > **provedores de identidade** no seu inquilino Azure AD B2C).
1. Digite um **nome** para o usuário. Este é tipicamente o nome completo (dado e sobrenome) do usuário.
1. (Opcional) Você pode **bloquear o login** se desejar atrasar a capacidade do usuário de fazer login. Você pode ativar o login posteriormente editando o **perfil** do usuário no portal Azure.
1. Escolha a senha de geração automática ou **deixe-me** **criar senha**.
1. Especifique o **nome e** sobrenome do usuário **.**
1. Selecione **Criar**.

A menos que você tenha selecionado **o login do Bloco,** o usuário agora pode entrar usando o método de login (e-mail ou nome de usuário) que você especificou.

## <a name="delete-a-consumer-user"></a>Exclua um usuário consumidor

1. No diretório Azure AD B2C, selecione **Usuários**e selecione o usuário que deseja excluir.
1. Selecione **Excluir**e, em seguida, **Sim** para confirmar a exclusão.

Para obter detalhes sobre como restaurar um usuário nos primeiros 30 dias após a exclusão, ou para excluir permanentemente um usuário, consulte [Restaurar ou remover um usuário recentemente excluído usando o Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Próximas etapas

Para cenários automatizados de gerenciamento de usuários, por exemplo, migrando usuários de outro provedor de identidade para o diretório Azure AD B2C, consulte [Azure AD B2C: Migração do usuário](user-migration.md).
