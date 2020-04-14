---
title: Habilite as VMs start/stop durante a solução de horas de folga
description: Este artigo descreve como ativar a solução Azure Automation Start/Stop VM para suas máquinas virtuais Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 7b619d3c9b4b334e637d6a1c456256cb33ad5134
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261373"
---
# <a name="enable-azure-startstop-vms-solution"></a>Ativar a solução de VMs Azure Start/Stop

Execute as seguintes etapas para adicionar as VMs Start/Stop durante a solução off-hours a uma conta de automação nova ou existente e o espaço de trabalho log analytics vinculado. Após concluir o processo de onboarding, configure as variáveis para personalizar a solução.

>[!NOTE]
>Para usar esta solução com VMs clássicos, você precisa de uma conta Classic Run As, que não é criada por padrão. Para obter instruções sobre como criar uma conta Classic Run As, consulte [Criar uma conta clássica de execução como .](automation-create-standalone-account.md#create-a-classic-run-as-account)
>

## <a name="enable-solution"></a>Habilitar a solução

1. Faça login no [portal](https://portal.azure.com)Azure .

2. Procure e selecione **Contas de Automação**.

3. Na página Contas de **Automação,** selecione sua conta de automação na lista.

4. Na conta de automação, selecione **Start/Stop VM** em **Recursos Relacionados**. A partir daqui, você pode clicar em **Saiba mais sobre e habilite a solução**. Se você já tiver uma solução Iniciar/Parar VM implantada, selecione-a clicando em **Gerenciar a solução** e localizando-a na lista.

   ![Habilitar a conta de automação](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Você também pode criá-lo em qualquer lugar no portal do Azure, clicando em **criar um recurso**. Na página Marketplace, digite uma palavra-chave, como **Iniciar** ou **Iniciar/Parar**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Como alternativa, você pode digitar uma ou mais palavras-chave do nome completo da solução e, em seguida, pressionar Enter. Selecione **Iniciar/Parar VMs fora do horário** nos resultados da pesquisa.

5. Na página **Iniciar/Parar VMs durante as horas de folga** para a solução selecionada, revise as informações de resumo e clique em **Criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

6. A página **Adicionar Solução** é exibida. Você será solicitado a configurar a solução antes de importá-la na sua assinatura da Automação.

   ![Página Adicionar Solução de Gerenciamento de VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Na página **Adicionar Solução**, selecione **Workspace**. Selecione um espaço de trabalho do Log Analytics que esteja vinculada à mesma assinatura do Azure na qual a conta de Automação está. Se você não tiver um workspace, selecione **Criar Novo Workspace**. Na página do **espaço de trabalho do Log Analytics,** execute as seguintes etapas:

   - Especifique um nome para o novo **espaço de trabalho do Log Analytics,** como "ContosoLAWorkspace".
   - Selecione uma **Assinatura** à qual se vincular, escolhendo na lista suspensa, caso a assinatura selecionada por padrão não seja adequada.
   - Em **Grupo de Recursos**, você pode criar um novo grupo de recursos ou selecionar um existente.
   - Selecione um **local**.
   - Selecione um **nível de preços .** Escolha a opção **Por GB (autônomo)**. Os registros do Monitor do Azure têm [preços](https://azure.microsoft.com/pricing/details/log-analytics/) atualizados e o nível Por GB é a única opção.

   > [!NOTE]
   > Ao habilitar soluções, somente determinadas regiões têm suporte para vincular um espaço de trabalho do Log Analytics e uma Conta de Automação.
   >
   > Para obter uma lista dos pares de mapeamento suportados, consulte [Mapeamento de região para o espaço de trabalho De conta de automação e log analytics](how-to/region-mappings.md).

8. Depois de fornecer as informações necessárias na página **Espaço de Trabalho do Log Analytics**, clique em **Criar**. Você pode acompanhar o progresso em **Notificações** no menu, que retornará a página **Adicionar Solução** ao terminar.

9. Na página **Adicionar Solução**, selecione **Conta de automação**. Se você estiver criando um novo espaço de trabalho do Log Analytics, poderá criar uma nova Conta de automação para associá-la ou selecionar uma conta de automação existente que ainda não esteja vinculada a um espaço de trabalho do Log Analytics. Selecione uma conta de automação existente ou clique em **Criar uma conta de automação** e, na página **Adicionar automação da conta**, forneça as seguintes informações:
 
   - No campo **Nome**, digite o nome da conta de Automação.

     Todas as outras opções são preenchidas automaticamente com base no espaço de trabalho do Log Analytics selecionado. Essas opções não podem ser modificadas. Uma conta Executar como do Azure é o método de autenticação padrão para os runbooks incluídos nesta solução. Depois de clicar em **OK**, as opções de configuração serão validadas e a conta de Automação será criada. Você pode acompanhar o progresso em **Notificações** no menu.

10. Por fim, na página **Adicionar Solução**, selecione **Configuração**. A página **Parâmetros** é exibida.

    ![Página de parâmetros para a solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Aqui, você será solicitado a:
  
   - Especificar os **Nomes do ResourceGroup de destino**. Esses valores são nomes de grupos de recursos que contêm VMs a serem gerenciadas por essa solução. Você pode inserir mais de um nome e separá-los por vírgula (os valores não diferenciam maiúsculas de minúsculas). O uso de um caractere curinga tem suporte para selecionar VMs em todos os grupos de recursos na assinatura. Esse valor é armazenado nas variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**.
  
   - Especifique a **Lista de exclusão de VM (cadeia de caracteres)**. Este valor é o nome de uma ou mais máquinas virtuais do grupo de recursos de destino. Você pode inserir mais de um nome e separá-los por vírgula (os valores não diferenciam maiúsculas de minúsculas). O uso de caracteres curingas é aceito. Esse valor é armazenado na variável **External_ExcludeVMNames**.
  
   - Selecione um **Agendamento**. Selecione uma data e hora para sua agenda. Uma programação diária recorrente será criada a partir do tempo selecionado. A seleção de uma região diferente não está disponível. Para configurar o agendamento de acordo com seu fuso horário específico após a configuração da solução, confira [Modificando o agendamento de inicialização e desligamento](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Para receber **Notificações por email** de um grupo de ações, aceite o valor padrão **Sim** e forneça um endereço de email válido. Se você selecionar **Não**, mas decidir mais tarde que deseja receber notificações por email, atualize o [grupo de ações](../azure-monitor/platform/action-groups.md) que foi criado, com endereços de email válidos separados por vírgula. Você também precisará habilitar as regras de alerta a seguir:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > O valor padrão para **Nomes do ResourceGroup de destino** é um **&ast;**. Isso direciona todas as VMs em uma assinatura. Se você não quiser que a solução direcione todas as VMs em sua assinatura, esse valor precisará ser atualizado para uma lista de nomes de grupos de recursos antes de habilitar os agendamentos.

11. Depois de configurar as definições iniciais necessárias para a solução, clique em **OK** para fechar a página **Parâmetros** e selecione **Criar**. 

Depois que todas as configurações forem validadas, a solução será implantada em sua assinatura. Esse processo pode levar vários segundos para ser finalizado e você pode acompanhar o progresso em **Notificações** no menu.

> [!NOTE]
> Se você tiver uma assinatura do Azure Cloud Solution Provider (Azure CSP), após a implantação estiver concluída, em sua Conta de Automação, vá para Variáveis em **Recursos Compartilhados** e defina a variável [**External_EnableClassicVMs**](automation-solution-vm-management.md#variables) como **Falsa**. **Variables** Isso faz com que a solução pare de procurar recursos de VM clássica.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem a solução [configure](automation-solution-vm-management-config.md) ativada, você pode configurá-la para suportar seus requisitos de gerenciamento de VM.