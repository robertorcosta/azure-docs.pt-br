---
title: 'Início Rápido: Projetar um servidor – Portal do Azure – Banco de Dados do Azure para MySQL'
description: Este artigo o orienta a usar o portal do Azure para criar rapidamente um servidor de Banco de Dados do Azure para MySQL de exemplo em aproximadamente cinco minutos.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: cdddd9a90911499421351adf0f41ef90f0e2f9a5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906561"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Início Rápido: Criar um servidor do Banco de Dados do Azure para MySQL no portal do Azure

O Banco de Dados do Azure para MySQL é um serviço gerenciado usado para executar, gerenciar e dimensionar Bancos de Dados MySQL altamente disponíveis na nuvem. Este Guia de Início Rápido mostra como criar um Banco de Dados do Azure para servidor MySQL em aproximadamente cinco minutos usando o portal do Azure.  

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Abra seu navegador da Web e vá para o [portal do Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL
Crie um Banco de Dados do Azure para o servidor MySQL com um conjunto definido de recursos de [computação e armazenamento](./concepts-compute-unit-and-storage.md). Crie o serviço dentro de um [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

Siga estas etapas para criar um Banco de Dados do Azure para o servidor MySQL:

1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para MySQL**. Você também pode inserir **MySQL** na caixa de pesquisa para localizar o serviço.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Opção do Banco de Dados do Azure para MySQL":::

3. Preencha o formulário de detalhes sobre o novo servidor com as seguintes informações:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Criar formulário de servidor":::

**Configuração** | **Valor sugerido** | **Descrição do campo** 
---|---|---
Subscription | Sua assinatura | Selecione a assinatura do Azure que você deseja usar para o servidor. Se você tem várias assinaturas, escolha a assinatura na qual recebe a cobrança do recurso.
Resource group | *myresourcegroup* | Forneça um novo nome de um grupo de recursos ou um existente. O grupo de recursos pode ser usado para organizar suas dependências que pertencem a um projeto.
Nome do servidor | Nome de servidor exclusivo | Insira um nome exclusivo que identifique seu servidor do Banco de Dados do Azure para MySQL. Por exemplo, "mysqldbserver". O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter de 3 a 63 caracteres.
Fonte de dados |*Nenhuma* | Selecione *Nenhum* para criar um novo servidor do zero. (Você selecionaria *Backup* se estivesse criando um servidor de um backup geográfico de um servidor do Banco de Dados do Azure para MySQL existente).
Logon de administrador do servidor | myadmin | Insira um nome de usuário para o administrador do servidor. Não é possível usar **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public** como o nome de usuário administrador.
Senha | *Sua escolha* | Forneça uma nova senha para a conta do administrador do servidor. A senha deve ter de 8 a 128 caracteres com uma combinação de letras maiúsculas ou minúsculas, números e caracteres não alfanuméricos (!, $, #,% e assim por diante).
Confirmar senha | *Sua escolha*| Confirme a senha da conta do administrador.
Location | *A região mais próxima de seus usuários*| Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure.
Versão | *A última versão principal*| A última versão principal (a menos que você tenha requisitos específicos que exijam uma outra versão).
Computação + Armazenamento | **Uso Geral**, **Gen 5**, **2 vCores**, **5 GB**, **7 dias**, **Com redundância geográfica** |As configurações de computação, armazenamento e backup para o novo servidor. Selecione **Configurar servidor**. Em seguida, selecione o tipo de preço apropriado; para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/mysql/). Para habilitar os backups do servidor em armazenamento com redundância geográfica, selecione **Redundância Geográfica** das **Opções de Redundância de Backup**. Selecione **OK**.

   > [!NOTE]
   > Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho. Observe que servidores criados no tipo de preço Básico não podem ser dimensionados mais tarde para Uso Geral ou Otimizado para Memória. 

4. Selecione **Examinar + criar** para provisionar o servidor. O provisionamento pode levar até 20 minutos.
   
5. Selecione **Notificações** na barra de ferramentas (ícone de sino) para monitorar o processo de implantação.
   
Por padrão, os seguintes bancos de dados são criados em seu servidor: **information_schema**, **mysql**, **performance_schema** e **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor
Por padrão, o servidor criado é protegido com um firewall e não pode ser acessado publicamente. Para permitir acesso ao seu IP, acesse o recurso de servidor no portal do Azure e selecione **Segurança da conexão** no menu do lado esquerdo do recurso de servidor. Se você não souber como encontrar seu recurso, confira [Como abrir um recurso](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Segurança da conexão – Regras de firewall":::
   
Agora selecione **Adicionar o endereço IP do cliente atual** e escolha **Salvar**. Você pode adicionar IPs extras ou fornecer um intervalo de IP para se conectar ao seu servidor por meio desses IPs. Para obter mais informações, confira [Como gerenciar regras de firewall no servidor do Banco de Dados do Azure para MySQL](./concepts-firewall-rules.md)

> [!NOTE]
> Verifique se a sua rede permite o tráfego de saída pela porta 3306 usada pelo Banco de Dados do Azure para MySQL a fim de evitar problemas de conectividade.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Conectar-se ao servidor do Banco de Dados do Azure para MySQL usando o cliente de linha de comando do MySQL
Você pode escolher [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [Workbench do MySQL](./connect-workbench.md) para se conectar ao servidor do seu ambiente local. Neste guia de início rápido, executaremos **mysql.exe** no [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para se conectar ao servidor.

1. Inicie o Azure Cloud Shell no portal clicando no ícone realçado do lado superior esquerdo. Anote o nome do servidor, o nome de logon do administrador do servidor, a senha e assinatura do servidor recém-criado na seção **Visão geral**, conforme mostrado na imagem abaixo.

    >[!NOTE]
    >Se você estiver iniciando o Cloud Shell pela primeira vez, verá um prompt que solicitará a criação de um grupo de recursos na conta de armazenamento. Essa é uma etapa única e será anexada automaticamente para todas as sessões. 

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Exibição Completa do portal Cloud Shell":::
2. Execute este comando no terminal do Azure Cloud Shell. Substitua os valores pelos nomes reais do servidor e de logon do usuário administrador. O nome de usuário do administrador exige '@\<servername>', conforme mostrado abaixo para o Banco de Dados do Azure para MySQL  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Veja como é a experiência no terminal do Cloud Shell
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. No mesmo terminal do Azure Cloud Shell, crie um banco de dados **convidado** 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. Alterar para o banco de dados **convidado**
  ```
  mysql> USE guest;
  Database changed 
  ```
5. Digite ```quit``` e selecione a tecla Enter para sair do mysql.   

## <a name="clean-up-resources"></a>Limpar os recursos
Você criou com êxito um servidor do Banco de Dados do Azure para MySQL em um grupo de recursos.  Caso não espere precisar desses recursos no futuro, exclua-os eliminando o grupo de recursos ou exclua apenas o servidor MySQL. Para excluir o grupo de recursos, siga estas etapas:
1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção. 
2. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.
3. Na página Visão geral do grupo de recursos, selecione **Excluir grupo de recursos**.
4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

Para excluir o servidor, clique no botão **Excluir** na página **Visão geral** do servidor, conforme mostrado abaixo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Excluir seus recursos":::

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
>[Criar um aplicativo PHP no Windows com o MySQL](../app-service/app-service-web-tutorial-php-mysql.md)
>[Criar um aplicativo PHP no Linux com o MySQL](../app-service/containers/tutorial-php-mysql-app.md)
>[Criar um aplicativo Spring baseado em Java com o MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
