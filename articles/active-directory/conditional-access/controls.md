---
title: Controles personalizados no Acesso Condicional do Azure AD
description: Saiba como funcionam os controles personalizados no Acesso Condicional do Diretório Ativo do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050545"
---
# <a name="custom-controls-preview"></a>Controles personalizados (versão prévia)

Controles personalizados é uma capacidade de visualização do Diretório Ativo do Azure. Ao usar controles personalizados, seus usuários são redirecionados para um serviço compatível para satisfazer os requisitos de autenticação fora do Azure Active Directory. Para satisfazer esse controle, o navegador do usuário é redirecionado para o serviço externo, executa qualquer autenticação necessária e, em seguida, é redirecionado de volta para o Azure Active Directory. O Azure Active Directory verifica a resposta e, se o usuário foi autenticado ou validado com sucesso, o usuário continua no fluxo de Acesso Condicional.

> [!NOTE]
> Para obter mais informações sobre as mudanças que estamos planejando para o recurso controle personalizado, consulte a [nova atualização de](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)fevereiro de 2020 .

## <a name="creating-custom-controls"></a>Criando controles personalizados

O Custom Controls funciona com um conjunto limitado de provedores de autenticação aprovados. Para criar um controle personalizado, você deve entrar em contato com o provedor que você deseja utilizar. Cada provedor não-Microsoft tem seu próprio processo e requisitos para se inscrever, assinar ou se tornar parte do serviço e indicar que deseja se integrar ao Conditional Access. Nesse ponto, o provedor lhe fornecerá um bloco de dados no formato JSON. Esses dados permitem que o provedor e o Acesso Condicional trabalhem juntos para o seu inquilino, cria o novo controle e define como o Acesso Condicional pode dizer se seus usuários realizaram a verificação com sucesso com o provedor.

Copie os dados JSON e, em seguida, cole-os na caixa de texto relacionada. Não faça nenhuma alteração no JSON a menos que você entenda explicitamente a mudança que está fazendo. Fazer qualquer alteração pode interromper a conexão entre o provedor e a Microsoft e potencialmente bloquear você e seus usuários do acesso às respectivas contas.

A opção de criar um controle personalizado está na seção **Gerenciar** a página **Acesso Condicional.**

![Control](./media/controls/82.png)

Clicar em **Novo controle personalizado** abrirá uma folha com uma caixa de texto para os dados JSON do seu controle.  

![Control](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Excluindo controles personalizados

Para excluir um controle personalizado, primeiro você deve garantir que ele não está sendo usado em nenhuma política de acesso condicional. Depois de concluído:

1. Vá para a lista de Controles personalizados
1. Clique em...  
1. Selecione **Excluir**.

## <a name="editing-custom-controls"></a>Editando controles personalizados

Para editar um controle personalizado, você deve excluir o controle atual e criar um novo controle com as informações atualizadas.

## <a name="known-limitations"></a>Limitações conhecidas

Os controles personalizados não podem ser usados com a automação do Identity Protection que exige autenticação multifatorial do Azure, redefinição de senha de autoatendimento do Azure AD (SSPR), satisfazer requisitos de solicitação de autenticação de vários fatores ou elevar funções no Privileged Gerente de Identidade (PIM).

## <a name="next-steps"></a>Próximas etapas

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

- [Modo somente relatório](concept-conditional-access-report-only.md)

- [Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)
