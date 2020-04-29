---
title: Gerenciar uma máquina virtual do Azure com a coleta de inventário | Microsoft Docs
description: Gerenciar uma máquina virtual com a coleta de inventário
services: automation
ms.subservice: change-inventory-management
keywords: inventário, automação, alteração, acompanhamento
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617378"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gerenciar uma Máquina Virtual do Azure com a coleta de inventário

Você pode habilitar o acompanhamento de inventário para uma máquina virtual do Azure na página de recursos da máquina virtual. Você pode coletar e exibir as seguintes informações de inventário em seus computadores:

- Software do Windows (aplicativos do Windows e atualizações do Windows), serviços, arquivos e chaves do registro
- Daemons de software (pacotes) do Linux e arquivos

Esse método fornece uma interface do usuário baseada em navegador para definir e configurar a coleta de inventário.

## <a name="before-you-begin"></a>Antes de começar

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Este artigo pressupõe que você tem uma VM para configurar a solução. Se você não tiver uma máquina virtual do Azure, [crie uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Habilitar a coleta de inventário de página de recursos da máquina virtual

1. No Portal do Azure, no painel esquerdo, selecione **Máquinas virtuais**.
2. Na lista de máquinas virtuais, selecione uma máquina virtual.
3. No menu **Recurso**, em **Operações**, selecione **Inventário**.
4. Selecione um espaço de trabalho Log Analytics para armazenar seus logs de dados.
    Se nenhum workspace estiver disponível para essa região, será solicitado que você crie uma conta de automação e um workspace padrão.
5. Para iniciar a integração de seu computador, selecione **Habilitar**.

   ![Exibir opções de integração](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Uma barra de status o notifica de que a solução está sendo habilitada. Esse processo pode levar até 15 minutos. Durante esse período, será possível fechar a janela, ou mantê-la aberta, e receber uma notificação quando a solução estiver habilitada. Você pode monitorar o status da implantação do painel de notificações.

   ![Exibir a solução de inventário imediatamente após a integração](./media/automation-vm-inventory/inventory-onboarded.png)

Após a conclusão da implantação, a barra de status desaparecerá. O sistema ainda está coletando dados de inventário, e talvez os dados ainda não estejam visíveis. Uma coleta de dados completa pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Defina as configurações de inventário

Por padrão, software, serviços do Windows e daemons Linux são configurados para coleta. Para coletar o inventário de arquivos e Registro do Windows, defina as configurações de coleta de inventário.

1. Na página inventário, clique em **Editar configurações** na parte superior da página.
2. Para adicionar uma nova configuração de coleção, vá para a categoria de configuração que você deseja adicionar selecionando a guia **registro do Windows**, **arquivos do Windows**ou **arquivos do Linux** .
3. Selecione a categoria apropriada e clique em **Adicionar** na parte superior da página.

As tabelas a seguir fornecem informações sobre cada propriedade que pode ser configurada para as várias categorias.

### <a name="windows-registry"></a>Registro do Windows

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
|Chave de Registro do Windows   | O caminho para verificar o arquivo. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Arquivos do Windows

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | True se a configuração for aplicada e false caso contrário.        |
|Nome do Item     | O nome amigável do arquivo a ser acompanhado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.       |
|Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Arquivos do Linux

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | True se a configuração for aplicada e false caso contrário.        |
|Nome do Item     | O nome amigável do arquivo a ser acompanhado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **/etc/*. conf**.       |
|Tipo de caminho     | O tipo de item a ser acompanhado. Os valores são arquivo e diretório.        |
|Recursão     | True se a recursão for usada ao procurar o item a ser rastreado; caso contrário, false.        |
|Usar o Sudo     | True se o sudo for usado ao verificar o item; caso contrário, false.         |
|Links     | Valor que indica como os links simbólicos são tratados ao atravessar diretórios. Os valores possíveis são: <br> Ignorar - Ignora os links simbólicos e não inclui os arquivos/diretórios referenciados<br>Seguir - Segue os links simbólicos durante a recursão e inclui também os arquivos/diretórios referenciados<br>Gerenciar - Segue os links simbólicos e permite a alteração do tratamento do conteúdo retornado      |

## <a name="manage-machine-groups"></a>Gerenciar grupos de computadores

O inventário permite criar e exibir grupos de computadores em logs de Azure Monitor. Grupos de computadores são coleções de computadores definidos por uma consulta em logs de Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para exibir seus grupos de computadores, selecione a guia **Grupos de computadores** na página Inventário.

![Exibir grupos de computadores na página de inventário](./media/automation-vm-inventory/inventory-machine-groups.png)

Selecionar um grupo de computadores na lista abre a página de grupos de Computadores. Esta página mostra detalhes sobre o grupo de computadores. Esses detalhes incluem a consulta da análise de logs que é usada para definir o grupo. Na parte inferior da página, há uma lista paginada dos computadores que fazem parte do grupo.

![Página Exibir grupo de computadores](./media/automation-vm-inventory/machine-group-page.png)

Clique em **+ clonar** para clonar o grupo de computadores. Você deve dar ao grupo um novo nome e alias para o grupo. A definição pode ser alterada neste momento. Depois de alterar a consulta, clique em **validar consulta** para visualizar os computadores que seriam selecionados. Quando estiver satisfeito com o grupo, clique em **criar** para criar o grupo de computadores.

Se você quiser criar um novo grupo de computadores, clique em **+ criar um grupo de computadores**. Esse botão abre a página **criar um grupo de computadores** , onde você pode definir seu novo grupo. Clique em **Criar** para criar o grupo.

![Criar novo grupo de computadores](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Desconectar a máquina virtual do gerenciamento

Para remover sua máquina virtual do gerenciamento de inventário:

1. No painel esquerdo do Portal do Azure, selecione **Log Analytics** e, em seguida, selecione o workspace que você usou ao realizar a integração de sua máquina virtual.
2. Na página Log Analytics, abra o menu de **recursos** .
3. Selecione **máquinas virtuais** em **fontes de dados do espaço de trabalho**.
4. Na lista, selecione a máquina virtual que você deseja desconectar. A máquina virtual tem uma marca de seleção verde ao lado de **Este workspace** na coluna **Conexão OMS**.

   >[!NOTE]
   >O OMS (Operations Management Suite) agora é conhecido como logs de Azure Monitor.
   
5. Na parte superior da próxima página, clique em **Desconectar**.
6. Na janela de confirmação, clique em **Sim** para desconectar o computador do gerenciamento.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gerenciamento de alterações em configurações de arquivos e do Registro em suas máquinas virtuais, consulte [Controlar alterações de software em seu ambiente com a solução Controle de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre como gerenciar atualizações de pacote e do Windows para máquinas virtuais, consulte [A solução de Gerenciamento de Atualizações no Azure](../operations-management-suite/oms-solution-update-management.md).