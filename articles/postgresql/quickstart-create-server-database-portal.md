---
title: 'Início Rápido: Criar servidor – Portal do Azure – Banco de Dados do Azure para PostgreSQL – Servidor único'
description: Guia de início rápido para criar e gerenciar um Banco de Dados do Azure para PostgreSQL – Servidor único usando a interface do usuário do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 9741e00cdcc4907c547ebfcc6f8e7df7a3b32891
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529653"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Início Rápido: Criar um Banco de Dados do Azure para o servidor PostgreSQL no portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este Guia de início rápido mostra como criar um Banco de Dados do Azure para o servidor PostgreSQL usando o portal do Azure em aproximadamente cinco minutos.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Abra o navegador da Web e acesse o [portal](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Um Banco de Dados do Azure para PostgreSQL é criado com um conjunto configurado de [recursos de computação e armazenamento](./concepts-pricing-tiers.md). O servidor é criado dentro de um [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

Para criar um Banco de Dados do Azure para o servidor PostgreSQL, execute as seguintes etapas:
1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > ![O "Banco de Dados do Azure para PostgreSQL" no menu](./media/quickstart-create-database-portal/1-create-database.png)

3. Selecione a opção de implantação **Servidor único**.

   > [!div class="mx-imgBorder"]
   > ![Selecione o Banco de Dados do Azure para PostgreSQL – Opção de implantação de servidor único](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Preencha o formulário **Básico** com as seguintes informações:

   > [!div class="mx-imgBorder"]
   > ![Criar um servidor](./media/quickstart-create-database-portal/create-basics.png)

   Configuração|Valor sugerido|Descrição
   ---|---|---
   Subscription|O nome da sua assinatura|A assinatura do Azure que você deseja usar para o servidor. Se você tiver várias assinaturas, escolha a assinatura para a qual você recebe a cobrança do recurso.
   Resource group|*myresourcegroup*| Um novo nome do grupo de recursos ou um existente de sua assinatura.
   Nome do servidor |*mydemoserver*|Um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome do servidor fornecido. O servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter, pelo menos, 3 até 63 caracteres.
   Fonte de dados | *Nenhuma* | Selecione *Nenhum* para criar um novo servidor do zero. (Você selecionaria *Backup* se estivesse criando um servidor de um backup de replicação geográfica de um Banco de Dados do Azure para servidor PostgreSQL existente).
   Nome de usuário do administrador |*myadmin*| Sua própria conta de logon para uso ao se conectar ao servidor. O nome de logon do administrador não pode ser **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest** ou **public**. Ele não pode começar com **pg_** .
   Senha |Sua senha| Uma nova senha para a conta do administrador do servidor. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (!, $, #, % etc.).
   Location|A região mais próxima dos usuários| A localização mais próxima dos usuários.
   Versão|A última versão principal| A última versão principal do PostgreSQL, a menos que você tenha requisitos específicos.
   Computação + armazenamento | **Uso Geral**, **Gen 5**, **2 vCores**, **5 GB**, **7 dias**, **Com redundância geográfica** | As configurações de computação, armazenamento e backup para o novo servidor. Selecione **Configurar servidor**. Em seguida, selecione o tipo de preço apropriado; para obter mais informações, confira [Detalhes de preços](https://azure.microsoft.com/pricing/details/postgresql/server/). Para habilitar os backups do servidor em armazenamento com redundância geográfica, selecione **Redundância Geográfica** das **Opções de Redundância de Backup**. Selecione **OK**.

   > [!NOTE]
   > Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho. Observe que servidores criados no tipo de preço Básico não podem ser dimensionados mais tarde para Uso Geral ou Otimizado para Memória. 
   
5. Selecione **Revisar + criar** para revisar suas seleções. Selecione **Criar** para provisionar o servidor. Esta operação pode levar alguns minutos.

6. Na barra de ferramentas, selecione o ícone (sino) **Notificações** para monitorar o processo de implantação. Quando a implantação for concluída, selecione **Ir para recurso** para abrir a página **Visão geral** do servidor.

Um banco de dados vazio, **postgres**, é criado. Você também encontrará o banco de dados **azure_maintenance** que é usado para separar os processos de serviço gerenciado das ações do usuário. Não é possível acessar o banco de dados **azure_maintenance**.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor
Por padrão, o servidor criado não é acessível publicamente, e você precisa conceder permissões ao seu IP. Para permitir acesso ao seu IP, acesse o recurso de servidor no portal do Azure e selecione **Segurança da conexão** no menu do lado esquerdo do recurso de servidor. Se você não tiver certeza de como encontrar seu recurso, confira [Como abrir um recurso](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

> [!div class="mx-imgBorder"]
> ![Segurança da conexão – Regras de firewall](./media/quickstart-create-database-portal/add-current-ip-firewall.png)
  
Agora selecione **Adicionar o endereço IP do cliente atual** e escolha **Salvar**. Você pode adicionar IPs extras ou fornecer um intervalo de IP para se conectar ao seu servidor por meio desses IPs. Para obter mais informações, confira [Como gerenciar regras de firewall](./concepts-firewall-rules.md)
   
> [!NOTE]
> Verifique se a sua rede permite o tráfego de saída pela porta 5432 usada pelo Banco de Dados do Azure para PostgreSQL a fim de evitar problemas de conectividade.  

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Conectar-se ao Banco de Dados do Azure para servidor PostgreSQL usando o psql

Use o [psql](http://postgresguide.com/utilities/psql.html) ou o [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), que são clientes populares do PostgreSQL. Para este guia de início rápido, vamos nos conectar usando o psql no [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) no portal do Azure.

1. Anote o nome do servidor, o nome de logon do administrador do servidor, a senha e a ID da assinatura do servidor recém-criado na seção **Visão geral** do servidor, conforme mostrado na imagem abaixo.

2. Inicie o Azure Cloud Shell no portal selecionando o ícone no lado superior esquerdo, conforme realçado na imagem abaixo.

   > [!NOTE]
   > Se você estiver iniciando o Cloud Shell pela primeira vez, verá um aviso que solicitará a criação de um grupo de recursos na conta de armazenamento. Essa é uma etapa única e será anexada automaticamente para todas as sessões. 

   > [!div class="mx-imgBorder"]
   > ![Abrir o Azure Cloud Shell](media/quickstart-create-database-portal/use-in-cloud-shell.png)

3. Execute este comando no terminal do Azure Cloud Shell. Substitua os valores pelos nomes reais do servidor e de logon do usuário administrador. Use o banco de dados vazio **postgres** com o usuário administrador no formato '<nome-de-usuário-do-administrador>'@<servername>, conforme mostrado abaixo, para o Banco de Dados do Azure para PostgreSQL.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Veja como é a experiência no terminal do Cloud Shell
   
   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell
 
    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. No mesmo terminal do Azure Cloud Shell, crie um banco de dados **convidado**
   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Agora, para alternar as conexões para o banco de dados recém-criado **convidado**

   ```bash
   \c guest
   ```
6. Digite `\q` e selecione a tecla ENTER para sair do psql. 

## <a name="clean-up-resources"></a>Limpar os recursos
Você criou com êxito um Banco de Dados do Azure para servidor PostgreSQL em um grupo de recursos.  Caso não espere precisar desses recursos no futuro, exclua-os eliminando o grupo de recursos ou exclua apenas o servidor PostgreSQL. Para excluir o grupo de recursos, siga estas etapas:

1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção. 
2. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.
3. Na página Visão geral do grupo de recursos, selecione **Excluir grupo de recursos**.
4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

Para excluir o servidor, clique no botão **Excluir** na página **Visão geral** do servidor, conforme mostrado abaixo:
> [!div class="mx-imgBorder"]
> ![Excluir seus recursos](media/quickstart-create-database-portal/12-delete.png)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)
