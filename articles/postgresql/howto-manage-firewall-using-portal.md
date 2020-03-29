---
title: Gerenciar regras de firewall - Portal Azure - Banco de Dados Azure para PostgreSQL - Servidor Único
description: Criar e gerenciar regras de firewall para o Banco de Dados Azure para PostgreSQL - Servidor Único usando o portal Azure
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aeef22bf96221061a444f40e16e33343fafe511c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770298"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Criar e gerenciar regras de firewall para o Banco de Dados Azure para PostgreSQL - Servidor Único usando o portal Azure
As regras de firewall em nível de servidor podem ser usadas para gerenciar o acesso a um banco de dados Do Zure para o PostgreSQL Server a partir de um endereço IP especificado ou de um intervalo de endereços IP.

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gerenciamento de pontos finais e regras de serviços da Rede Virtual usando o portal Azure](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um servidor [Criar um servidor de Banco de Dados do Azure para o PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall de nível de servidor no portal do Azure
1. Na página do servidor PostgreSQL, no título Configurações, clique em **Segurança de Conexão** para abrir a página Segurança de Conexão para o Banco de Dados do Azure para PostgreSQL.

   ![Portal do Azure - clique em Segurança de Conexão](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Clique em **Adicionar Meu IP** na barra de ferramentas. Isso cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como visto pelo sistema do Azure.

   ![Portal do Azure - clique em Adicionar Meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado pelo Portal do Azure é diferente do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para fazer a regra funcionar conforme o esperado.
   Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP. Por exemplo, pesquise "qual é meu IP".

   ![Pesquisa do Bing para Qual é meu IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicionar outros intervalos de endereço. Nas regras de firewall do Banco de Dados do Azure para PostgreSQL, é possível especificar um único endereço IP ou um intervalo de endereços. Se você desejar limitar a regra a um único endereço IP, digite o mesmo endereço no campo IP inicial e IP final. A abertura do firewall permite que administradores, usuários e aplicativos acessem qualquer banco de dados no servidor PostgreSQL para o qual eles têm credenciais válidas.

   ![Portal do Azure - regras de firewall](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Clique em **Salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização das regras de firewall foi bem-sucedida.

   ![Portal do Azure - clique em Salvar](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Conexão pelo Azure
Para permitir que aplicativos do Azure se conectem ao seu servidor do Banco de Dados do Azure para PostgreSQL, as conexões do Azure deverão estar habilitadas. Por exemplo, para hospedar um aplicativo dos Aplicativos Web do Azure, um aplicativo executando em uma VM do Azure ou se conectar a partir de um gateway de gerenciamento de dados do Azure Data Factory. Os recursos não precisam estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall habilitar essas conexões. Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se há permissão para conexões do Azure. Há alguns métodos para habilitar esses tipos de conexões. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que essas conexões são permitidas. Alternativamente, você pode definir a opção **Permitir acesso aos serviços do Azure** para **ON** no portal a partir do painel de **segurança Conexão** e bater **em Salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor do Banco de Dados do Azure para PostgreSQL.

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerenciar regras de firewall existentes no nível de servidor pelo Portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique no botão + **Adicionar Meu IP**. Clique em **Salvar** para salvar as alterações.
* Para adicionar mais endereços IP, digite o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Salvar** para salvar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Salvar** para salvar as alterações.
* Para excluir uma regra existente, clique nas reticências [...] e clique em **Excluir** para remover a regra. Clique em **Salvar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas
- Da mesma forma, você pode criar [e gerenciar o Banco de Dados Azure para regras de firewall PostgreSQL usando o Azure CLI](howto-manage-firewall-using-cli.md).
- Mais acesso seguro ao seu [servidor, criando e gerenciando pontos finais e regras de serviço da Rede Virtual usando o portal Azure](howto-manage-vnet-using-portal.md).
- Para obter ajuda com a conexão com um servidor do Banco de Dados do Azure para PostgreSQL, consulte [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md).
