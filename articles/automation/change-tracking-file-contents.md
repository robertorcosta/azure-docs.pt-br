---
title: Gerenciar Controle de Alterações e inventário na automação do Azure
description: Este artigo informa como usar Controle de Alterações e inventário para acompanhar as alterações de software e serviço da Microsoft que ocorrem em seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8ca1bd7a724d3256bc2e171ce39fd6a06e2e5935
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779290"
---
# <a name="manage-change-tracking-and-inventory"></a>Gerenciar Controle de Alterações e Inventário

Quando você adiciona um novo arquivo ou chave do registro para acompanhar, a automação do Azure habilita-o para o recurso de [controle de alterações e inventário](change-tracking.md) . Este artigo inclui procedimentos para trabalhar com esse recurso.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>Habilitar o recurso de Controle de Alterações e inventário completo

Se você tiver habilitado o [fim (monitoramento de integridade de arquivo) da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring), poderá usar o recurso completo de controle de alterações e inventário, conforme descrito abaixo. Suas configurações não são removidas por esse processo.

> [!NOTE]
> Habilitar o recurso de Controle de Alterações e inventário completo pode causar encargos adicionais. Consulte [preços de automação](https://azure.microsoft.com/pricing/details/automation/).

1. Remova a solução de monitoramento navegando até o espaço de trabalho e localizando-a na [lista de soluções de monitoramento instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Clique no nome da solução para abrir sua página de resumo e, em seguida, clique em **excluir**, conforme detalhado em [remover uma solução de monitoramento](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Para reabilitar Controle de Alterações e inventário, navegue até a conta de automação e selecione **controle de alterações** em **Gerenciamento de configuração**.
4. Escolha o espaço de trabalho Log Analytics e a conta de automação, confirme as configurações do espaço de trabalho e clique em **habilitar**.

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>Carregar computadores para Controle de Alterações e inventário

Para começar a controlar as alterações, você deve habilitar Controle de Alterações e inventário na automação do Azure. Aqui estão as maneiras recomendadas e com suporte para carregar seus computadores para esse recurso: 

* [Integração de uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Integração da navegação de vários computadores](automation-onboard-solutions-from-browse.md)
* [Integração da sua conta de automação](automation-onboard-solutions-from-automation-account.md)
* [Integração em um runbook de automação do Azure](automation-onboard-solutions.md)

## <a name="track-files"></a>Rastrear arquivos

### <a name="configure-file-tracking-on-windows"></a>Configurar o rastreamento de arquivos no Windows

Use as etapas a seguir para configurar o rastreamento de arquivos em computadores Windows:

1. Em sua conta de automação, selecione **controle de alterações** em gerenciamento de **configuração**. 
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Na página configuração do espaço de trabalho, selecione **arquivos do Windows**e clique em **+ Adicionar** para adicionar um novo arquivo a ser acompanhado.
4. No painel Adicionar arquivo do Windows para Controle de Alterações, insira as informações do arquivo a ser acompanhado e clique em **salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se a configuração for aplicada e false caso contrário.        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
    |Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **\\\*c:\temp. txt**. Você também pode usar variáveis de ambiente, como `%winDir%\System32\\\*.*`.       |
    |Tipo de caminho     | O tipo de caminho. Os valores possíveis são arquivo e diretório.        |    
    |Recursão     | True se a recursão for usada ao procurar o item a ser rastreado; caso contrário, false.        |    
    |Carregar conteúdo do arquivo | True para carregar o conteúdo do arquivo em alterações controladas; caso contrário, false.|

5. Certifique-se de especificar true para **carregar o conteúdo do arquivo**. Essa configuração habilita o rastreamento de conteúdo de arquivo para o caminho de arquivo indicado.

### <a name="configure-file-tracking-on-linux"></a>Configurar o rastreamento de arquivos no Linux

Use as etapas a seguir para configurar o acompanhamento de arquivo em computadores Linux:

1. Em sua conta de automação, selecione **controle de alterações** em gerenciamento de **configuração**. 
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Na página configuração do espaço de trabalho, selecione **arquivos do Linux**e clique em **+ Adicionar** para adicionar um novo arquivo a ser acompanhado.
4. No painel Adicionar arquivo do Linux para Controle de Alterações, insira as informações para o arquivo ou diretório a ser acompanhado e clique em **salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se a configuração for aplicada e false caso contrário.        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
    |Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **/etc/*. conf**.       |
    |Tipo de caminho     | O tipo de caminho. Os valores possíveis são arquivo e diretório.        |
    |Recursão     | True se a recursão for usada ao procurar o item a ser rastreado; caso contrário, false.        |
    |Usar o Sudo     | True para usar sudo ao verificar o item; caso contrário, false.         |
    |Links     | Configuração que determina como lidar com links simbólicos ao atravessar diretórios. Os valores possíveis são:<br> Ignorar – ignora os links simbólicos e não inclui os arquivos/diretórios referenciados.<br>Seguir-segue os links simbólicos durante a recursão e também inclui os arquivos/diretórios referenciados.<br>Gerenciar - Segue os links simbólicos e permite a alteração do conteúdo retornado. **Observação** -essa opção não é recomendada, pois não dá suporte à recuperação de conteúdo de arquivo.    |
    |Carregar conteúdo do arquivo | True para carregar o conteúdo do arquivo em alterações controladas; caso contrário, false. |

5. Certifique-se de especificar true para **carregar o conteúdo do arquivo**. Essa configuração habilita o rastreamento de conteúdo de arquivo para o caminho de arquivo indicado.

   ![Adicionar arquivo do Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Acompanhar o conteúdo do arquivo

O controle de conteúdo de arquivo permite que você exiba o conteúdo de um arquivo antes e depois de uma alteração controlada. O recurso salva o conteúdo do arquivo em uma conta de armazenamento após a ocorrência de cada alteração. Aqui estão algumas regras a serem seguidas para acompanhar o conteúdo do arquivo:

* Uma conta de armazenamento Standard usando o modelo de implantação do Resource Manager é necessária para armazenar o conteúdo do arquivo. 

* Não use contas de armazenamento do modelo de implantação Premium e clássico. Consulte [sobre as contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

* A conta de armazenamento que você usa pode ser conectada a apenas uma conta de automação.

* O [controle de alterações e o inventário](change-tracking.md) estão habilitados em sua conta de automação.

### <a name="enable-tracking-for-file-content-changes"></a>Habilitar o acompanhamento de alterações de conteúdo de arquivo

1. Na portal do Azure, abra sua conta de automação e, em seguida, selecione **controle de alterações** em **Gerenciamento de configuração**.
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Selecione **Conteúdo do Arquivo** e clique em **Link**. Essa seleção abre o painel Adicionar local de conteúdo para Controle de Alterações.

   ![Habilitar local de conteúdo](./media/change-tracking-file-contents/enable.png)

4. Selecione a assinatura e a conta de armazenamento a serem usadas para armazenar o conteúdo do arquivo. 

5. Se você deseja habilitar o controle de conteúdo de arquivo para todos os arquivos controlados existentes, selecione **Habilitado** para **Carregar conteúdo do arquivo para todas as configurações**. Você pode alterar essa configuração para cada caminho de arquivo posteriormente.

   ![Definir conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

6. Controle de Alterações e inventário mostra URIs de conta de armazenamento e SAS (assinatura de acesso compartilhado) quando ele habilita o controle de alterações de conteúdo de arquivo. As assinaturas expiram após 365 dias e você pode recriá-las clicando em **regenerar**.

   ![Listar chaves de conta](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Exibir o conteúdo de um arquivo rastreado

Uma vez que Controle de Alterações e o inventário detectem uma alteração para um arquivo rastreado, você pode exibir o conteúdo do arquivo no painel de detalhes de alteração.  

![Listar alterações](./media/change-tracking-file-contents/change-list.png)

1. Na portal do Azure, abra sua conta de automação e, em seguida, selecione **controle de alterações** em **Gerenciamento de configuração**.

2. Escolha um arquivo na lista de alterações e selecione **Exibir alterações de conteúdo do arquivo** para ver o conteúdo do arquivo. O painel alterar detalhes mostra as informações padrão antes e depois do arquivo.

   ![Detalhes da alteração](./media/change-tracking-file-contents/change-details.png)

3. Você está exibindo o conteúdo do arquivo em uma exibição lado a lado. Você pode selecionar **embutido** para ver uma exibição em linha das alterações.

## <a name="track-registry-keys"></a>Rastrear chaves do registro

Use as etapas a seguir para configurar as chaves do registro para acompanhamento em computadores Windows:

1. Em sua conta de automação, selecione **controle de alterações** em gerenciamento de **configuração**. 
2. Clique em **Editar Configurações** (o símbolo de engrenagem).
3. Na página configuração do espaço de trabalho, selecione **registro do Windows**.
4. Clique em **+ Adicionar** para adicionar uma nova chave do registro a ser controlada.
5. No painel Adicionar registro do Windows para Controle de Alterações, insira as informações da chave a ser controlada e clique em **salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se uma configuração for aplicada e false caso contrário.        |
    |Nome do Item     | Nome amigável da chave do registro a ser rastreada.        |
    |Agrupar     | Nome do grupo para agrupar logicamente as chaves do registro.        |
    |Chave de Registro do Windows   | Nome da chave com caminho, por exemplo, **HKEY_LOCAL_MACHINE inicialização do Folders\Common do Shell \software\microsoft\windows\currentversion\explorer\user**.      |

## <a name="search-logs-for-change-records"></a>Pesquisar registros de alteração nos logs

Você pode fazer várias pesquisas em relação aos logs de Azure Monitor para registros de alteração. Com a página controle de alterações aberta, clique em **log Analytics** para abrir a página logs. A tabela a seguir fornece pesquisas de log de exemplo para registros de alteração.

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationData<br>&#124; em que ConfigDataType = = "Microsoft Services" e SvcStartupType = = "auto"<br>&#124; onde SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) por SoftwareName, Computer         | Mostra os registros de inventário mais recentes para os serviços da Microsoft que foram definidos como automáticos, mas que foram relatados como sendo interrompidos. Os resultados são limitados ao registro mais recente para o nome de software e o computador especificados.    |
|ConfigurationChange<br>&#124; onde ConfigChangeType == "Software" e ChangeCategory == "Removed"<br>&#124; ordenar por TimeGenerated desc|Mostra registros de alteração do software removido.|

## <a name="create-alerts-on-changes"></a>Criar alertas sobre alterações

O exemplo a seguir mostra que o arquivo **C:\Windows\System32\drivers\etc\hosts** foi modificado em um computador. Esse arquivo é importante porque o Windows o usa para resolver nomes de host para endereços IP. Essa operação tem precedência sobre DNS e pode resultar em problemas de conectividade. Ele também pode levar ao redirecionamento de tráfego para sites mal-intencionados ou perigosos.

![Um gráfico mostrando os hosts de alteração de arquivo](./media/change-tracking-file-contents/changes.png)

Vamos usar este exemplo para discutir as etapas para criar alertas em uma alteração.

1. Em sua conta de automação, selecione **controle de alterações** em gerenciamento de **configuração**e, em seguida, selecione **log Analytics**. 
2. Na pesquisa de logs, procure alterações de conteúdo no arquivo de **hosts** com a `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`consulta. Essa consulta procura uma alteração de conteúdo de arquivos com um caminho totalmente qualificado contendo a palavra "hosts". Você também pode solicitar um arquivo específico alterando a parte do caminho para seu formulário totalmente qualificado, por exemplo, usando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Depois que a consulta retornar os resultados desejados, clique em **nova regra de alerta** na pesquisa de logs para abrir a página de criação de alertas. Você também pode navegar até essa página por meio de **Azure monitor** no portal do Azure. 

4. Verifique a consulta novamente e modifique a lógica de alerta. Nesse caso, você desejará que o alerta seja disparado se for detectada até mesmo uma alteração entre todos os computadores no ambiente.

    ![Alterar para consulta de controle de alterações no arquivo de hosts](./media/change-tracking-file-contents/change-query.png)

5. Depois que a lógica de alerta for definida, atribua grupos de ações para executar ações em resposta ao alerta que está sendo disparado. Nesse caso, estamos Configurando emails a serem enviados e um tíquete de ITSM (gerenciamento de serviços de ti) a ser criado. 

    ![Configurando grupo de ações para alertar sobre alteração](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter noções básicas de Controle de Alterações e inventário, consulte [visão geral de controle de alterações e inventário](change-tracking.md).
* Para solucionar problemas de alterações de uma VM do Azure, consulte [solucionar problemas de controle de alterações e inventário](troubleshoot/change-tracking.md).
* Use [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados detalhados de controle de alterações.