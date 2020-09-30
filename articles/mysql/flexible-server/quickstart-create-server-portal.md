---
title: 'Início Rápido: Criar um Servidor Flexível do BD do Azure para MySQL – portal do Azure'
description: Este artigo descreve como usar o portal do Azure para criar rapidamente um Servidor Flexível do Banco de Dados do Azure para MySQL em alguns minutos.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/29/2020
ms.openlocfilehash: 70f2cf183a9bd93b6066516cb68e99ee21cdc1ac
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569630"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Início Rápido: Usar o portal do Azure para criar um Servidor Flexível do Banco de Dados do Azure para MySQL

O Servidor Flexível do Banco de Dados do Azure para MySQL é um serviço gerenciado usado para executar, gerenciar e escalar servidores MySQL altamente disponíveis na nuvem. Este guia de início rápido mostra como criar um servidor flexível em alguns minutos usando o portal do Azure.

> [!IMPORTANT] 
> Atualmente, o Servidor Flexível do Banco de Dados do Azure para MySQL está em versão prévia pública

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Abra seu navegador da Web e vá para o [portal do Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Criar um Servidor Flexível do Banco de Dados do Azure para MySQL

Crie um servidor flexível com um conjunto definido de [recursos de computação e armazenamento](./concepts-compute-storage.md). Crie o serviço dentro de um [Grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

Siga estas etapas para criar um servidor flexível:

1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para MySQL**. Você também pode inserir **MySQL** na caixa de pesquisa para localizar o serviço.

    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="Opção do Banco de Dados do Azure para MySQL":::

3. Selecione **Servidor flexível** como a opção de implantação.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Opção do Banco de Dados do Azure para MySQL":::    

4. Preencha o formulário **Básico** com as seguintes informações: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Opção do Banco de Dados do Azure para MySQL"::: 
                                    
    |**Configuração**|**Valor Sugerido**|**Descrição**|
    |---|---|---|
    Subscription|O nome da sua assinatura|A assinatura do Azure que você deseja usar para o servidor. Caso você tenha várias assinaturas, escolha a assinatura na qual você deseja receber a cobrança do recurso.|
    Resource group|*myresourcegroup*| Um novo nome do grupo de recursos ou um existente de sua assinatura.|
    Nome do servidor |*mydemoserver*|Um nome exclusivo que identifique o servidor flexível. O nome de domínio *mysql.database.azure.com* é acrescentado ao nome do servidor fornecido. O servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter, pelo menos, 3 até 63 caracteres.|
    Nome de usuário do administrador |*mydemouser*| Sua própria conta de logon para uso ao se conectar ao servidor. O nome de logon do administrador não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.|
    Senha |Sua senha| Uma nova senha para a conta do administrador do servidor. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (!, $, #, % etc.).|
    Região|A região mais próxima dos usuários| A localização mais próxima dos usuários.|
    Versão|5.7| Versão principal do MySQL.|
    Computação + armazenamento | **Expansível**, **Standard_B1ms**, **10 GiB**, **7 dias** | As configurações de computação, armazenamento e backup para o novo servidor. Selecione **Configurar servidor**. *Expansível*, *Standard_B1ms*, *10 GiB* e *7 dias* são os valores padrão de **Camada de computação**, **Tamanho da computação**, **Armazenamento** e **Período de Retenção de Backup**. Você pode deixar esses controles deslizantes como estão ou ajustá-los. Para salvar essa seleção de computação e armazenamento, escolha **Salvar** para continuar com as configurações. A captura de tela abaixo mostra as opções de computação e armazenamento.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Opção do Banco de Dados do Azure para MySQL":::

5. Configurar as opções de Rede

    Na guia Rede, você pode escolher como o servidor poderá ser acessado. O Servidor Flexível do Banco de Dados do Azure para MySQL fornece duas opções para se conectar ao seu servidor por meio do *Acesso público (endereços IP permitidos)* e *Acesso privado (Integração VNET)* . Com o *Acesso público (endereços IP permitidos)* , o acesso ao seu servidor é limitado a endereços IP permitidos adicionados a uma regra de firewall. Ele impede que aplicativos e ferramentas externas se conectem ao servidor e aos bancos de dados do servidor, a menos que você crie uma regra para abrir o firewall em um endereço IP ou um intervalo específico. Com o *Acesso privado (Integração VNET)* , o acesso ao seu servidor fica limitado à sua rede virtual. Neste guia de início rápido, mostraremos como habilitar o acesso público para se conectar ao servidor. Saiba mais sobre os métodos de conectividade no [artigo sobre conceitos](./concepts-networking.md).

    > [!NOTE]
    > O método de conectividade não poderá ser alterado após a criação do servidor. Por exemplo, se você escolheu *Acesso público (endereços IP permitidos)* durante a criação, não poderá alterar a opção para *Acesso privado (Integração VNET)* após a criação. Recomendamos criar um servidor com acesso Privado para que seja possível acessar seu servidor com segurança usando a Integração VNet. Saiba mais sobre o Acesso privado no [artigo sobre conceitos](./concepts-networking.md).

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Opção do Banco de Dados do Azure para MySQL":::  

6. Selecione **Examinar + criar** para examinar a configuração do servidor flexível.

7. Selecione **Criar** para provisionar o servidor. O provisionamento pode levar alguns minutos.

8. Selecione **Notificações** na barra de ferramentas (ícone de sino) para monitorar o processo de implantação. Depois que a implantação for concluída, selecione **Fixar no painel**, que criará um bloco para esse servidor flexível no painel do portal do Azure como um atalho para a página **Visão geral** do servidor. A opção **Ir para recurso** abre a página **Visão geral** do servidor.

Por padrão, os seguintes bancos de dados são criados em seu servidor: **information_schema**, **mysql**, **performance_schema** e **sys**.

> [!NOTE]
> Verifique se a rede permite o tráfego de saída na porta 3306, que é usada pelo Servidor Flexível do Banco de Dados do Azure para MySQL para evitar problemas de conectividade.  

## <a name="connect-to-using-mysql-command-line-client"></a>Conectar-se ao servidor usando a ferramenta de linha de comando mysql

Caso tenha criado um servidor flexível com *acesso privado (Integração VNet)* , será necessário se conectar ao servidor de um recurso na mesma VNet do servidor. Crie uma máquina virtual e adicione-a à VNET criada com o servidor flexível.

Caso tenha criado um servidor flexível com *acesso público (endereços IP permitidos)* , será possível adicionar seu endereço IP local à lista de regras de firewall no servidor.

Você pode escolher [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [Workbench do MySQL](./connect-workbench.md) para se conectar ao servidor do seu ambiente local. 

Com o mysql.exe, conecte-se usando o comando abaixo. Substitua os valores por um nome do servidor e uma senha que sejam reais. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Limpar os recursos
Você criou com êxito um Servidor Flexível do Banco de Dados do Azure para MySQL em um grupo de recursos.  Caso não espere precisar desses recursos no futuro, exclua-os eliminando o grupo de recursos ou exclua apenas o servidor MySQL. Para excluir o grupo de recursos, siga estas etapas:

1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção.
1. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.
1. Na página Visão geral do grupo de recursos, selecione **Excluir grupo de recursos**.
1. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

Para excluir o servidor, clique no botão **Excluir** na página **Visão geral** do servidor, conforme mostrado abaixo:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Opção do Banco de Dados do Azure para MySQL":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)
