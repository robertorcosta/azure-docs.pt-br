---
title: 'Início Rápido: Criar servidor – Portal do Azure – Banco de Dados do Azure para PostgreSQL – servidor único'
description: Neste guia de início rápido, você criará e gerenciará um servidor do Banco de Dados do Azure para PostgreSQL usando o portal do Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 2c340f9e7d2c44082adc67eeb816276449602035
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484484"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Início Rápido: Criar um servidor do Banco de Dados do Azure para PostgreSQL usando o portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este guia de início rápido mostra como criar um servidor único do Banco de Dados do Azure para PostgreSQL usando o portal do Azure em aproximadamente cinco minutos.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Abra o navegador da Web e acesse o [portal](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Um Banco de Dados do Azure para PostgreSQL é criado com um conjunto configurado de [recursos de computação e armazenamento](./concepts-pricing-tiers.md). O servidor é criado dentro de um [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

Para criar um servidor do Banco de Dados do Azure para PostgreSQL:

1. Selecione **Criar um recurso** no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para PostgreSQL** .

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Captura de tela que mostra Banco de Dados do Azure para PostgreSQL no menu.":::

3. Selecione a opção de implantação **Servidor único** .

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/select-deployment-option.png" alt-text="Captura de tela que mostra Banco de Dados do Azure para PostgreSQL no menu.":::

4. Preencha o formulário **Informações Básicas** com as informações a seguir.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Captura de tela que mostra Banco de Dados do Azure para PostgreSQL no menu.":::

   Configuração|Valor sugerido|Descrição
   ---|---|---
   Subscription|O nome da sua assinatura|A assinatura do Azure que você deseja usar para o servidor. Se você tiver várias assinaturas, escolha a assinatura para a qual você recebe a cobrança do recurso.
   Resource group|*myresourcegroup*| Um novo nome do grupo de recursos ou um existente de sua assinatura.
   Nome do servidor |*mydemoserver*|Um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome do servidor fornecido por você. O servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele precisa conter de 3 a 63 caracteres.
   Fonte de dados | **Nenhuma** | Selecione **Nenhum** para criar um novo servidor do zero. (Você selecionará **Backup** se estiver criando um servidor com base em um backup geográfico de um servidor existente do Banco de Dados do Azure para PostgreSQL).
   Nome de usuário do administrador |*myadmin*| Sua própria conta de logon para uso ao se conectar ao servidor. O nome de logon do administrador não pode ser **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest** ou **public** . Ele não pode começar com **pg_** .
   Senha |Sua senha| Uma nova senha para a conta do administrador do servidor. Ela precisa conter de 8 a 128 caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (por exemplo, !, $, # e %).
   Location|A região mais próxima dos usuários| A localização mais próxima dos usuários.
   Versão|A última versão principal| A última versão principal do PostgreSQL, a menos que você tenha requisitos específicos.
   Computação + armazenamento | **Uso Geral** , **Gen 5** , **2 vCores** , **5 GB** , **7 dias** , **Com redundância geográfica** | As configurações de computação, armazenamento e backup para o novo servidor. Selecione **Configurar servidor** . Em seguida, escolha o tipo de preço apropriado. Para obter mais informações, confira [Detalhes de preços](https://azure.microsoft.com/pricing/details/postgresql/server/). Para habilitar os backups do servidor em armazenamento com redundância geográfica, selecione **Redundância Geográfica** das **Opções de Redundância de Backup** . Selecione **OK** .

   > [!NOTE]
   > Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho. Observe que os servidores criados no tipo de preço Básico não podem ser escalados posteriormente para Uso Geral ou Otimizado para Memória. 
   
5. Selecione **Revisar + criar** para revisar suas seleções. Selecione **Criar** para provisionar o servidor. Essa operação poderá levar alguns minutos.

6. Na barra de ferramentas, selecione o ícone (sino) **Notificações** para monitorar o processo de implantação. Depois que a implantação for concluída, selecione **Ir para recurso** para abrir a página **Visão geral** do servidor.

Um banco de dados vazio, **postgres** , é criado. Você também encontrará um banco de dados **azure_maintenance** que é usado para separar os processos de serviço gerenciado das ações do usuário. Não é possível acessar o banco de dados **azure_maintenance** .

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor
Por padrão, o servidor que você cria não é publicamente acessível. Você precisa conceder permissões ao seu endereço IP. Acesse o recurso de servidor no portal do Azure e selecione **Segurança da conexão** no menu do lado esquerdo do recurso de servidor. Caso não tenha certeza de como localizar seu recurso, confira [Abrir recursos](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Captura de tela que mostra Banco de Dados do Azure para PostgreSQL no menu.":::
  
Selecione **Adicionar o endereço IP do cliente atual** e escolha **Salvar** . Adicione mais endereços IP ou forneça um intervalo de IP para se conectar ao seu servidor nesses endereços IP. Para obter mais informações, confira [Regras de firewall do Banco de Dados do Azure para PostgreSQL](./concepts-firewall-rules.md).
   
> [!NOTE]
> Para evitar problemas de conectividade, verifique se a sua rede permite o tráfego de saída na porta 5432. O Banco de Dados do Azure para PostgreSQL usa essa porta.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Conectar-se ao servidor do Banco de Dados do Azure para PostgreSQL usando o psql

Use o [psql](http://postgresguide.com/utilities/psql.html) ou o [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), que são clientes populares do PostgreSQL. Neste guia de início rápido, vamos nos conectar usando o psql no [Azure Cloud Shell](../cloud-shell/overview.md) dentro do portal do Azure.

1. Anote o nome do servidor, o nome de logon do administrador do servidor, a senha e a ID da assinatura do servidor recém-criado na seção **Visão geral** do servidor.

2. Abra o Azure Cloud Shell no portal selecionando o ícone no lado superior esquerdo.

   > [!NOTE]
   > Se você estiver abrindo o Cloud Shell pela primeira vez, verá um aviso que solicitará a criação de um grupo de recursos e uma conta de armazenamento. Essa é uma etapa única e será anexada automaticamente para todas as sessões. 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Captura de tela que mostra Banco de Dados do Azure para PostgreSQL no menu.":::

3. Execute o comando a seguir no terminal do Azure Cloud Shell. Substitua os valores pelos nomes reais do servidor e de logon do usuário administrador. Use o banco de dados vazio **postgres** com o usuário administrador neste formato: `<admin-username>@<servername>`.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Veja como é a experiência no terminal do Cloud Shell:
   
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
4. No mesmo terminal do Azure Cloud Shell, crie um banco de dados chamado **convidado** .

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Alterne as conexões para o banco de dados **convidado** recém-criado.

   ```bash
   \c guest
   ```
6. Digite `\q` e selecione a tecla ENTER para fechar o psql. 

## <a name="clean-up-resources"></a>Limpar os recursos
Você criou com êxito um servidor do Banco de Dados do Azure para PostgreSQL em um grupo de recursos. Se você não espera precisar desses recursos no futuro, exclua-os eliminando o grupo de recursos ou o servidor PostgreSQL. 

Para excluir o grupo de recursos:

1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção. 
2. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.
3. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos** .
4. Na caixa de diálogo de confirmação, insira o nome do seu grupo de recursos e selecione **Excluir** .

Para excluir o servidor, selecione o botão **Excluir** na página **Visão geral** do servidor:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Captura de tela que mostra Banco de Dados do Azure para PostgreSQL no menu.":::

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migrar seu banco de dados usando a exportação e a importação](./howto-migrate-using-export-and-import.md)