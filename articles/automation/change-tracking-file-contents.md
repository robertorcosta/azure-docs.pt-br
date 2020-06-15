---
title: Controle de Alterações e Inventário na Automação do Azure
description: Este artigo informa como usar o Controle de Alterações e Inventário para controlar as alterações de software e de serviço da Microsoft em seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 4b8bf6a3f583e4c17f61e0a46911990ac5cc827c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830473"
---
# <a name="manage-change-tracking-and-inventory"></a>Gerenciar Controle de Alterações e Inventário

A Automação do Azure habilita o recurso [Controle de Alterações e Inventário](change-tracking.md) para computadores em seu ambiente. O recurso rastreia e disponibiliza alterações em chaves do registro, arquivos, conteúdos e similares. Este artigo inclui procedimentos para trabalhar com esse recurso.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>Habilitar o recurso de Controle de Alterações e Inventário completo

Se você tiver habilitado o [FIM (Monitoramento de Integridade de Arquivo) da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring), você poderá usar o recurso de Controle de Alterações e Inventário completo para seus computadores, conforme descrito abaixo. Suas configurações não são removidas por esse processo.

> [!NOTE]
> Habilitar o recurso de Controle de Alterações e Inventário completo pode causar encargos adicionais. Consulte [Preços da Automação](https://azure.microsoft.com/pricing/details/automation/).

1. Remova a solução de monitoramento navegando até o espaço de trabalho e localizando-a na [lista de soluções de monitoramento instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Clique no nome da solução para abrir sua página de resumo e, em seguida, clique em **Excluir**, conforme detalhado em [Remover uma solução de monitoramento](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Para reabilitar o Controle de Alterações e Inventário, navegue até a Conta de Automação e selecione **Controle de Alterações** ou **Inventário** em **Gerenciamento de Configuração**.
4. Escolha o workspace do Log Analytics e a Conta de Automação, confirme as configurações do workspace e clique em **Habilitar**.

## <a name="enable-machines-for-change-tracking-and-inventory"></a><a name="onboard"></a>Habilitar computadores para Controle de Alterações e Inventário

Para começar a controlar as alterações, você deve habilitar o Controle de Alterações e Inventário na Automação do Azure. Aqui estão as maneiras recomendadas e com suporte para habilitar esse recurso para seus computadores: 

* [Habilitar de uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Habilitar a partir da navegação por várias máquinas](automation-onboard-solutions-from-browse.md)
* [Habilitar a partir de sua Conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Habilitar em um runbook de Automação do Azure](automation-onboard-solutions.md)

## <a name="track-files"></a>Controlar arquivos

### <a name="configure-file-tracking-on-windows"></a>Configurar o controle de arquivos no Windows

Use as etapas a seguir para configurar o controle de arquivos em computadores Windows:

1. Na sua Conta de Automação, selecione **Controle de alterações** em **Gerenciamento de configuração**. 
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Na página Configuração do Workspace, selecione **Arquivos do Windows** e clique em **+ Adicionar** para adicionar um novo arquivo a ser controlado.
4. No painel Adicionar Arquivo do Windows para Controle de Alterações, insira as informações do arquivo a ser controlado e clique em **Salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se a configuração for aplicada e, caso contrário, False.        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
    |Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **c:\temp\\\*. txt**. Você também pode usar variáveis de ambiente, como `%winDir%\System32\\\*.*`.       |
    |Tipo de caminho     | O tipo de caminho. Os valores possíveis são Arquivo e Diretório.        |    
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
    |Links     | Essa configuração determina como os links simbólicos lidam ao percorrer diretórios. Os valores possíveis são:<br> Ignorar – Ignora os links simbólicos e não inclui os arquivos/diretórios referenciados.<br>Seguir – Segue os links simbólicos durante a recursão e também inclui os arquivos/diretórios referenciados.<br>Gerenciar – Segue os links simbólicos e permite a alteração do conteúdo retornado. **Observação** – Essa opção não é recomendada, pois não dá suporte à recuperação de conteúdo de arquivo.    |
    |Carregar conteúdo do arquivo | True para carregar o conteúdo do arquivo em alterações controladas e, caso contrário, False. |

5. Certifique-se de especificar True para **Carregar o conteúdo do arquivo**. Essa configuração habilita o controle de conteúdo do arquivo para o caminho de arquivo indicado.

   ![Adicionar arquivo Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Controlar o conteúdo do arquivo

O controle de conteúdo de arquivo permite que você exiba o conteúdo de um arquivo antes e depois de uma alteração controlada. O recurso salva o conteúdo do arquivo em uma conta de armazenamento após cada alteração. Aqui estão algumas regras a serem seguidas para controlar o conteúdo do arquivo:

* Uma conta de armazenamento Standard usando o modelo de implantação do Resource Manager é necessária para armazenar o conteúdo do arquivo. 

* Não use contas de armazenamento do modelo de implantação Premium e Clássico. Consulte [Sobre as contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md).

* A conta de armazenamento que você usa pode ser conectada a apenas uma conta de Automação.

* O [Controle de Alterações e Inventário](change-tracking.md) está habilitado em sua Conta de Automação.

### <a name="enable-tracking-for-file-content-changes"></a>Habilitar o controle de alterações de conteúdo de arquivo

1. No portal do Azure, abra sua Conta de Automação e, em seguida, selecione **Controle de alterações** em **Gerenciamento de Configuração**.
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Selecione **Conteúdo do Arquivo** e clique em **Link**. Essa seleção abre o painel Adicionar local de conteúdo para o Controle de Alterações.

   ![Habilitar local de conteúdo](./media/change-tracking-file-contents/enable.png)

4. Selecione a assinatura e a conta de armazenamento a serem usadas para armazenar o conteúdo do arquivo. 

5. Se você deseja habilitar o controle de conteúdo de arquivo para todos os arquivos controlados existentes, selecione **Habilitado** para **Carregar conteúdo do arquivo para todas as configurações**. Você pode alterar essa configuração para cada caminho de arquivo mais tarde.

   ![Definir a conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

6. O Controle de Alterações e Inventário mostra os URIs de conta de armazenamento e a SAS (Assinatura de Acesso Compartilhado) quando ele habilita o controle de alterações de conteúdo de arquivo. As assinaturas expiram após 365 dias e você pode recriá-las clicando em **Regenerar**.

   ![Listar chaves de conta](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Exibir o conteúdo de um arquivo rastreado

Quando o Controle de Alterações e o inventário detectar uma alteração de um arquivo rastreado, você pode exibir o conteúdo do arquivo no painel Detalhes da Alteração.  

![Listar alterações](./media/change-tracking-file-contents/change-list.png)

1. No portal do Azure, abra sua Conta de Automação e, em seguida, selecione **Controle de alterações** em **Gerenciamento de Configuração**.

2. Escolha um arquivo na lista de alterações e selecione **Exibir Alterações de Conteúdo do Arquivo** para ver o conteúdo do arquivo. O painel Alterar Detalhes mostra as informações padrão antes e depois do arquivo.

   ![Detalhes da alteração](./media/change-tracking-file-contents/change-details.png)

3. Você está exibindo o conteúdo do arquivo em uma exibição lado a lado. Você pode selecionar **Embutido** para ver uma exibição embutida das alterações.

## <a name="track-registry-keys"></a>Rastrear chaves do registro

Use as etapas a seguir para configurar as chaves do registro para acompanhamento em computadores Windows:

1. Na sua Conta de Automação, selecione **Controle de alterações** em **Gerenciamento de configuração**. 
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Na página Configuração do Workspace, selecione **Registro do Windows**.
4. Clique em **+ Adicionar** para adicionar uma nova chave do registro a ser controlado.
5. No painel Adicionar Registro do Windows para Controle de Alterações, insira as informações para a chave rastrear e clique em **Salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se uma configuração for aplicada e, caso contrário, False.        |
    |Nome do Item     | Nome amigável da chave de registro a ser rastreada.        |
    |Agrupar     | Um nome de grupo para agrupar chaves de registro logicamente.        |
    |Chave de Registro do Windows   | Nome da chave com caminho, por exemplo, **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup**.      |

## <a name="search-logs-for-change-records"></a>Pesquisar registros de alteração nos logs

Você pode fazer várias pesquisas por registros de alteração em relação aos logs do Azure Monitor. Com a página controle de alterações aberta, clique em **Log Analytics** para abrir a página logs. A tabela a seguir fornece pesquisas de logs de exemplo para os registros de alterações.

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationData<br>&#124; em que   ConfigDataType = = "Microsoft Services" e SvcStartupType = = "auto"<br>&#124; onde SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) por SoftwareName, Computer         | Mostra os registros de inventário mais recentes para os serviços da Microsoft que foram configurados para Automático, mas foram relatados como Parados. Os resultados são limitados ao registro mais recente do nome de software e do computador especificados.    |
|ConfigurationChange<br>&#124; onde ConfigChangeType == "Software" e ChangeCategory == "Removed"<br>&#124; ordenar por TimeGenerated desc|Mostra registros de alterações do software removido.|

## <a name="create-alerts-on-changes"></a>Criar alertas sobre as alterações

O exemplo a seguir mostra que o arquivo **C:\Windows\system32\drivers\etc\hosts** foi modificado em um computador. Esse arquivo é importante porque o Windows o usa para resolver nomes de host para endereços IP. Essa operação tem precedência sobre o DNS e pode resultar em problemas de conectividade. Ele também pode levar ao redirecionamento de tráfego para sites mal-intencionados ou perigosos.

![Um gráfico mostrando os hosts de alteração de arquivo](./media/change-tracking-file-contents/changes.png)

Vamos usar este exemplo para discutir as etapas para criar alertas em uma alteração.

1. Na sua Conta de Automação, selecione **Controle de alterações** em **Gerenciamento de Configuração** e, em seguida, selecione **Log Analytics**. 
2. Na pesquisa de logs, procure alterações de conteúdo no arquivo **hosts** com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Essa consulta procura por uma alteração de conteúdo em arquivos com um caminho totalmente qualificado contendo a palavra “hosts”. Você também pode solicitar um arquivo específico alterando a parte do caminho para sua forma totalmente qualificada, por exemplo, usando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Depois que a consulta retornar os resultados desejados, clique em **Nova regra de alerta** na pesquisa de logs para abrir a página de criação de alertas. Você também pode navegar até essa página por meio do **Azure Monitor** no portal do Azure. 

4. Verifique a consulta novamente e modifique a lógica de alerta. Nesse caso, você desejará que o alerta seja disparado se for detectada até mesmo uma alteração entre todos os computadores no ambiente.

    ![Alterar para consulta de controle de alterações no arquivo de hosts](./media/change-tracking-file-contents/change-query.png)

5. Após definir a lógica de alerta, atribua grupos de ação para executar ações em resposta ao alerta que está sendo disparado. Nesse caso, estamos configurando emails a serem enviados e um tíquete de ITSM (Gerenciamento de Serviços de TI) a ser criado. 

    ![Configurando o grupo de ações para alertar sobre uma alteração](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Próximas etapas

* Se você precisar pesquisar logs armazenados em seu workspace do Log Analytics, consulte [Pesquisas de logs em logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md).
* Para solucionar problemas de erros de recurso, consulte [Solucionar problemas do Controle de Alterações e Inventário](troubleshoot/change-tracking.md).