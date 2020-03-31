---
title: Gerenciar pacotes do Python 2 no Automação do Azure
description: Este artigo descreve como gerenciar pacotes do Python 2 na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 05d892edf20cda228bc566b30b0b693ea7c4a184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417639"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerenciar pacotes do Python 2 no Automação do Azure

A Automação do Azure permite executar runbooks do Python 2 no Azure e no Hybrid Runbook Worker do Linux. Para ajudar na simplificação de runbooks, é possível usar pacotes do Python para importar os módulos que forem necessários. Este artigo descreve como você gerencia e usa pacotes do Python na Automação do Azure.

## <a name="import-packages"></a>Importar pacotes

Na sua Conta de Automação, selecione **pacotes do Python 2** em **Recursos Compartilhar**. Clique em **+ Adicionar um pacote do Python 2**.

![Adicionar pacote do Python](media/python-packages/add-python-package.png)

Na página **Adicionar pacote do Python 2**, selecione um pacote local para carregar. O pacote pode ser um arquivo `.whl` ou arquivo `.tar.gz`. Quando selecionado, clique em **OK** para carregar o pacote.

![Adicionar pacote do Python](media/python-packages/upload-package.png)

Uma vez que um pacote tenha sido importado, ele está listado na página **de pacotes Python 2** em sua Conta de Automação. Se for necessário remover um pacote, selecione o pacote e escolha **Excluir**  na página do pacote.

![Lista de pacotes](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importar pacotes com dependências

A automação do Azure não resolve dependências de pacotes python durante o processo de importação. Existem duas maneiras de importar um pacote com todas as suas dependências. Apenas uma das seguintes etapas precisa ser usada para importar os pacotes para sua Conta de Automação.

### <a name="manually-download"></a>Download manual

Em uma máquina com Windows de 64 bits com [python2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) instalados, execute o seguinte comando para baixar um pacote e todas as suas dependências:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Uma vez que os pacotes são baixados, você pode importá-los para sua conta de automação.

### <a name="runbook"></a>Runbook

Importe o runbook python [Importe pacotes Python Python 2 de pipi para a conta azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) da galeria para sua Conta de Automação. Certifique-se de que as configurações de execução estão definidas **como Azure** e inicie o manual com os parâmetros. O manual requer que uma conta execute como a conta de automação funcione. Para cada parâmetro certifique-se de iniciá-lo com o interruptor, como visto na seguinte lista e imagem:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -uma \<conta de automação\>
* -m \<móduloPacote\>

![Lista de pacotes](media/python-packages/import-python-runbook.png)

O runbook permite especificar qual pacote baixar, por exemplo, `Azure` (o quarto parâmetro) baixará todos os módulos do Azure e todas as suas dependências, que é cerca de 105.

Uma vez que o runbook tenha sido concluído, você pode verificar a página **de pacotes Python 2** em Recursos **Compartilhados** em sua Conta de Automação para verificar se o pacote foi importado corretamente.

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um runbook

Depois de importar um pacote, agora você pode usá-lo em um runbook. O exemplo a seguir usa o [ pacote utilitário de Automação do Azure](https://github.com/azureautomation/azure_automation_utility). Esse pacote facilita o uso do Python com a Automação do Azure. Para usar o pacote, siga as instruções no repositório GitHub e adicione-o ao runbook, usando `from azure_automation_utility import get_automation_runas_credential` por exemplo, para importar a função para recuperar a Conta Executar como.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Desenvolva e teste runbooks offline

Para desenvolver e testar os runbooks do Python 2 offline, você pode usar o módulo de [ativos emulados do python de Automação do Azure](https://github.com/azureautomation/python_emulated_assets) no GitHub. Esse módulo permite referenciar os recursos compartilhados como credenciais, variáveis, conexões e certificados.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar os runbooks do Python 2, consulte [Meu primeiro runbook do Python 2](automation-first-runbook-textual-python2.md)
