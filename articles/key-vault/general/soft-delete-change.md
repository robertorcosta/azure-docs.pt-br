---
title: Habilitar a exclusão reversível em todos os cofres de chave do Azure | Microsoft Docs
description: Use este documento para adotar a exclusão reversível em todos os cofres de chaves.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: e512cccdbfdc56500fa7c69372ca38f59d3195c2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590079"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>A exclusão reversível será habilitada em todos os cofres de chaves

> [!WARNING]
> **Alteração da Falha**: A capacidade de recusar a exclusão reversível será preterida em breve. Os usuários e administradores do Azure Key Vault devem habilitar a exclusão reversível nos seus cofres de chaves imediatamente.
>
> Para o HSM Gerenciado, a exclusão reversível é habilitada por padrão e não pode ser desabilitada.

Quando um segredo for excluído de um cofre de chaves sem proteção de exclusão reversível, o segredo será excluído permanentemente. Atualmente, os usuários podem recusar a exclusão reversível durante a criação do cofre de chaves, mas, para proteger os seus segredos de exclusão acidental ou mal-intencionada por um usuário, a Microsoft em breve habilitará a proteção de exclusão reversível em **todos** os cofres de chaves e os usuários não terão mais a opção de recusar ou desativar a exclusão reversível.

:::image type="content" source="../media/softdeletediagram.png" alt-text="<texto Alt>":::

Para obter detalhes completos sobre a funcionalidade de exclusão reversível, confira [Visão geral da exclusão reversível do Azure Key Vault](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Meu aplicativo poderá funcionar com a exclusão reversível habilitada?

> [!Important] 
> **Examine atentamente as informações a seguir antes de ativar a exclusão reversível em seus cofres de chaves**

Os nomes dos cofres de chave são globalmente exclusivos. Os nomes de segredos armazenados em um cofre de chaves também são exclusivos. Você não poderá reutilizar o nome de um cofre de chaves ou um objeto do cofre de chaves que exista no estado de exclusão reversível. 

**Exemplo #1** Se seu aplicativo cria programaticamente um cofre de chaves chamado “Cofre A” e, posteriormente, exclui o “Cofre A”. O cofre de chaves será movido para o estado de exclusão reversível. Seu aplicativo não poderá recriar outro cofre de chaves chamado “Cofre A” até que o cofre de chaves seja limpo do estado de exclusão reversível. 

**Exemplo #2** Se o aplicativo criar uma chave chamada `test key` no cofre de chaves A e posteriormente excluir a chave do cofre A, ele não poderá criar uma nova chave chamada `test key` no cofre de chaves A até que o objeto `test key` seja limpo do estado de exclusão reversível. 

Isso poderá resultar em erros de conflito se você tentar excluir um objeto do cofre de chaves e recriá-lo com o mesmo nome sem limpá-lo primeiro do estado de exclusão reversível. Isso pode fazer com que os seus aplicativos ou automação falhem. Consulte sua equipe de desenvolvimento antes de fazer as alterações necessárias no aplicativo e na administração abaixo. 

### <a name="application-changes"></a>Mudanças do aplicativo

Se o seu aplicativo pressupor que a exclusão reversível não está habilitada e espera que os nomes do segredo ou do cofre de chaves excluídos estejam disponíveis para reutilização imediata, a lógica do aplicativo precisará fazer as alterações a seguir para adotar essa alteração.

1. Excluir o cofre de chaves ou o segredo original
2. Limpe o cofre de chaves ou o segredo no estado de exclusão reversível.
3. Aguarde; a recriação imediata pode resultar em um conflito.
4. Crie novamente o cofre de chaves com o mesmo nome.
5. Implemente uma nova tentativa se a operação de criação ainda resultar em um erro de conflito de nomes, pode levar até 10 minutos para que os registros DNS sejam atualizados no pior cenário.

### <a name="administration-changes"></a>Mudanças na administração

As entidades de segurança que precisam de acesso para excluir permanentemente os segredos precisam receber permissões de política de acesso adicionais para limpar esses segredos e o cofre de chaves.

Se você tiver uma Azure Policy nos cofres de chaves que exige que a exclusão reversível seja desativada, essa política precisará ser desabilitada.  Talvez seja necessário escalonar esse problema para um administrador que controla as políticas do Azure aplicadas ao seu ambiente. Se essa política não estiver desabilitada, você poderá perder a capacidade de criar cofres de chaves no escopo da política aplicada.

Se a sua organização estiver sujeita a requisitos de conformidade legal e não puder permitir que os cofres de chaves e os segredos excluídos permaneçam em um estado recuperável por um longo período, você precisará ajustar o período de retenção da exclusão reversível, que pode ser configurado entre 7 e 90 dias de acordo com os padrões da sua organização.

## <a name="procedures"></a>Procedimentos

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Auditar os seus cofres de chaves para verificar se a exclusão reversível está habilitada

1. Faça logon no Portal do Azure.
2. Pesquise "Azure Policy".
3. Selecione "Definições".
4. Em Categoria, selecione "Key Vault" no filtro.
5. Selecione a política "O cofre de chaves deve estar com a exclusão reversível habilitada".
6. Clique em "Atribuir".
7. Defina o escopo da sua assinatura.
8. Defina o efeito da política como "Auditoria".
9. Selecione "Examinar + Criar".
10. Pode levar até 24 horas para que uma verificação completa do seu ambiente seja concluída.
11. Na Folha Azure Policy, clique em "Conformidade".
12. Selecione a política que você aplicou.

Agora, você conseguirá filtrar e ver quais cofres de chaves têm a exclusão reversível habilitada (recursos em conformidade) e quais deles não têm a exclusão reversível habilitada (recursos sem conformidade).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Ativar a Exclusão Reversível para um cofre de chaves existente

1. Faça logon no Portal do Azure.
2. Pesquise o seu Key Vault.
3. Selecione "Propriedades" em configurações.
4. Em Exclusão Reversível, selecione o botão de opção correspondente a "Habilitar a recuperação deste cofre e dos respectivos objetos".
5. Defina o período de retenção para a exclusão reversível.
6. Selecione "Salvar".

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Conceder permissões de política de acesso de limpeza a uma entidade de segurança

1. Faça logon no Portal do Azure.
2. Pesquise o seu Key Vault.
3. Selecione "Políticas de Acesso" em configurações.
4. Selecione a entidade de serviço à qual você deseja permitir acesso.
5. Para cada lista suspensa em permissões de chave, de segredo e de certificado, role para baixo até "Operações Privilegiadas" e selecione a permissão "Limpar".

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-this-change-affect-me"></a>Esta alteração me afeta?

Se você já tiver a exclusão reversível ativada ou se você não excluir e recriar os objetos do cofre de chaves com o mesmo nome, provavelmente não observará nenhuma alteração no comportamento do cofre de chaves.

Se você tiver um aplicativo que exclui e recria com frequência objetos do cofre de chaves com as mesmas convenções de nomenclatura, precisará fazer alterações na lógica do aplicativo para manter o comportamento esperado. Confira a seção "Como fazer para responder às alterações da falha?" acima.

### <a name="how-do-i-benefit-from-this-change"></a>Como fazer para me beneficiar desta mudança?

A proteção de exclusão reversível oferece à sua organização uma camada adicional de proteção contra exclusão acidental ou mal-intencionada. Como administrador do cofre de chaves, você pode restringir o acesso às permissões de recuperação e de limpeza.

Se um usuário excluir acidentalmente um cofre de chaves ou um segredo, você poderá conceder a eles permissões de acesso para recuperar o segredo em si sem criar um risco de excluir permanentemente o segredo ou o cofre de chaves. Esse processo de autoatendimento minimizará o tempo de inatividade no seu ambiente e garantirá a disponibilidade dos seus segredos.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Como fazer para descobrir se preciso executar ações?

Siga as etapas acima na seção intitulada "Procedimento para auditar os seus cofres de chaves para verificar se a exclusão reversível está ativada". Qualquer cofre de chaves que não tenha a exclusão reversível ativada será afetado por essa alteração. Ferramentas adicionais para ajudar a auditoria estarão disponíveis em breve e este documento será atualizado.

### <a name="what-action-do-i-need-to-take"></a>Quais ações preciso realizar?

Verifique se você não precisa fazer alterações na lógica do aplicativo. Depois de confirmar isso, ative a exclusão reversível em todos os cofres de chaves.

### <a name="by-when-do-i-need-to-take-action"></a>Quando preciso executar ações?

Para garantir que os seus aplicativos não sejam afetados, ative a exclusão reversível nos seus cofres de chaves assim que possível.

## <a name="next-steps"></a>Próximas etapas

- Entre em contato conosco com perguntas sobre essa alteração em [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com).
- Leia a [Visão geral da exclusão reversível](soft-delete-overview.md)
