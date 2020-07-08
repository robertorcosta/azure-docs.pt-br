---
title: Controle de Alterações e Inventário na Automação do Azure
description: Este artigo informa como usar o Controle de Alterações e Inventário para controlar as alterações de software e de serviço da Microsoft em seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: 0eebd626013614bb6240fc0e6530a358a2b86d1c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781184"
---
# <a name="manage-change-tracking-and-inventory"></a>Gerenciar Controle de Alterações e Inventário

Quando você adiciona um novo arquivo ou chave do registro para acompanhar, a automação do Azure permite que ele [controle de alterações e inventário](change-tracking.md). Este artigo descreve como configurar o controle, examinar os resultados do controle e lidar com alertas quando alterações forem detectadas.

Antes de usar os procedimentos neste artigo, verifique se você habilitou Controle de Alterações e inventário em suas VMs usando uma destas técnicas:

* [Habilitar Controle de Alterações e Inventário de uma conta de Automação](automation-enable-changes-from-auto-acct.md)
* [Habilite Controle de Alterações e inventário navegando na portal do Azure](automation-enable-changes-from-browse.md)
* [Habilitar Controle de Alterações e Inventário de um runbook](automation-enable-changes-from-runbook.md)
* [Habilitar Controle de Alterações e Inventário de uma VM do Azure](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o escopo para a implantação

O Controle de Alterações e Inventário usa uma configuração de escopo dentro do workspace a fim de definir os computadores que receberão alterações. Para obter mais informações, consulte [limitar controle de alterações e escopo de implantação de estoque](automation-scope-configurations-change-tracking.md).

## <a name="track-files"></a>Controlar arquivos

Você pode usar Controle de Alterações e inventário para controlar alterações em arquivos e pastas/diretórios. Esta seção informa como configurar o rastreamento de arquivos no Windows e no Linux.

### <a name="configure-file-tracking-on-windows"></a>Configurar o controle de arquivos no Windows

Use as etapas a seguir para configurar o controle de arquivos em computadores Windows:

1. Na sua Conta de Automação, selecione **Controle de alterações** em **Gerenciamento de configuração**. 
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Na página Configuração do Workspace, selecione **Arquivos do Windows** e clique em **+ Adicionar** para adicionar um novo arquivo a ser controlado.
4. No painel Adicionar arquivo do Windows para Controle de Alterações, insira as informações do arquivo ou da pasta a ser rastreada e clique em **salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se a configuração for aplicada e, caso contrário, False.        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
    |Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **c:\temp\\\*. txt**. Você também pode usar variáveis de ambiente, como `%winDir%\System32\\\*.*`.       |
    |Tipo de caminho     | O tipo de caminho. Os valores possíveis são arquivo e pasta.        |    
    |Recursão     | True se a recursão for usada ao procurar o item a ser rastreado e, caso contrário, False.        |    
    |Carregar conteúdo do arquivo | True para carregar o conteúdo do arquivo em alterações controladas e, caso contrário, False.|

5. Certifique-se de especificar True para **Carregar o conteúdo do arquivo**. Essa configuração habilita o controle de conteúdo do arquivo para o caminho de arquivo indicado.

### <a name="configure-file-tracking-on-linux"></a>Configurar o controle de arquivos no Linux

Use as etapas a seguir para configurar o acompanhamento de arquivo em computadores Linux:

1. Na sua Conta de Automação, selecione **Controle de alterações** em **Gerenciamento de configuração**. 
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Na página Configuração do Workspace, selecione **Arquivos do Linux** e clique em **+ Adicionar** para adicionar um novo arquivo a ser controlado.
4. No painel Adicionar o Arquivo do Linux para o Controle de Alterações, insira as informações para o arquivo ou para o diretório a ser controlado e clique em **Salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se a configuração for aplicada e, caso contrário, False.        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
    |Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **/etc/*.conf**.       |
    |Tipo de caminho     | O tipo de caminho. Os valores possíveis são Arquivo e Diretório.        |
    |Recursão     | True se a recursão for usada ao procurar o item a ser rastreado e, caso contrário, False.        |
    |Usar o Sudo     | True para usar sudo ao verificar o item e, caso contrário, False.         |
    |Links     | Essa configuração determina como os links simbólicos lidam ao percorrer diretórios. Os valores possíveis são:<br> Ignorar – Ignora os links simbólicos e não inclui os arquivos/diretórios referenciados.<br>Seguir – Segue os links simbólicos durante a recursão e também inclui os arquivos/diretórios referenciados.<br>Gerenciar-segue os links simbólicos e permite a alteração de conteúdo retornado.<br>**Observação:** A opção Manage não é recomendada, pois não dá suporte à recuperação de conteúdo do arquivo.    |
    |Carregar conteúdo do arquivo | True para carregar o conteúdo do arquivo em alterações controladas e, caso contrário, False. |

5. Certifique-se de especificar True para **Carregar o conteúdo do arquivo**. Essa configuração habilita o controle de conteúdo do arquivo para o caminho de arquivo indicado.

   ![Adicionar arquivo Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Controlar o conteúdo do arquivo

O controle de conteúdo de arquivo permite que você exiba o conteúdo de um arquivo antes e depois de uma alteração controlada. O recurso salva o conteúdo do arquivo em uma [conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-overview) após a ocorrência de cada alteração. Aqui estão algumas regras a serem seguidas para controlar o conteúdo do arquivo:

* Uma conta de armazenamento Standard usando o modelo de implantação do Resource Manager é necessária para armazenar o conteúdo do arquivo. 
* Não use contas de armazenamento do modelo de implantação Premium e Clássico. Consulte [Sobre as contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* Você pode conectar a conta de armazenamento a apenas uma conta de automação.
* [Controle de alterações e inventário](change-tracking.md) devem ser habilitados em sua conta de automação.

### <a name="enable-tracking-for-file-content-changes"></a>Habilitar o controle de alterações de conteúdo de arquivo

Use as etapas a seguir para habilitar o rastreamento de alterações no conteúdo do arquivo:

1. No portal do Azure, abra sua Conta de Automação e, em seguida, selecione **Controle de alterações** em **Gerenciamento de Configuração**.
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Selecione **Conteúdo do Arquivo** e clique em **Link**. Essa seleção abre o painel Adicionar local de conteúdo para o Controle de Alterações.

   ![Adicionar local de conteúdo](./media/change-tracking-file-contents/enable.png)

4. Selecione a assinatura e a conta de armazenamento a serem usadas para armazenar o conteúdo do arquivo. 

5. Se você deseja habilitar o controle de conteúdo de arquivo para todos os arquivos controlados existentes, selecione **Habilitado** para **Carregar conteúdo do arquivo para todas as configurações**. Você pode alterar essa configuração para cada caminho de arquivo mais tarde.

   ![Definir a conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

6. O Controle de Alterações e Inventário mostra os URIs de conta de armazenamento e a SAS (Assinatura de Acesso Compartilhado) quando ele habilita o controle de alterações de conteúdo de arquivo. As assinaturas expiram após 365 dias e você pode recriá-las clicando em **Regenerar**.

   ![Listar chaves de conta](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Exibir o conteúdo de um arquivo rastreado

Quando o Controle de Alterações e o inventário detectar uma alteração de um arquivo rastreado, você pode exibir o conteúdo do arquivo no painel Detalhes da Alteração.  

![Listar alterações](./media/change-tracking-file-contents/change-list.png)

1. No portal do Azure, abra sua Conta de Automação e, em seguida, selecione **Controle de alterações** em **Gerenciamento de Configuração**.

2. Escolha um arquivo na lista de alterações e selecione **Exibir Alterações de Conteúdo do Arquivo** para ver o conteúdo do arquivo. O painel alterar detalhes mostra as informações padrão antes e depois do arquivo para cada propriedade.

   ![Detalhes da alteração](./media/change-tracking-file-contents/change-details.png)

3. Você está exibindo o conteúdo do arquivo em uma exibição lado a lado. Você pode selecionar **Embutido** para ver uma exibição embutida das alterações.

## <a name="track-registry-keys"></a>Rastrear chaves do registro

Use as etapas a seguir para configurar as chaves do registro para acompanhamento em computadores Windows:

1. No portal do Azure, abra sua Conta de Automação e, em seguida, selecione **Controle de alterações** em **Gerenciamento de Configuração**. 
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Na página Configuração do Workspace, selecione **Registro do Windows**.
4. Clique em **+ Adicionar** para adicionar uma nova chave do registro a ser controlado.
5. No painel Adicionar Registro do Windows para Controle de Alterações, insira as informações para a chave rastrear e clique em **Salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se uma configuração for aplicada e, caso contrário, False.        |
    |Nome do Item     | Nome amigável da chave de registro a ser rastreada.        |
    |Agrupar     | Um nome de grupo para agrupar chaves de registro logicamente.        |
    |Chave de Registro do Windows   | Nome da chave com caminho, por exemplo, `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Pesquisar registros de alteração nos logs

Você pode fazer várias pesquisas por registros de alteração em relação aos logs do Azure Monitor. Com a página controle de alterações aberta, clique em **Log Analytics** para abrir a página logs. A tabela a seguir fornece pesquisas de logs de exemplo para os registros de alterações.

|Consulta  |Descrição  |
|---------|---------|
|`ConfigurationData`<br>&#124;`where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124;`where SvcState == "Stopped"`<br>&#124;`summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Mostra os registros de inventário mais recentes para os serviços da Microsoft que foram configurados para Automático, mas foram relatados como Parados. Os resultados são limitados ao registro mais recente do nome de software e do computador especificados.    |
|`ConfigurationChange`<br>&#124;`where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124;`order by TimeGenerated desc`|Mostra registros de alterações do software removido.|

## <a name="create-alerts-on-changes"></a>Criar alertas sobre as alterações

O exemplo a seguir mostra que o arquivo **c:\Windows\System32\drivers\etc\hosts** foi modificado em um computador. Esse arquivo é importante porque o Windows o usa para resolver nomes de host para endereços IP. Essa operação tem precedência sobre o DNS e pode resultar em problemas de conectividade. Ele também pode levar ao redirecionamento de tráfego para sites mal-intencionados ou perigosos.

![Gráfico mostrando a alteração do arquivo de hosts](./media/change-tracking-file-contents/changes.png)

Vamos usar este exemplo para discutir as etapas para criar alertas em uma alteração.

1. Na sua Conta de Automação, selecione **Controle de alterações** em **Gerenciamento de Configuração** e, em seguida, selecione **Log Analytics**. 
2. Na pesquisa de logs, procure alterações de conteúdo no arquivo **hosts** com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Essa consulta procura alterações de conteúdo de arquivos com nomes de caminho totalmente qualificados contendo a palavra `hosts` . Você também pode solicitar um arquivo específico alterando a parte do caminho para sua forma totalmente qualificada, por exemplo, usando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Depois que a consulta retornar seus resultados, clique em **nova regra de alerta** na pesquisa de logs para abrir a página de criação de alertas. Você também pode navegar até essa página por meio do **Azure Monitor** no portal do Azure. 

4. Verifique a consulta novamente e modifique a lógica de alerta. Nesse caso, você desejará que o alerta seja disparado se for detectada até mesmo uma alteração entre todos os computadores no ambiente.

    ![Alterar para consulta de controle de alterações no arquivo de hosts](./media/change-tracking-file-contents/change-query.png)

5. Depois que a lógica de alerta for definida, atribua grupos de ações para executar ações em resposta ao disparo do alerta. Nesse caso, estamos configurando emails a serem enviados e um tíquete de ITSM (Gerenciamento de Serviços de TI) a ser criado. 

    ![Configurando o grupo de ações para alertar sobre uma alteração](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre configurações de escopo, consulte [limitar controle de alterações e escopo de implantação de estoque](automation-scope-configurations-change-tracking.md).
* Se você precisar pesquisar logs armazenados em seu workspace do Log Analytics, confira [Pesquisas de logs em logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md).
* Se terminar com implantações, consulte [desvincular o espaço de trabalho da conta de automação para controle de alterações e inventário](automation-unlink-workspace-change-tracking.md).
* Para excluir suas VMs de Controle de Alterações e inventário, consulte [remover VMs de controle de alterações e inventário](automation-remove-vms-from-change-tracking.md).
* Para solucionar problemas de erros de recurso, confira [Solucionar problemas do Controle de Alterações e Inventário](troubleshoot/change-tracking.md).
