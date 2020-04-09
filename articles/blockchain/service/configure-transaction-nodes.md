---
title: Configure os adeptos de transação do Azure Blockchain Service
description: Como configurar os ates de transação do Azure Blockchain Service
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 8fa18496d0c1aa59beb55569e731967d5ebea427
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876922"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configure os adeptos de transação do Azure Blockchain Service

Nós de transações são usados para enviar transações blockchain para o Azure Blockchain Service através de um ponto final público. O nó de transação padrão contém a chave privada da conta Ethereum registrada na blockchain e, como tal, não pode ser excluída.

Para exibir os detalhes do nó de transação padrão:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue para o membro do serviço Azure Blockchain. Selecione **nós de transação**.

    ![Selecionar o nó de transação padrão](./media/configure-transaction-nodes/nodes.png)

    Os detalhes da visão geral incluem endereços de ponto final público e chave pública.

## <a name="create-transaction-node"></a>Criar nó de transação

Você pode adicionar até nove nós de transação adicionais ao seu membro blockchain, para um total de 10 nós de transação. Adicionando nós de transação, você pode aumentar a escalabilidade ou distribuir carga. Por exemplo, você pode ter um ponto final de nó de transação para diferentes aplicativos clientes.

Para adicionar um nó de transação:

1. No portal Azure, navegue até o membro do Azure Blockchain Service e selecione **os nós de transação > Adicionar**.
1. Complete as configurações do novo nó de transação.

    ![Adicionar nó de transação](./media/configure-transaction-nodes/add-node.png)

    | Configuração | Descrição |
    |---------|-------------|
    | Nome | Nome do nó de transação. O nome é usado para criar o endereço DNS para o ponto de extremidade do nó de transação. Por exemplo, `newnode-myblockchainmember.blockchain.azure.com`. O nome do nó não pode ser alterado uma vez que ele é criado. |
    | Senha | Defina uma senha forte. Use a senha para acessar o ponto final do nó de transação com autenticação básica.

1. Selecione **Criar**.

    O provisionamento de um novo nó de transação leva cerca de 10 minutos. Os nós de transação adicionais incorrem em custo. Para obter mais informações sobre os custos, consulte [os preços do Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Pontos de extremidade

Os nós de transação têm um nome DNS exclusivo e pontos finais públicos.

Para visualizar os detalhes do ponto final de um nó de transação:

1. No portal Azure, navegue até um dos ádeos de transações do membro do Azure Blockchain Service e selecione **Visão geral**.

    ![Pontos de extremidade](./media/configure-transaction-nodes/endpoints.png)

Os pontos finais do nó de transação são seguros e requerem autenticação. Você pode se conectar a um ponto final de transação usando autenticação Azure AD, autenticação básica HTTPS e usando uma chave de acesso em HTTPS ou Websocket via TLS.

### <a name="azure-active-directory-access-control"></a>Controle de acesso do Azure Active Directory

Os pontos finais do azure Blockchain Service oferecem suporte à autenticação do Azure Active Directory (Azure AD). Você pode conceder ao usuário, grupo e serviço principal do Azure acesso ao seu ponto final.

Para conceder controle de acesso a Ad do Azure ao seu ponto final:

1. No portal Azure, navegue até o membro do Azure Blockchain Service e selecione **os nós de transação > controle de acesso (IAM) > adicionar > adicionar atribuição de função**.
1. Crie uma nova atribuição de função para um usuário, grupo ou principal de serviço (funções de aplicativo).

    ![Adicionar papel IAM](./media/configure-transaction-nodes/add-role.png)

    | Configuração | Ação |
    |---------|-------------|
    | Função | Selecione **Proprietário,** **Contribuinte**ou **Leitor.**
    | Atribuir acesso a | Selecione **o usuário, grupo ou principal de serviço do Azure AD**.
    | Selecionar | Procure o usuário, grupo ou principal de serviço que você deseja adicionar.

1. Selecione **Salvar** para adicionar a atribuição de função.

Para obter mais informações sobre o controle de acesso a Azure AD, consulte [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal Azure](../../role-based-access-control/role-assignments-portal.md)

Para obter detalhes sobre como se conectar usando a autenticação Azure AD, consulte [conectar-se ao seu nó usando autenticação AAD](configure-aad.md).

### <a name="basic-authentication"></a>Autenticação Básica

Para autenticação básica HTTPS, as credenciais de nome de usuário e senha são passadas no cabeçalho HTTPS da solicitação para o ponto final.

Você pode visualizar os detalhes básicos do ponto final de autenticação de um nó de transação no portal Azure. Navegue até um de seus nós de transação membros do Azure Blockchain Service e selecione **Autenticação Básica** nas configurações.

![Autenticação Básica](./media/configure-transaction-nodes/basic.png)

O nome de usuário é o nome do seu nó e não pode ser alterado.

Para usar a \<URL, substitua a senha\> pelo conjunto de senhas quando o nó foi provisionado. Você pode atualizar a senha selecionando **Redefinir senha**.

### <a name="access-keys"></a>Chaves de acesso

Para autenticação de chave de acesso, a chave de acesso está incluída na URL do ponto final. Quando o nó de transação é provisionado, duas chaves de acesso são geradas. Qualquer tecla de acesso pode ser usada para autenticação. Duas teclas permitem que você mude e gire as teclas.

Você pode visualizar os detalhes da chave de acesso de um nó de transação e copiar endereços de ponto final que incluem as chaves de acesso. Navegue até um dos ádeos de transações do membro do Azure Blockchain Service e selecione **Chaves de acesso** nas configurações.

### <a name="firewall-rules"></a>Regras de firewall

As regras do firewall permitem limitar os endereços IP que podem tentar autenticar ao seu nó de transação.  Se nenhuma regra de firewall estiver configurada para o nó de transação, ela não poderá ser acessada por nenhuma das partes.  

Para visualizar as regras de firewall de um nó de transação, navegue até um dos ádeos de transações do membro do Azure Blockchain Service e selecione **as regras do Firewall** nas configurações.

Você pode adicionar regras de firewall inserindo um nome de regra, iniciando endereço IP e um endereço IP final na grade de regras do **Firewall.**

![Regras de firewall](./media/configure-transaction-nodes/firewall-rules.png)

Para habilitar:

* **Endereço IP único:** Configure o mesmo endereço IP para os endereços IP de partida e término.
* **Intervalo de endereços IP:** Configure o intervalo de endereços IP de partida e final. Por exemplo, um intervalo que começa em 10.221.34.0 e termina em 10.221.34.255 permitiria toda a 10.221.34.xxx sub-rede.
* **Permitir todos os endereços IP:** Configure o endereço IP inicial para 0.0.0.0 e o endereço IP final para 255.255.255.255.

## <a name="connection-strings"></a>Cadeias de conexão

A sintaxe de seqüência de conexão para o nó de transação é fornecida para autenticação básica ou usando chaves de acesso. As seqüências de conexão, incluindo chaves de acesso em HTTPS e WebSockets, são fornecidas.

Você pode visualizar as strings de conexão de um nó de transação e copiar endereços de ponto final. Navegue até um de seus nós de transação membros do Azure Blockchain Service e selecione **strings de conexão** nas configurações.

![Cadeias de conexão](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Código de exemplo

O código de exemplo é fornecido para habilitar rapidamente a conexão ao nó de transação via Web3, Nethereum, Web3js e Truffle.

Você pode visualizar o código de conexão de amostra de um nó de transação e copiá-lo para usar com ferramentas de desenvolvedor populares. Vá para um de seus nós de transação membros do Azure Blockchain Service e selecione **Código de amostra** nas configurações.

Escolha a guia Web3, Nethereum, Truffle ou Web3j para visualizar a amostra de código que deseja usar.

![Código de exemplo](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configure os nós de transação usando o Azure CLI](manage-cli.md)
