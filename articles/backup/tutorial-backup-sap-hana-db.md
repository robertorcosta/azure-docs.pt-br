---
title: Tutorial – fazer backup de bancos de dados do SAP HANA em VMs do Azure
description: Neste tutorial, saiba o backup de bancos de dados SAP HANA executados em uma VM do Azure pode ser realizado no cofre dos Serviços de Recuperação do Backup do Azure.
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: a622370fca3144aeb6a5d7c071c227b3c21cf135
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287183"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Tutorial: Fazer backup de bancos de dados do SAP HANA em uma VM do Azure

Este tutorial mostra a você como fazer backup de bancos de dados do SAP HANA em execução em VMs do Azure em um cofre dos Serviços de Recuperação do Backup do Azure. Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descobrir bancos de dados
> * Configurar backups

[Aqui](sap-hana-backup-support-matrix.md#scenario-support) estão todos os cenários aos quais damos suporte no momento.

## <a name="onboard-to-the-public-preview"></a>Integrar-se à versão prévia pública

Integre à versão prévia pública da seguinte maneira:

* No portal, registre sua ID da assinatura para o provedor de serviço dos Serviços de Recuperação [seguindo este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).

* Para o PowerShell, execute este cmdlet. Ele deve ser concluído como "Registrado".

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar backups, verifique se você fez o seguinte:

1. Na VM que executa o banco de dados SAP HANA, instale e habilite pacotes de driver ODBC do pacote/mídia oficial do SLES usando o zypper, da seguinte maneira:

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> Se você não estiver atualizando os repositórios, verifique se a versão de unixODBC é 2.3.4 ou superior. Para saber a versão do uniXODBC, execute `odbcinst -j` como raiz
>

2. Permita a conectividade da VM com a Internet para que ela possa acessar o Azure, conforme descrito no [procedimento abaixo](#set-up-network-connectivity).

3. Execute o script de pré-registro na máquina virtual em que o HANA está instalado como um usuário raiz. [Este script](https://aka.ms/scriptforpermsonhana) definirá as [permissões corretas](#setting-up-permissions).

## <a name="set-up-network-connectivity"></a>Configurar a conectividade de rede

Para todas as operações, a VM SAP HANA precisa de conectividade para os endereços IP públicos do Azure. As operações de VM (descoberta de banco de dados, configuração de backups, agendamento de backups, restauração de pontos de recuperação e assim por diante) não podem funcionar se não há conectividade. Estabeleça a conectividade permitindo o acesso aos intervalos de IP do datacenter do Azure:

* Você pode baixar os [intervalos de endereços IP](https://www.microsoft.com/download/details.aspx?id=41653) para datacenters do Azure e, em seguida, permitir o acesso a esses endereços IP.
* Se estiver usando NSGs (grupos de segurança de rede), você poderá usar a [tag de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) do AzureCloud para permitir todos os endereços IP públicos do Azure. Você pode usar o [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) para modificar as regras do NSG.
* A porta 443 deve ser adicionada à lista de permissões, pois o transporte é via HTTPS.

## <a name="setting-up-permissions"></a>Configurando as permissões

O script de pré-registro executa as seguintes ações:

1. Cria AZUREWLBACKUPHANAUSER no sistema HANA e adiciona estas funções e permissões necessárias:
   * ADMIN DO BANCO DE DADOS: para criar novos BDs durante a restauração.
   * LEITURA DO CATÁLOGO: para ler o catálogo de backup.
   * SAP_INTERNAL_HANA_SUPPORT: para acessar algumas tabelas privadas.
2. Adiciona uma chave a Hdbuserstore para o plug-in do HANA lidar com todas as operações (consultas de banco de dados, operações de restauração, configuração e execução de backup).

Para confirmar a criação da chave, execute o comando HDBSQL no computador HANA com credenciais de SIDADM:

```hdbsql
hdbuserstore list
```

A saída do comando deve exibir a chave {SID}{DBNAME}, com o usuário mostrado como AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Verifique se você tem um conjunto exclusivo de arquivos SSFS em /usr/sap/{SID}/home/.hdb/. Deve haver apenas uma pasta neste caminho.
>

## <a name="create-a-recovery-service-vault"></a>Criar um cofre do Serviço de Recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre de Serviços de Recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre de Serviços de Recuperação:

1. Entre na sua assinatura no [Portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Todos os serviços**

![Selecionar Todos os serviços](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Na caixa de diálogo **Todos os serviços**, insira **Serviços de Recuperação**. A lista de recurso filtra de acordo com sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação**.

![Selecione Cofres de Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. No painel de cofres dos **Serviços de Recuperação**, selecione **Adicionar**.

![Adicionar um cofre dos Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/add-vault.png)

A caixa de diálogo **Cofre dos Serviços de Recuperação** é aberta. Fornecer valores para **Nome, Assinatura, Grupo de recursos** e **Localização**

![Criar cofre de Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/create-vault.png)

* **Nome**: O nome é usado para identificar o cofre dos Serviços de Recuperação e precisa ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens. Para este tutorial, usamos o nome **SAPHanaVault**.
* **Assinatura**: Escolha a assinatura a ser usada. Se você for um membro de apenas uma assinatura, verá esse nome. Se você não tem certeza de qual assinatura usar, utilize a assinatura padrão (sugerida). Só haverá múltiplas opções se a sua conta corporativa ou de estudante estiver associada a várias assinaturas do Azure. Aqui, usamos a **assinatura do laboratório de solução do SAP HANA**.
* **Grupo de recursos**: Use um grupo de recursos existente ou crie um novo. Aqui, usamos **SAPHANADemo**.<br>
Para ver a lista de grupos de recursos disponíveis em sua assinatura, selecione **Usar existente** e, em seguida, selecione um recurso na caixa de listagem suspensa. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
* **Localização**: Selecione a região geográfica para o cofre. O cofre precisa estar na mesma região que a máquina virtual que executa o SAP HANA. Nós usamos **Leste dos EUA 2**.

5. Selecione **Examinar + criar**.

   ![Selecione Examinar + Criar](./media/tutorial-backup-sap-hana-db/review-create.png)

O cofre dos Serviços de Recuperação agora está criado.

## <a name="discover-the-databases"></a>Descobrir os bancos de dados

1. No cofre, em **Introdução**, clique em **Backup**. Em **Em que local sua carga de trabalho é executada?** , selecione **SAP HANA em VM do Azure**.
2. Clique em **Iniciar Descoberta**. Isso inicia a descoberta de VMs do Linux desprotegidas na região do cofre. Você verá a VM do Azure que deseja proteger.
3. Em **Selecionar Máquinas Virtuais**, clique no link para baixar o script que fornece permissões para que o serviço de Backup do Azure acesse as VMs SAP HANA para descoberta de banco de dados.
4. Execute o script na VM que hospeda os bancos de dados do SAP HANA dos quais você deseja fazer backup.
5. Depois de executar o script na VM, em **Selecionar Máquinas Virtuais**, selecione a VM. Em seguida, clique em **Descobrir BDs**.
6. O Backup do Azure descobre todos os bancos de dados do SAP HANA na VM. Durante a descoberta, o Backup do Azure registra a VM com o cofre e instala uma extensão na VM. Nenhum agente é instalado no banco de dados.

   ![Descobrir os bancos de dados](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurar o backup

Agora que os bancos de dados dos quais desejamos fazer backup foram descobertos, vamos habilitar o backup.

1. Clique em **Configurar Backup**.

![Configurar o backup](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Em **Selecionar os itens para fazer backup**, selecione um ou mais bancos de dados que você deseja proteger e clique em **OK**.

![Selecionar itens para fazer backup](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Em **Política de Backup > Escolher política de backup**, crie uma política de backup para os bancos de dados, de acordo com as instruções na próxima seção.

![Escolher política de backup](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Depois de criar a política, no **menu Backup**, clique em **Habilitar o backup**.

   ![Clique em Habilitar o backup](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

## <a name="creating-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.

Especifique as configurações de política da seguinte maneira:

1. Em **Nome da política**, insira um nome para a nova política. Nesse caso, digite **SAPHANA**.

![Inserir um nome para a nova política](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Em **Política de backup completo**, selecione uma **Frequência de Backup**. Você pode escolher **Diário** ou **Semanal**. Para este tutorial, escolhemos o backup **Diário**.

![Selecionar uma frequência de backup](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Em **Período de Retenção**, defina as configurações de retenção para o backup completo.
   * Por padrão, todas as opções são selecionadas. Desmarque os limites de período de retenção que você não deseja usar e marque os que você deseja.
   * O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
   * Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
   * O backup para um dia específico é marcado e mantido com base na configuração e no período de retenção semanal.
   * Os intervalos de retenção mensal e anual comportam-se de maneira semelhante.
4. No menu de **Política de Backup Completo**, clique em **OK** para aceitar as configurações.
5. Em seguida, selecione **Backup Diferencial** para adicionar uma política diferencial.
6. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência. Habilitamos um backup diferencial a cada **Domingo** às **02:00**, que é mantido por **30 dias**.

   ![Política de backup diferencial](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

>[!NOTE]
>Atualmente, backups incrementais não são compatíveis.
>

7. Clique em **OK** para salvar a política e retornar para o menu principal da **Política de backup**.
8. Selecione **Backup de Log** para adicionar uma política de backup de log transacional.
   * **Backup de Log** é definido por padrão como **Habilitar**. Isso não pode ser desabilitado, pois o SAP HANA gerencia todos os backups de log.
   * Definimos **2 horas** como o agendamento de backup e **15 dias** de período de retenção.

    ![Política de backup de log](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

>[!NOTE]
> Os backups de log só começam a fluir depois que um backup completo bem-sucedido é concluído.
>

9. Clique em **OK** para salvar a política e retornar para o menu principal da **Política de backup**.
10. Depois de terminar de definir a política de backup, clique em **OK**.

Você configurou com êxito os backups para seus bancos de dados do SAP HANA.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [executar backups sob demanda em bancos de dados do SAP HANA em execução em VMs do Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Saiba como [restaurar bancos de dados do SAP HANA em execução em VMs do Azure](sap-hana-db-restore.md)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
