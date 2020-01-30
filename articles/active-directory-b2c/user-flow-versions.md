---
title: Versões no fluxo de usuário no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre as versões de fluxos de usuário disponíveis no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d7e174245755659494dfe1243c39619ae37f0f33
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840070"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versões no fluxo de usuário no Azure Active Directory B2C

O usuário flui em Azure Active Directory B2C (Azure AD B2C) ajuda a configurar políticas comuns que descrevem totalmente [as](user-flow-overview.md) experiências de identidade do cliente. Essas experiências incluem inscrição, entrada, redefinição de senha ou edição de perfil. No Azure Active Directory B2C, você pode selecionar uma coleção de fluxos de usuário recomendada e fluxos de usuário de visualização.

Novos fluxos de usuário são adicionados como novas versões. Como fluxos dos usuários se tornam estáveis, serão recomendados para uso. Os fluxos dos usuários são marcados como **Recomendados** se tiver sido totalmente testadas. Os fluxos dos usuários serão considerados na visualização até que sejam marcados como recomendado. Use um fluxo de usuário recomendado para qualquer aplicativo de produção, mas escolha de outras versões para testar a nova funcionalidade assim que estiverem disponíveis. Você não deve usar as versões mais antigas dos fluxos recomendados ao usuário.

>[!IMPORTANT]
> A menos que um fluxo de usuário seja identificado como **recomendado**, ele será considerado em versão *prévia*. Você deve usar somente os fluxos de usuário recomendados para seus aplicativos de produção.

## <a name="v1"></a>V1

| Fluxo de usuário | Recomendado | Description |
| --------- | ----------- | ----------- |
| Redefinição de senha | Sim | Permite que um usuário escolha uma nova senha após verificar seu email. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>Configurações de compatibilidade de token</li><li>[Requisitos de complexidade de senha](user-flow-password-complexity.md)</li></ul> |
| Edição de perfil | Sim | Permite que o usuário configure seus atributos de usuário. Usando este fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li></ul> |
| Entrar usando ROPC | Não | Permite que um usuário com uma conta local entre diretamente em aplicativos nativos (nenhum navegador necessário). Usando este fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li></ul> |
| Entrar | Não | Permite que um usuário entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>Entrar no bloco</li><li>Forçar redefinição de senha</li><li>Manter-me conectado (KMSI)</ul><br>Não é possível personalizar a interface do usuário com este fluxo de usuário. |
| Inscrever-se | Não | Permite que um usuário crie uma conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de senha](user-flow-password-complexity.md)</li></ul> |
| Inscrever-se e entrar | Sim | Permite que um usuário crie uma conta ou entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de senha](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V 1.1

| Fluxo de usuário | Recomendado | Description |
| --------- | ----------- | ----------- |
| Redefinição de senha v 1.1 | Não | Permite que um usuário escolha uma nova senha depois de verificar seu email (novo layout de página disponível). Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>Configurações de compatibilidade de token</li><li>[Requisitos de complexidade de senha](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Fluxo de usuário | Recomendado | Description |
| --------- | ----------- | ----------- |
| Redefinição de senha v2 | Não | Permite que um usuário escolha uma nova senha após verificar seu email. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>Configurações de compatibilidade de token</li><li>[Restrição de idade](basic-age-gating.md)</li><li>[requisitos de complexidade de senha](user-flow-password-complexity.md)</li></ul> |
| Edição de perfil v2 | Sim | Permite que o usuário configure seus atributos de usuário. Usando este fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li></ul> |
| Entrar v2 | Não | Permite que um usuário entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Restrição de idade](basic-age-gating.md)</li><li>Personalização da página de entrada</li></ul> |
| Inscrever v2 | Não | Permite que um usuário crie uma conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Restrição de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de senha](user-flow-password-complexity.md)</li></ul> |
| Inscrever-se e entrar v2 | Não | Permite que um usuário crie uma conta ou entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](custom-policy-multi-factor-authentication.md)</li><li>[Restrição de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de senha](user-flow-password-complexity.md)</li></ul> |
