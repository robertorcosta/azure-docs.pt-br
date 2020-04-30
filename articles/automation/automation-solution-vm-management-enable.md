---
title: Habilitar a solução iniciar/parar VMs do Azure durante o expediente
description: Este artigo descreve como habilitar a solução de VM de início/parada da automação do Azure para suas máquinas virtuais do Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096955"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Habilitar a solução de inicialização/parada de VMs do Azure

Execute as etapas a seguir para adicionar a solução de **iniciar/parar VMs fora do horário comercial** a uma conta de automação nova ou existente e log Analytics espaço de trabalho vinculado. Depois de concluir o processo de integração, configure as variáveis para personalizar a solução.

>[!NOTE]
>Para usar essa solução com VMs clássicas, você precisa de uma conta Executar como clássica, que não é criada por padrão. Para obter instruções sobre como criar uma conta Executar como clássica, consulte [criar uma conta Executar como clássica](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Habilitar a solução

1. Entre no [portal](https://portal.azure.com)do Azure.

2. Pesquise e selecione **contas de automação**.

3. Na página contas de automação, selecione sua conta de automação na lista.

4. Na conta de automação, selecione **iniciar/parar VM** em **recursos relacionados**. A partir daqui, você pode clicar em **Saiba mais sobre e habilite a solução**. Se você já tiver uma solução Iniciar/Parar VM implantada, selecione-a clicando em **Gerenciar a solução** e localizando-a na lista.

   ![Habilitar a conta de automação](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Você também pode criá-lo em qualquer lugar no portal do Azure, clicando em **criar um recurso**. Na página Marketplace, digite uma palavra-chave, como **Iniciar** ou **Iniciar/Parar**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Como alternativa, você pode digitar uma ou mais palavras-chave do nome completo da solução e, em seguida, pressionar Enter. Selecione **Iniciar/Parar VMs fora do horário** nos resultados da pesquisa.

5. Na página **Iniciar/Parar VMs durante as horas de folga** para a solução selecionada, revise as informações de resumo e clique em **Criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

6. A página Adicionar Solução é exibida. Você será solicitado a configurar a solução antes de poder importá-la para sua assinatura de automação.

   ![Página Adicionar Solução de Gerenciamento de VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Na página Adicionar Solução, selecione **Workspace**. Selecione um espaço de trabalho do Log Analytics que esteja vinculada à mesma assinatura do Azure na qual a conta de Automação está. Se você não tiver um workspace, selecione **Criar Novo Workspace**. Na página Log Analytics espaço de trabalho, execute as seguintes etapas:

   - Especifique um nome para o novo espaço de trabalho Log Analytics, como **ContosoLAWorkspace**.
   - Selecione uma **assinatura** à qual vincular selecionando na lista suspensa, se o padrão selecionado não for apropriado.
   - Em **Grupo de Recursos**, você pode criar um novo grupo de recursos ou selecionar um existente.
   - Selecione um **local**.
   - Selecione um **tipo de preço**. Escolha a opção **Por GB (autônomo)**. Os logs de Azure Monitor têm [preços](https://azure.microsoft.com/pricing/details/log-analytics/) atualizados e a camada por GB é a única opção.

   > [!NOTE]
   > Ao habilitar soluções, somente determinadas regiões têm suporte para vincular um Log Analytics espaço de trabalho e uma conta de automação.
   >
   > Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

8. Depois de fornecer as informações necessárias na página Espaço de Trabalho do Log Analytics, clique em **Criar**. Você pode acompanhar o progresso em **Notificações** no menu, que retornará a página Adicionar Solução ao terminar.

9. Na página Adicionar Solução, selecione **Conta de automação**. Se você estiver criando um novo espaço de trabalho Log Analytics, poderá criar uma nova conta de automação a ser associada a ela ou selecionar uma conta de automação existente que ainda não esteja vinculada a um espaço de trabalho Log Analytics. Selecione uma conta de automação existente ou clique em **criar uma conta de automação**e, na página Adicionar conta de automação, forneça as seguintes informações:
 
   - No campo **Nome**, digite o nome da conta de Automação.

     Todas as outras opções são preenchidas automaticamente com base no espaço de trabalho do Log Analytics selecionado. Essas opções não podem ser modificadas. Uma conta Executar como do Azure é o método de autenticação padrão para os runbooks incluídos nesta solução. Depois de clicar em **OK**, as opções de configuração serão validadas e a conta de Automação será criada. Você pode acompanhar o progresso em **Notificações** no menu.

10. Por fim, na página Adicionar Solução, selecione **Configuração**. A página Parâmetros é exibida.

    ![Página de parâmetros para a solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Aqui, você será solicitado a:
  
   - Especificar os **Nomes do ResourceGroup de destino**. Esses valores são nomes de grupos de recursos que contêm VMs a serem gerenciadas por essa solução. Você pode inserir mais de um nome e separá-los por vírgula (os valores não diferenciam maiúsculas de minúsculas). O uso de um caractere curinga tem suporte para selecionar VMs em todos os grupos de recursos na assinatura. Esse valor é armazenado nas variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**.
  
   - Especifique a **Lista de exclusão de VM (cadeia de caracteres)**. Este valor é o nome de uma ou mais máquinas virtuais do grupo de recursos de destino. Você pode inserir mais de um nome e separá-los por vírgula (os valores não diferenciam maiúsculas de minúsculas). O uso de caracteres curingas é aceito. Esse valor é armazenado na variável **External_ExcludeVMNames**.
  
   - Selecione um **Agendamento**. Selecione uma data e hora para sua agenda. Uma agenda recorrente diária será criada a partir da hora que você selecionou. A seleção de uma região diferente não está disponível. Para configurar o agendamento de acordo com seu fuso horário específico após a configuração da solução, confira [Modificando o agendamento de inicialização e desligamento](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Para receber **Notificações por email** de um grupo de ações, aceite o valor padrão **Sim** e forneça um endereço de email válido. Se você selecionar **Não**, mas decidir mais tarde que deseja receber notificações por email, atualize o [grupo de ações](../azure-monitor/platform/action-groups.md) que foi criado, com endereços de email válidos separados por vírgula. Você também precisará habilitar as regras de alerta a seguir:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > O valor padrão para **Nomes do ResourceGroup de destino** é um **&ast;**. Isso direciona todas as VMs em uma assinatura. Se você não quiser que a solução direcione todas as VMs em sua assinatura, esse valor precisará ser atualizado para uma lista de nomes de grupos de recursos antes de habilitar os agendamentos.

11. Depois de configurar as definições iniciais necessárias para a solução, clique em **OK** para fechar a página Parâmetros e selecione **Criar**. 

Depois que todas as configurações forem validadas, a solução será implantada em sua assinatura. Esse processo pode levar vários segundos para ser finalizado e você pode acompanhar o progresso em **Notificações** no menu.

> [!NOTE]
> Se você tiver uma assinatura do provedor de soluções na nuvem do Azure (CSP do Azure), após a conclusão da implantação, em sua conta de automação, vá para **variáveis** em **recursos compartilhados** e defina a variável [External_EnableClassicVMs](automation-solution-vm-management.md#variables) como **false**. Isso faz com que a solução pare de procurar recursos de VM clássica.

## <a name="next-steps"></a>Próximas etapas

Agora que a solução está habilitada, você pode [configurá](automation-solution-vm-management-config.md) -la para dar suporte aos seus requisitos de gerenciamento de VM.