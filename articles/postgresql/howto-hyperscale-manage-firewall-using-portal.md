---
title: Gerenciar regras de firewall - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Crie e gerencie regras de firewall para o Banco de Dados Azure para PostgreSQL - Hyperscale (Citus) usando o portal Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977532"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Gerenciar regras de firewall para banco de dados Azure para PostgreSQL - Hyperscale (Citus)
As regras de firewall em nível de servidor podem ser usadas para gerenciar o acesso a um nó de coordenador de Hyperscale (Citus) a partir de um endereço IP especificado ou alcance de endereços IP.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um grupo de servidores [Crie um banco de dados Azure para o grupo de servidores PostgreSQL – Hyperscale (Citus).](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall de nível de servidor no portal do Azure

> [!NOTE]
> Essas configurações também são acessíveis durante a criação de um grupo de servidores Azure Database para Servidor PostgreSQL - Hyperscale (Citus). Na guia Rede, clique em 'Ponto final ' - **'''''''''''''''''** **Networking**
> ![Portal Azure - guia de rede](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Na página do grupo de servidor PostgreSQL, o título Segurança, clique **em Rede** para abrir as regras do Firewall.

   ![Portal Azure - clique em Rede](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Clique **em Adicionar IP cliente,** seja na barra de ferramentas (opção A abaixo) ou no link (opção B). De qualquer forma, cria automaticamente uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema Azure.

   ![Portal Azure - clique em Adicionar IP cliente](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Alternativamente, clicar **em +Adicionar 0.0.0.0 - 255.255.255.255** (à direita da opção B) permite não apenas o seu IP, mas toda a internet para acessar a porta 5432 do nó coordenador. Nesta situação, os clientes ainda devem fazer login com o nome de usuário e senha corretos para usar o cluster. No entanto, recomendamos permitir o acesso mundial por apenas curtos períodos de tempo e apenas para bancos de dados não-produzidos.

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado pelo Portal do Azure é diferente do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para fazer a regra funcionar conforme o esperado.
   Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP. Por exemplo, pesquise "qual é meu IP".

   ![Pesquisa do Bing para Qual é meu IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicionar outros intervalos de endereço. Nas regras de firewall, você pode especificar um único endereço IP ou uma série de endereços. Se você desejar limitar a regra a um único endereço IP, digite o mesmo endereço no campo IP inicial e IP final. A abertura do firewall permite que administradores, usuários e aplicativos acessem o nó coordenador na porta 5432.

5. Clique em **Salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização das regras de firewall foi bem-sucedida.

## <a name="connecting-from-azure"></a>Conexão pelo Azure

Há uma maneira fácil de conceder acesso de banco de dados Hyperscale a aplicativos hospedados no Azure (como um aplicativo Azure Web Apps ou aqueles que estão sendo executados em uma VM Do Zure). Basta definir os **serviços e recursos do Azure para acessar essa** opção de grupo de servidor para **Sim** no portal do painel **Rede** e clique **em Salvar**.

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerenciar regras de firewall existentes no nível de servidor pelo Portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique no botão para + **Adicionar IP do cliente**. Clique em **Salvar** para salvar as alterações.
* Para adicionar mais endereços IP, digite o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Salvar** para salvar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Salvar** para salvar as alterações.
* Para excluir uma regra existente, clique nas reticências [...] e clique em **Excluir** para remover a regra. Clique em **Salvar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [o conceito de regras de firewall,](concepts-hyperscale-firewall-rules.md)incluindo como solucionar problemas de conexão.
