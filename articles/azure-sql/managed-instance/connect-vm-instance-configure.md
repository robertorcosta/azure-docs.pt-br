---
title: Configurar a conectividade de VM do Azure
titleSuffix: Azure SQL Managed Instance
description: Conecte-se ao Azure SQL Instância Gerenciada usando o SQL Server Management Studio de uma máquina virtual do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: 76c4e2c5052e70c4c6cb8ff631151a5e6fc544e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84706351"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>Início rápido: configurar uma VM do Azure para se conectar ao Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este guia de início rápido mostra como configurar uma máquina virtual do Azure para se conectar ao Azure SQL Instância Gerenciada usando o SQL Server Management Studio (SSMS). 


Para obter um guia de início rápido mostrando como se conectar de um computador cliente local usando uma conexão ponto a site, consulte [Configurar uma conexão ponto a site](point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido usa os recursos criados em [criar uma instância gerenciada](instance-create-quickstart.md) como ponto de partida.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Criar uma nova VNet de sub-rede

As etapas a seguir criam uma nova sub-rede na VNet do SQL Instância Gerenciada para que uma máquina virtual do Azure possa se conectar à instância gerenciada. A sub-rede do SQL Instância Gerenciada é dedicada a instâncias gerenciadas. Você não pode criar nenhum outro recurso (por exemplo, máquinas virtuais do Azure) nessa sub-rede.

1. Abra o grupo de recursos para a instância gerenciada que você criou no guia de início rápido [criar uma instância gerenciada](instance-create-quickstart.md) . Selecione a rede virtual para sua instância gerenciada.

   ![Recursos da Instância Gerenciada de SQL](./media/connect-vm-instance-configure/resources.png)

2. Selecione **sub-redes** e, em seguida, selecione **+ Sub-rede** para criar uma sub-rede.

   ![Sub-redes do SQL Instância Gerenciada](./media/connect-vm-instance-configure/subnets.png)

3. Preencha o formulário usando as informações nesta tabela:

   | Configuração| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- |
   | **Nome** | Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   | **Intervalo de endereços (bloco CIDR)** | Um intervalo válido | O valor padrão é bom para este início rápido.|
   | **Grupo de segurança de rede** | Nenhum | O valor padrão é bom para este início rápido.|
   | **Tabela de rotas** | Nenhum | O valor padrão é bom para este início rápido.|
   | **Pontos de extremidade de serviço** | 0 selecionado | O valor padrão é bom para este início rápido.|
   | **Delegação de sub-rede** | Nenhum | O valor padrão é bom para este início rápido.|

   ![Nova sub-rede do SQL Instância Gerenciada para VM do cliente](./media/connect-vm-instance-configure/new-subnet.png)

4. Selecione **OK** para criar essa sub-rede adicional na VNET do SQL instância gerenciada.

## <a name="create-a-vm-in-the-new-subnet"></a>Criar uma VM na nova sub-rede 

As etapas a seguir mostram como criar uma máquina virtual na nova sub-rede para se conectar ao SQL Instância Gerenciada.

## <a name="prepare-the-azure-virtual-machine"></a>Preparar a máquina virtual do Azure

Como o SQL Instância Gerenciada é colocado em sua rede virtual privada, você precisa criar uma VM do Azure com uma ferramenta de cliente SQL instalada, como SQL Server Management Studio ou Azure Data Studio. Essa ferramenta permite que você se conecte ao SQL Instância Gerenciada e execute consultas. Este início rápido usa o SQL Server Management Studio.

A maneira mais fácil de criar uma máquina virtual cliente com todas as ferramentas necessárias é usar os modelos do Azure Resource Manager.

1. Verifique se você está conectado ao portal do Azure em outra guia do navegador. Em seguida, selecione o seguinte botão para criar uma máquina virtual do cliente e instalar SQL Server Management Studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Preencha o formulário usando as informações na tabela a seguir:

   | Configuração| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- |
   | **Assinatura** | Uma assinatura válida | Precisa ser uma assinatura na qual você tem permissão para criar novos recursos. |
   | **Grupo de recursos** |O grupo de recursos que você especificou no início rápido [criar SQL instância gerenciada](instance-create-quickstart.md)|Esse grupo de recursos precisa ser aquele no qual a VNet existe.|
   | **Localidade** | O local para o grupo de recursos | Esse valor é preenchido com base no grupo de recursos selecionado. |
   | **Nome da máquina virtual**  | Qualquer nome válido | Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   |**Nome de usuário do administrador**|Qualquer nome de usuário válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). Não use "serveradmin", que é uma função de nível de servidor reservada.<br>Você usa esse nome de usuário sempre que você [se conecta à VM](#connect-to-the-virtual-machine).|
   |**Senha**|Qualquer senha válida|A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Você usa essa senha sempre que você [se conecta à VM](#connect-to-the-virtual-machine).|
   | **Tamanho da máquina virtual** | Qualquer tamanho válido | O padrão neste modelo de **Standard_B2s** é suficiente para este início rápido. |
   | **Localidade**|[resourceGroup().location].| Não altere esse valor. |
   | **Nome da rede virtual**|A rede virtual na qual você criou a instância gerenciada|
   | **Nome da sub-rede**|O nome da sub-rede que você criou no procedimento anterior| Não escolha a sub-rede na qual você criou a instância gerenciada.|
   | **Localização de artefatos** | [deployment().properties.templateLink.uri] | Não altere esse valor. |
   | **token SAS de localização de artefatos** | Deixar em branco | Não altere esse valor. |

   ![criar VM cliente](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Se você usou o nome da VNet sugerido e a sub-rede padrão ao [criar o SQL instância gerenciada](instance-create-quickstart.md), não precisará alterar os dois últimos parâmetros. Caso contrário, você deverá alterar esses valores para os valores inseridos quando você configurou o ambiente de rede.

3. Selecione a caixa de seleção **Concordo com os termos e condições declarados acima**.
4. Selecione **Comprar** para implantar a VM do Azure em sua rede.
5. Selecione o ícone **Notificações** para exibir o status da implantação.

> [!IMPORTANT]
> Não continue por aproximadamente 15 minutos após a criação da máquina virtual para dar tempo para que os scripts de pós-criação instalem o SQL Server Management Studio.

## <a name="connect-to-the-virtual-machine"></a>Conecte-se à máquina virtual

As etapas a seguir mostram como se conectar à sua máquina virtual recém-criada usando uma conexão Área de Trabalho Remota.

1. Após a conclusão da implantação, vá para o recurso da máquina virtual.

    ![VM](./media/connect-vm-instance-configure/vm.png)  

2. Selecione **Conectar**.

   Um formulário de Protocolo RDP (arquivo .rdp) é exibido com o IP endereço IP público e o número da porta para a máquina virtual.

   ![Formulário do RDP](./media/connect-vm-instance-configure/rdp.png)  

3. Selecione **Baixar Arquivo RDP**.

   > [!NOTE]
   > Você também pode usa SSH para se conectar à VM.

4. Feche o formulário **Conectar-se à máquina virtual**.
5. Para se conectar à sua VM, abra o arquivo RDP baixado.
6. Quando solicitado, selecione **Conectar**. Em um Mac, você precisa de um cliente RDP, como [este área de trabalho remota Client](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) da Mac App Store.

7. Insira o nome de usuário e a senha que você especificou ao criar a máquina virtual e escolha **OK**.

8. Você pode receber um aviso de certificado durante o processo de entrada. Escolha **Sim** ou **Continuar** para prosseguir com a conexão.

Você está conectado à máquina virtual no painel do Gerenciador do Servidor.

## <a name="connect-to-sql-managed-instance"></a>Conectar-se à Instância Gerenciada do SQL 

1. Na máquina virtual, abra SQL Server Management Studio.

   Demora alguns minutos para abrir, pois ele precisa concluir sua configuração, pois esta é a primeira vez que o SSMS foi iniciado.
2. Na caixa de diálogo **conectar ao servidor** , insira o nome de **host** totalmente qualificado para sua instância gerenciada na caixa **nome do servidor** . Selecione **Autenticação do SQL Server**, forneça seu nome de usuário e senha e, em seguida, selecione **Conectar**.

    ![Conexão do SSMS](./media/connect-vm-instance-configure/ssms-connect.png)  

Depois de se conectar, você pode exibir seus bancos de dados do sistema e do usuário no nó Bancos de Dados e vários objetos nos nós Segurança, Objetos do Servidor, Replicação, Gerenciamento, SQL Server Agent e XEvent Profiler.

## <a name="next-steps"></a>Próximas etapas

- Para obter um guia de início rápido mostrando como se conectar de um computador cliente local usando uma conexão ponto a site, consulte [Configurar uma conexão ponto a site](point-to-site-p2s-configure.md).
- Para obter uma visão geral das opções de conexão para aplicativos, confira [Conectar seus aplicativos à Instância Gerenciada de SQL](connect-application-instance.md).
- Para restaurar um banco de dados de SQL Server existente do local para uma instância gerenciada, você pode usar o [serviço de migração de banco de dados do Azure para migração](../../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando T-SQL RESTORE](restore-sample-database-quickstart.md) para restaurar de um arquivo de backup de banco de dados.
