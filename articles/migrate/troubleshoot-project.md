---
title: Solucionar problemas nos projetos de Migrações para Azure
description: Ajuda você a solucionar problemas com a criação e gerenciamento de projetos do Azure Migrate.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535393"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Solucionar problemas nos projetos de Migrações para Azure

Este artigo ajuda você a solucionar problemas ao criar e gerenciar projetos [do Azure Migrate.](migrate-services-overview.md)

## <a name="how-to-add-new-project"></a>Como adicionar novo projeto?

Você pode ter vários projetos do Azure Migrate em uma assinatura. [Aprenda a](how-to-add-tool-first-time.md) criar um projeto pela primeira vez ou adicione projetos [adicionais.](create-manage-projects.md#create-additional-projects)

## <a name="what-azure-permissions-are-needed"></a>Quais permissões do Azure são necessárias?

Você precisa de permissões de Contribuinte ou Proprietário na assinatura para criar um projeto Do Zure Migrate.

## <a name="cant-find-a-project"></a>Não consigo encontrar um projeto

Encontrar um projeto existente do Azure Migrate depende se você está usando a versão atual ou antiga do Azure Migrate. [Siga](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Não consigo encontrar uma geografia

Você pode criar um projeto Azure Migrate em geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

## <a name="what-are-vm-limits"></a>Quais são os limites da VM?

Você pode avaliar até 35.000 VMs VMware ou até 35.000 Hyper-VMs em um único projeto. Um projeto pode incluir vms VMware e Hyper-VMs, até os limites de avaliação.

## <a name="can-i-upgrade-old-project"></a>Posso atualizar o projeto antigo?

Os projetos da versão anterior do Azure Migrate não podem ser atualizados. Você precisa [criar um novo projeto](how-to-add-tool-first-time.md)e adicionar ferramentas a ele.

## <a name="cant-create-a-project"></a>Não é possível criar um projeto

Se você tentar criar um projeto e encontrar um erro de implantação:

- Tente criar o projeto novamente no caso de ser um erro transitório. Em **Implantações,** clique em **Reimplantar** para tentar novamente.
- Verifique se você tem permissões de Contribuinte ou Proprietário na assinatura.
- Se você está implantando em uma geografia recém-adicionada, espere um pouco e tente novamente.
- Se você receber o erro, "As solicitações devem conter cabeçalhos de identidade do usuário", isso pode indicar que você não tem acesso ao inquilino do Azure Active Directory (Azure AD) da organização. Nesse caso:
    - Quando você é adicionado a um inquilino azure AD pela primeira vez, você recebe um convite por e-mail para se juntar ao inquilino.
    - Aceite o convite para ser adicionado ao inquilino.
    - Se você não puder ver o e-mail, entre em contato com um usuário com acesso ao inquilino e peça que ele [reenvie o convite](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) para você.
    - Após receber o e-mail do convite, abra-o e selecione o link para aceitar o convite. Em seguida, saia do portal Azure e faça login novamente. (atualizar o navegador não funcionará.) Em seguida, você pode começar a criar o projeto de migração.

## <a name="how-do-i-delete-a-project"></a>Como excluir um projeto

[Siga estas instruções](create-manage-projects.md#delete-a-project) para excluir um projeto. Observe que quando você exclui um projeto, tanto o projeto quanto os metadados sobre máquinas descobertas no projeto são excluídos.

## <a name="added-tools-dont-show"></a>Ferramentas adicionadas não mostram

Certifique-se de ter o projeto certo selecionado. No hub Azure Migrate > **Servers** ou em **Bancos de Dados,** clique em **Alterar** ao lado do **projeto Migrar (Alterar)** no canto superior direito da tela. Escolha a assinatura correta e o nome do projeto > **OK**. A página deve ser atualizada com as ferramentas adicionadas do projeto selecionado.

## <a name="next-steps"></a>Próximas etapas

Adicione ferramentas [de avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) aos projetos do Azure Migrate.