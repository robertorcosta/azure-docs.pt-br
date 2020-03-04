---
title: Migrar VMs do AWS para o serviço Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar VMs do Windows em execução no AWS (Amazon Web Services) para o Azure usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a1952f5ece4c021834fb98f8a09f1a2738e6469
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789385"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrar das VMs do AWS (Amazon Web Services) para o Azure

Este tutorial ensina como migrar VMs (máquinas virtuais) do AWS (Amazon Web Services) para VMs do Azure, usando o Azure Site Recovery. Ao migrar instâncias de EC2 do AWS para Azure, as VMs são tratadas como computadores físicos locais. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar pré-requisitos
> * Preparar os recursos do Azure
> * Preparar instâncias de EC2 do AWS para migração
> * Implante um servidor de configuração
> * Habilite a replicação para VMs
> * Teste o failover para verificar se tudo está funcionando
> * Executar um único failover para Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


> [!NOTE]
> Agora, você pode usar o serviço de Migrações para Azure para migrar instâncias da AWS para o Azure. [Saiba mais](../migrate/tutorial-migrate-physical-virtual-machines.md).

## <a name="prerequisites"></a>Prerequisites
- Assegure-se de que as VMs que você quer migrar estejam executando uma versão do sistema operacional com suporte. Versões com suporte incluem: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - Versão de 64 bits do Windows Server 2008 R2 SP1 ou posterior
  - Red Hat Enterprise Linux 6.4 a 6.10, 7.1 a 7.6 (somente instâncias virtualizadas de HVM) *(não há suporte para instâncias que executam drivers RedHat PV.)*
  - CentOS 6.4 a 6.10, 7.1 a 7.6 (somente instâncias virtualizadas de HVM)
 
- O serviço de mobilidade deve ser instalado em cada VM que você quer replicar. 

    > [!IMPORTANT]
    > O Site Recovery instala esse serviço automaticamente quando você habilita a replicação para a VM. Para instalação automática, você deve preparar uma conta nas instâncias de EC2 que o Site Recovery usará para acessar a VM. Você pode usar uma conta local ou de domínio. 
    > - Para VMs do Linux, a conta deve ser a raiz no servidor Linux de origem. 
    > - Para VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o Controle de Acesso de Usuários Remotos no computador local:
    >
    >      No registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**,       adicione a entrada DWORD **LocalAccountTokenFilterPolicy** e defina o valor para **1**.

- Uma instância separada de EC2 que é possível usar como o servidor de configuração do Site Recovery. Essa instância deve estar em execução no Windows Server 2012 R2.

## <a name="prepare-azure-resources"></a>Preparar os recursos do Azure

É necessário ter alguns recursos prontos no Azure para as instâncias de EC2 migradas usarem. Isso inclui uma conta de armazenamento, um cofre e uma rede virtual.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Imagens de comutadores replicados são mantidas no Armazenamento do Microsoft Azure. VMs do Azure são criadas a partir do armazenamento ao fazer failover do local para o Azure.

1. No [portal do Azure](https://portal.azure.com), no menu esquerdo, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.
2. Insira um nome para a conta de armazenamento. Nestes tutoriais, usamos o nome **awsmigrated2017**. O nome deve:
    - Ser exclusivo no Azure
    - Ter entre 3 e 24 caracteres
    - Contém apenas números e letras minúsculas
3. Deixe os padrões para **Modelo de implantação**, **Tipo de conta**, **Desempenho** e **Transferência segura necessária**.
5. Para **Replicação**, selecione o **RA-GRS** padrão.
6. Selecione a assinatura que você quer usar para este tutorial.
7. Para **Grupo de recursos**, selecione **Criar novo**. Neste exemplo, usamos **migrationRG** para o nome do grupo de recursos.
8. Em **Localização**, selecione **Europa Ocidental**.
9. Selecione **Criar** para criar a conta de armazenamento.

### <a name="create-a-vault"></a>Criar um cofre

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Pesquise e, em seguida, selecione **cofres dos Serviços de Recuperação**.
2. Na página de cofres dos Serviços de Recuperação do Azure, selecione **Adicionar**.
3. Para **Nome**, insira **myVault**.
4. Para **Assinatura**, selecione a assinatura que você quer usar.
4. Para **Grupo de Recursos**, selecione **Usar existente** e, em seguida, selecione **migrationRG**.
5. Em **Localização**, selecione **Europa Ocidental**.
5. Selecione **Fixar no painel** para poder acessar rapidamente o novo cofre do painel.
7. Quando terminar, selecione **Criar**.

Para ver o novo cofre, vá para **Painel** > **Todos os recursos**. O novo cofre também aparece na página **Cofres dos Serviços de Recuperação**.

### <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Quando as VMs do Azure são criadas após a migração (failover), elas são ingressadas nessa rede do Azure.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** >
   **Rede virtual**.
3. Para **Nome**, insira **myMigrationNetwork**.
4. Deixe o valor padrão para o **Espaço de endereço** (é obrigatório inserir o valor).
5. Para **Assinatura**, selecione a assinatura que você quer usar.
6. Para **Grupo de recursos**, selecione **Usar existente** e, em seguida, selecione **migrationRG**.
7. Em **Localização**, selecione **Europa Ocidental**.
8. Em **Sub-rede**, deixe os valores padrão para **Nome** e **intervalo de IP (é obrigatório inserir o valor)** .
9. Adicione instruções para as configurações de proteção contra DDoS.
10. Deixe a opção **Pontos de Extremidade de Serviço** desabilitada.
11. Adicione instruções para as configurações do firewall.
12. Quando terminar, selecione **Criar**.

## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura

Na página do cofre no portal do Azure, na seção **Introdução**, selecione **Site Recovery** e, em seguida, selecione **Preparar a Infraestrutura**. Conclua as etapas a seguir.

### <a name="1-protection-goal"></a>1: Meta de proteção

Na página **Meta de Proteção**, selecione os valores a seguir:

|    |  |
|---------|-----------|
| Onde seus computadores estão localizados? |Selecione **Local**.|
| Para qual local deseja replicar as máquinas? |Selecione **Para Azure**.|
| Você está realizando uma migração? | Selecione **Sim** e, em seguida, marque a caixa ao lado de **Entendo, mas desejo continuar com o Azure Site Recovery.**
| Seus computadores são virtualizados? |Selecione **Não virtualizado/Outro**.|

Quando terminar, selecione **OK** para ir à próxima seção.

### <a name="2-select-deployment-planning"></a>2: Selecionar o planejamento de implantação

Em **Você concluiu o planejamento de implantação?** , selecione **Farei isso mais tarde** e, em seguida, selecione**OK**.

### <a name="3-prepare-source"></a>3: Preparar fonte

Na página **Preparar fonte**, selecione **+ Servidor de Configuração**.

1. Use uma instância de EC2 em execução no Windows Server 2012 R2 para criar um servidor de configuração e registre-o no cofre de recuperação.
2. Configure o proxy na VM da instância de EC2 que está usando como o servidor de configuração para poder acessar as [URLs de serviço](site-recovery-support-matrix-to-azure.md).
3. Baixe a [Configuração Unificada do Microsoft Azure Site Recovery](https://aka.ms/unifiedinstaller_wus). Você pode baixá-lo no computador local e copiá-lo para a VM que está sendo utilizada como o servidor de configuração.
4. Selecione o botão **Baixar** para baixar a chave de registro do cofre. Copie o arquivo baixado para a VM que está sendo utilizada como o servidor de configuração.
5. Na VM, clique com o botão direito do mouse no instalador que você baixou para a Configuração Unificada do Microsoft Azure Site Recovery e, em seguida, selecione **Executar como administrador**.

    1. Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor de processo** e, em seguida, selecione **Avançar**.
    2. Em **Licença de Software de Terceiros**, selecione **Aceito o contrato de licença de terceiros** e, em seguida, selecione **Avançar**.
    3. Em **Registro**, selecione **Procurar** e, em seguida, navegue até o local em que você colocou o arquivo da chave de registro do cofre. Selecione **Avançar**.
    4. Em **Configurações da Internet**, selecione **Conectar ao Azure Site Recovery sem um servidor proxy** e, em seguida, selecione **Avançar**.
    5. A página **Verificação de Pré-requisitos** executa verificações de vários itens. Quando terminar, selecione **Avançar**.
    6. Em **Configuração do MySQL**, forneça as senhas necessárias e, em seguida, selecione **Avançar**.
    7. Em **Detalhes do ambiente**, selecione **Não**. Não é necessário proteger computadores VMware. Em seguida, selecione **Avançar**.
    8. Em **Local de Instalação**, selecione **Avançar** para aceitar o padrão.
    9. Em **Seleção de Rede**, selecione **Avançar** para aceitar o padrão.
    10. Em **Resumo**, selecione **Instalar**.
    11. O **Progresso da Instalação** mostra informações sobre o processo de instalação. Quando terminar, selecione **Concluir**. Uma janela exibe uma mensagem sobre uma reinicialização. Selecione **OK**. Em seguida, uma janela exibe uma mensagem sobre a frase secreta de conexão do servidor de configuração. Copie a frase secreta para a área de transferência e salve-a em algum local seguro.
6. Na VM, execute spsconfigtool.exe para criar uma ou mais contas de gerenciamento no servidor de configuração. Certifique-se de que as contas de gerenciamento tenham permissões de administrador nas instâncias de EC2 que você quer migrar.

Quando terminar de configurar o servidor de configuração, volte para o portal e selecione o servidor que você criou para **Servidor de Configuração**. Selecione **OK** para ir para 3: Preparar destino.

### <a name="4-prepare-target"></a>4: Preparar o destino

Nesta seção, você vai inserir informações sobre os recursos que criou em [Preparar recursos do Azure](#prepare-azure-resources) anteriormente neste tutorial.

1. Em **Assinatura**, selecione a assinatura do Azure que você usou no tutorial [Preparar o Azure](tutorial-prepare-azure.md).
2. Selecione **Gerenciador de Recursos** como o modelo de implantação.
3. O Site Recovery verifica se você tem uma ou mais contas de armazenamento e rede compatíveis do Azure. Esses devem ser os recursos que você criou em [Preparar recursos do Azure](#prepare-azure-resources) anteriormente neste tutorial.
4. Quando terminar, selecione **OK**.

### <a name="5-prepare-replication-settings"></a>5: Preparar configurações de replicação

Antes de poder habilitar a replicação, será necessário criar uma política de replicação.

1. Selecione **Criar e Associar**.
2. Em **Nome**, insira **myReplicationPolicy**.
3. Deixe o restante das configurações padrão e, em seguida, selecione **OK** para criar a política. A nova política é associada automaticamente ao servidor de configuração.

Quando terminar todas as cinco seções em **Preparar a Infraestrutura**, selecione **OK**.

## <a name="enable-replication"></a>Habilitar a replicação

Habilite a replicação para cada VM que você quer migrar. Quando a replicação estiver habilitada, o Site Recovery instalará automaticamente o serviço de Mobilidade.

1. Vá para o [Portal do Azure](https://portal.azure.com).
1. Na página do cofre, em **Introdução**, selecione **Site Recovery**.
2. Em **Para computadores locais e VMs do Azure**, selecione **Etapa 1: Replicar aplicativo**. Complete as páginas do assistente com as informações a seguir. Selecione **OK** em cada página quando terminar:
   - 1: Configurar fonte

     |  |  |
     |-----|-----|
     | Origem: | Selecione **Local**.|
     | Local de origem:| Insira o nome da instância de EC2 do servidor de configuração.|
     |Tipo de computador: | Selecione **Computadores físicos**.|
     | Servidor de processo: | Selecione o servidor de configuração na lista suspensa.|

   - 2: Configurar destino

     |  |  |
     |-----|-----|
     | Destino: | Deixar o padrão.|
     | Assinatura: | Selecione a assinatura que você está usando.|
     | Grupo de recursos do failover de postagem:| Use o grupo de recursos que você criou em [Preparar recursos do Azure](#prepare-azure-resources).|
     | Modelo de implantação pós-failover: | Selecione **Gerenciador de Recursos**.|
     | Conta de armazenamento: | Selecione a conta de armazenamento que você criou em [Preparar recursos do Azure](#prepare-azure-resources).|
     | Rede do Azure: | Selecione **Configurar agora para os computadores selecionados**.|
     | Rede do Azure pós-failover: | Escolha a rede criada em [Preparar recursos do Azure](#prepare-azure-resources).|
     | Sub-rede: | Selecione o **padrão** na lista suspensa.|

   - 3: Selecionar computadores físicos

     Selecione **Computador físico** e, em seguida, insira os valores para**Nome**, **Endereço IP** e **Tipo do SO** da instância de EC2 que você quer migrar. Selecione **OK**.

   - 4: Configurar propriedades

     Selecione a conta que você criou no servidor de configuração e, em seguida, selecione **OK**.

   - 5: Definir configurações de replicação

     Certifique-se de que a política de replicação selecionada na lista suspensa é **myReplicationPolicy** e, em seguida, selecione**OK**.

3. Quando o assistente terminar, selecione**Habilitar replicação**.

Para acompanhar o progresso do trabalho **Habilitar Proteção**, vá para **Monitoramento e relatórios** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.        

Ao habilitar a replicação para uma VM, as alterações podem demorar 15 minutos ou mais para entrarem em vigor e aparecerem no portal.

## <a name="run-a-test-failover"></a>Execute um teste de failover

Ao executar um failover de teste, os seguintes eventos ocorrem:

- Uma verificação de pré-requisitos é executada para garantir que todas as condições exigidas para failover foram cumpridas.
- O failover processa os dados para que uma VM do Azure possa ser criada. Se você selecionar o último ponto de recuperação, um ponto de recuperação será criado a partir dos dados.
- Uma VM do Azure é criada usando os dados processados na etapa anterior.

No portal, execute o failover de teste:

1. Na página do seu cofre, acesse **Itens Protegidos** > **Itens Replicados**. Selecione a VM e, em seguida, selecione **Failover de Teste**.
2. Selecione um ponto de recuperação para usar no failover:
    - **Mais recente processado**: Faz failover na VM para o último ponto de recuperação que foi processado pelo Site Recovery. A carimbo de data/hora é mostrado. Com essa opção, nenhum tempo é gasto no processamento de dados, portanto, ele fornece um RTO (Objetivo do Tempo de Recuperação) baixo.
    - **Consistente com o aplicativo mais recente**: Essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
    - **Personalizado**: Selecione qualquer ponto de recuperação.

3. Em **Failover de Teste**, selecione a rede de destino do Azure à qual as VMs do Azure serão conectadas após o failover. Essa deve ser a rede criada em [Preparar recursos do Azure](#prepare-azure-resources).
4. Selecione **OK** para iniciar o failover. Para acompanhar o progresso, selecione a VM para exibir as propriedades. Ou você pode selecionar o trabalho **Failover de Teste** na página do cofre. Para fazer isso, selecione **Monitoramento e relatórios** > **Trabalhos** >  **Trabalhos do Site Recovery**.
5. Quando o failover for concluído, a réplica da VM do Azure aparecerá no portal do Azure. Para exibir a VM, selecione **Máquinas Virtuais**. Certifique-se de que a VM é do tamanho apropriado, se está conectada à rede correta e se está em execução.
6. Agora você certamente poderá se conectar à VM replicada no Azure.
7. Para excluir as VMs do Azure que foram criadas durante o failover de teste, selecione **Limpar failover de teste** no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.

Em alguns cenários, o failover exige processamento adicional. O processamento demora de 8 a 10 minutos para ser concluído.

## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute um failover real para as instâncias de EC2 para migrá-las para as VMs do Azure:

1. Em **Itens Protegidos** > **Itens Replicados**, selecione as instâncias do AWS e, em seguida, selecione **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Selecione o ponto de recuperação mais recente e inicie o failover. Você pode acompanhar o progresso do failover na página **Trabalhos** .
1. Verifique se a VM aparece em **Itens Replicados**.
2. Clique com o botão direito do mouse em cada VM e selecione **Concluir Migração**. Isso faz o seguinte:

   - Isso conclui o processo de migração, interrompe a replicação da VM do AWS e interrompe a cobrança do Site Recovery para a VM.
   - Esta etapa limpa os dados de replicação. Ela não exclui as VMs migradas. 

     ![Migração completa](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Não cancele um failover em andamento*. Antes de iniciar o failover, a replicação de VM será interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.  


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a migrar instâncias de EC2 do AWS para VMs do Azure. Para saber mais sobre VMs do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](../virtual-machines/windows/tutorial-manage-vm.md)
