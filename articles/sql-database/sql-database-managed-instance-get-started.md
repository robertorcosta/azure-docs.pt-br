---
title: 'Portal do Azure: Criar uma instância gerenciada do Banco de Dados SQL | Microsoft Docs'
description: Crie uma instância gerenciada do Banco de Dados SQL, um ambiente de rede e uma VM de cliente para acesso.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: 4f9ea699bd6d09f902a3ff97c95ff3455926a9d8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350926"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Início Rápido: Criar uma instância gerenciada do Banco de Dados SQL do Azure

Este início rápido orientará você pela criação de uma [instância gerenciada](sql-database-managed-instance.md) do Banco de Dados SQL do Azure no portal do Azure.

> [!IMPORTANT]
> Para obter as limitações, confira [Regiões compatíveis](sql-database-managed-instance-resource-limits.md#supported-regions) e [Tipos de assinatura compatíveis](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Criar uma instância gerenciada

As etapas a seguir mostram como criar uma instância gerenciada:

1. Selecione **Azure SQL** no menu esquerdo do portal do Azure. Se **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite **SQL do Azure** na caixa de pesquisa.
2. Selecione **+Adicionar** para abrir a página **Selecionar opção de implantação do SQL**. É possível exibir informações adicionais sobre uma instância gerenciada do Banco de Dados SQL do Azure selecionando **Mostrar detalhes** no bloco **Instâncias gerenciadas**.
3. Selecione **Criar**.

   ![Criar uma instância gerenciada](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Use as guias no formulário de provisionamento **Criar Instância Gerenciada do Banco de Dados SQL do Azure** para adicionar informações necessárias e opcionais. As seções a seguir descrevem essas guias.

### <a name="basics"></a>Noções básicas

- Preencha as informações obrigatórias exigidas na guia **Conceitos Básicos**. Este é um conjunto mínimo de informações exigido para provisionar uma instância gerenciada.

   ![Guia "Básico" para a criação de uma instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Use a tabela abaixo como uma referência para obter informações obrigatórias nesta guia.

   | Configuração| Valor sugerido | DESCRIÇÃO |
   | ------ | --------------- | ----------- |
   | **Assinatura** | Sua assinatura. | Uma assinatura que concede a você permissão para criar recursos. |
   | **Grupo de recursos** | Um grupo de recursos novo ou existente.|Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Nome da Instância Gerenciada** | Qualquer nome válido.|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Região** |A região na qual você deseja criar a instância gerenciada.|Para obter mais informações sobre as regiões, confira [Regiões do Azure](https://azure.microsoft.com/regions/).|
   | **Logon de administrador da Instância Gerenciada** | Qualquer nome de usuário válido. | Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Não use "serveradmin", pois essa é uma função reservada no nível de servidor.|
   | **Senha** | Qualquer senha válida.| A senha deve ter no mínimo 16 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selecione **Configurar Instância Gerenciada** para dimensionar os recursos de computação e armazenamento e examinar os tipos de preço. Use os controles deslizantes ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais. Quando terminar, selecione **Aplicar** para salvar a seleção. 

   ![Formulário da instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Para revisar suas escolhas antes de criar uma instância gerenciada, você pode selecionar **Revisar + criar**. Ou configure as opções de rede selecionando **Avançar: Rede**.

### <a name="networking"></a>Rede

- Preencha as informações opcionais na guia **Rede**. Se você omitir essas informações, o portal aplicará as configurações padrão.

   ![Guia "Rede" para a criação de uma instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Use a tabela abaixo como uma referência para obter informações obrigatórias nesta guia.

   | Configuração| Valor sugerido | DESCRIÇÃO |
   | ------ | --------------- | ----------- |
   | **Rede virtual** | Selecione a **Criar nova rede virtual** ou uma rede virtual e uma sub-rede válidas.| Se uma rede ou uma sub-rede não estiver disponível, ela precisará ser [modificada para atender aos requisitos de rede](sql-database-managed-instance-configure-vnet-subnet.md) antes de ser selecionada como um destino para a nova instância gerenciada. Para obter mais informações sobre os requisitos de configuração do ambiente de rede para uma instância gerenciada, confira [Configurar uma rede virtual para uma instância gerenciada](sql-database-managed-instance-connectivity-architecture.md). |
   | **Tipo de conexão** | Escolha entre um tipo de conexão de proxy e redirecionamento.|Para obter mais informações sobre tipos de conexão, confira [Política de conexão do Banco de Dados SQL do Azure](sql-database-connectivity-architecture.md#connection-policy).|
   | **Ponto de extremidade público**  | Selecione **Habilitar**. | Para que a instância gerenciada esteja acessível por meio do ponto de extremidade de dados público, você precisa habilitar essa opção. | 
   | **Permitir o acesso de** (se o **Ponto de extremidade público** estiver habilitado) | Selecione uma das opções.   |A experiência do portal permite configurar um grupo de segurança com um ponto de extremidade público. </br> </br> De acordo com o seu cenário, selecione uma das seguintes opções: </br> <ul> <li>**Serviços do Azure**: recomendamos essa opção quando você estiver se conectando de Power BI ou de outro serviço multilocatário. </li> <li> **Internet**: use para fins de teste quando desejar criar rapidamente uma instância gerenciada. Não a recomendamos para ambientes de produção. </li> <li> **Sem acesso**: esta opção cria a regra de segurança **Negar**. Modifique essa regra para tornar uma instância gerenciada acessível por meio de um ponto de extremidade público. </li> </ul> </br> Para obter mais informações sobre a segurança do ponto de extremidade público, confira [Usando uma instância gerenciada do Banco de Dados SQL do Azure de forma segura com um ponto de extremidade público](sql-database-managed-instance-public-endpoint-securely.md).|

- Selecione **Revisar + criar** para revisar suas escolhas antes de criar uma instância gerenciada. Ou defina mais configurações personalizadas selecionando **Avançar: configurações adicionais**.

### <a name="additional-settings"></a>Configurações adicionais

- Preencha as informações opcionais na guia **Configurações adicionais**. Se você omitir essas informações, o portal aplicará as configurações padrão.

   ![Guia "Configurações adicionais" para a criação de uma instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Use a tabela abaixo como uma referência para obter informações obrigatórias nesta guia.

   | Configuração| Valor sugerido | DESCRIÇÃO |
   | ------ | --------------- | ----------- |
   | **Ordenação** | Escolha a ordenação que deseja usar para a instância gerenciada. Se estiver migrando bancos de dados do SQL Server, verifique a ordenação de origem usando `SELECT SERVERPROPERTY(N'Collation')` e use esse valor.| Para obter informações sobre ordenações, confira [Definir ou alterar a ordenação do servidor](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Fuso horário** | Selecione o fuso horário que sua instância gerenciada observará.|Para obter mais informações, confira [Fusos horários](sql-database-managed-instance-timezone.md).|
   | **Usar como secundário de failover** | Selecione **Sim**. | Habilite esta opção para usar a instância gerenciada como o secundário de um grupo de failover.|
   | **Instância gerenciada primária** (se **Usar como secundário de failover** estiver definido como **Sim**) | Escolha uma instância gerenciada primária existente que será ingressada na mesma zona DNS que a instância gerenciada que você está criando. | Esta etapa habilitará a configuração pós-criação do grupo de failover. Para obter mais informações, confira [Tutorial: Adicionar uma instância gerenciada do Banco de Dados SQL a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Examinar + criar

5. Selecione a guia **Examinar + criar** para examinar suas escolhas antes de criar uma instância gerenciada.

   ![Guia para revisar e criar uma instância gerenciada](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Selecione **Criar** para iniciar o provisionamento da instância gerenciada.

> [!IMPORTANT]
> A implantação de uma instância gerenciada é uma operação de execução longa. A implantação da primeira instância na sub-rede normalmente demora muito mais do que a implantação em uma sub-rede com instâncias gerenciadas existentes. Para ver tempos de provisionamento médios, confira [Operações de gerenciamento da instância gerenciada](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Monitorar o progresso da implantação

7. Selecione o ícone **Notificações** para exibir o status da implantação.

   ![Progresso da implantação de uma instância gerenciada](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Selecione **Implantação em andamento** na notificação para abrir a janela da instância gerenciada e monitorar melhor o progresso da implantação. 

> [!TIP]
> Se tiver fechado o navegador da Web ou saído da tela de progresso da implantação, siga estas etapas para localizar a tela de progresso da implantação:
> 1. No portal do Azure, abra o grupo de recursos (na guia **Conceitos Básicos**) no qual você está implantando uma instância gerenciada.
> 2. Selecione **Implantações**.
> 3. Selecione a operação de implantação de instância gerenciada em andamento.

## <a name="post-deployment-operations"></a>Operações de pós-implantação

Para examinar os recursos criados, ajuste as configurações de rede e recupere os detalhes de conexão do host (FQDN) de acordo com as etapas descritas nesta seção.

### <a name="view-resources-created"></a>Exibir recursos criados

Após a implantação bem-sucedida da instância gerenciada, para exibir os recursos criados:

1. Abra o grupo de recursos da instância gerenciada. Exiba os recursos criados para você no início rápido [Criar uma instância gerenciada](#create-a-managed-instance).

   ![Recursos da instância gerenciada](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Exibir e ajustar configurações de rede

Para, opcionalmente, ajustar as configurações de rede, inspecione o seguinte:

1. Selecione a tabela de rotas para revisar a UDR (rota definida pelo usuário) criada para você.

   ![Tabela de rotas](./media/sql-database-managed-instance-get-started/route-table.png)

2. Na tabela de rotas, revise as entradas para rotear o tráfego de/para a rede virtual da instância gerenciada. Se você criar ou configurar sua tabela de rotas manualmente, crie essas entradas na tabela de rotas de instância gerenciada.

   ![Entrada para uma sub-rede da instância gerenciada para o local](./media/sql-database-managed-instance-get-started/udr.png)

3. Retorne ao grupo de recursos e selecione o grupo de segurança de rede.

   ![Grupo de segurança de rede](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Examine as regras de segurança de entrada e saída. 

   ![Regras de segurança](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Se tiver configurado um ponto de extremidade público para sua instância gerenciada, será necessário abrir portas para permitir o tráfego de rede que permite conexões com a instância gerenciada da Internet pública. Confira [Configurar um ponto de extremidade público para instância gerenciada](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) para saber mais.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Recuperar detalhes de conexão para a instância gerenciada

Para conectar-se à instância gerenciada, siga estas etapas para recuperar o nome do host e o FQDN (nome de domínio totalmente qualificado):

1. Retorne ao grupo de recursos e selecione sua instância gerenciada.

   ![Instância gerenciada no grupo de recursos](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Na guia **Visão Geral**, localize a propriedade **Host**. Copie o nome do host da instância gerenciada para uso no próximo início rápido.

   ![Nome do host](./media/sql-database-managed-instance-get-started/host-name.png)

   O valor copiado representa um FQDN (nome de domínio totalmente qualificado) que pode ser usado para conectar-se à instância gerenciada. Ele é semelhante ao seguinte endereço de exemplo: *nome_do_seu_host.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como se conectar a uma instância gerenciada:
- Para obter uma visão geral das opções de conexão para aplicativos, confira [Conectar seus aplicativos a uma instância gerenciada](sql-database-managed-instance-connect-app.md).
- Para obter um Início Rápido que mostra como se conectar a uma instância gerenciada em uma máquina virtual do Azure, confira [Configurar uma conexão com uma máquina virtual do Azure](sql-database-managed-instance-configure-vm.md).
- Para obter um Início Rápido que mostra como se conectar a uma instância gerenciada em um computador cliente local usando uma conexão ponto a site, confira [Configurar uma conexão ponto a site](sql-database-managed-instance-configure-p2s.md).

Para restaurar um banco de dados existente do SQL Server do local para uma instância gerenciada: 
- Use o [Serviço de Migração de Banco de Dados do Azure para migração](../dms/tutorial-sql-server-to-managed-instance.md) para fazer a restauração de um arquivo de backup de banco de dados. 
- Use o [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) para fazer a restauração de um arquivo de backup de banco de dados.

Para o monitoramento avançado do desempenho de banco de dados de instância gerenciada com inteligência de solução de problemas interna, confira [Monitorar o Banco de Dados SQL do Azure usando a Análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).
