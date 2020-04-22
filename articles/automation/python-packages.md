---
title: Gerenciar pacotes do Python 2 no Automação do Azure
description: Este artigo descreve como gerenciar pacotes do Python 2 na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 9f52dfd92d430abffe5857d231898dd4b0e7745e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679915"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerenciar pacotes do Python 2 no Automação do Azure

A Automação do Azure permite executar runbooks do Python 2 no Azure e no Hybrid Runbook Worker do Linux. Para ajudar na simplificação de runbooks, é possível usar pacotes do Python para importar os módulos que forem necessários. Este artigo descreve como gerenciar e usar pacotes Python na Automação Azure.

## <a name="import-packages"></a>Importar pacotes

Em sua conta de Automação, selecione **pacotes Python 2** em Recursos **Compartilhados**. Clique em **+ Adicionar um pacote do Python 2**.

![Adicionar pacote do Python](media/python-packages/add-python-package.png)

Na página Adicionar pacote do Python 2, selecione um pacote local para carregar. O pacote pode ser um arquivo **.whl** ou **.tar.gz.** Quando o pacote estiver selecionado, clique em **OK** para carregá-lo.

![Adicionar pacote do Python](media/python-packages/upload-package.png)

Uma vez que um pacote tenha sido importado, ele está listado na página de pacotes Python 2 em sua conta de Automação. Se você precisar remover um pacote, selecione o pacote e clique em **Excluir**.

![Lista de pacotes](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importar pacotes com dependências

A automação do Azure não resolve dependências de pacotes python durante o processo de importação. Existem duas maneiras de importar um pacote com todas as suas dependências. Apenas uma das seguintes etapas precisa ser usada para importar os pacotes para sua conta de Automação.

### <a name="manually-download"></a>Download manual

Em uma máquina com Windows de 64 bits com [python2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) instalados, execute o seguinte comando para baixar um pacote e todas as suas dependências:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Uma vez que os pacotes são baixados, você pode importá-los para sua conta de automação.

### <a name="runbook"></a>Runbook

Importe o runbook python [Importe pacotes Python Python 2 de pipi para a conta azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) da galeria para sua conta de Automação. Certifique-se de que as configurações de execução estão definidas **como Azure** e inicie o manual com os parâmetros. O manual requer uma conta Run As para que a conta automação funcione. Para cada parâmetro certifique-se de iniciá-lo com o interruptor, como visto na seguinte lista e imagem:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -uma \<conta de automação\>
* -m \<móduloPacote\>

![Lista de pacotes](media/python-packages/import-python-runbook.png)

O runbook permite especificar qual pacote baixar. Por exemplo, o `Azure` uso do parâmetro baixa todos os módulos Do Zure e todas as dependências (cerca de 105).

Uma vez que o runbook esteja concluído, você pode verificar os **pacotes Python 2** em Recursos **Compartilhados** em sua conta de Automação para verificar se o pacote foi importado corretamente.

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um runbook

Com um pacote importado, você pode usá-lo em um runbook. O exemplo a seguir usa o [pacote utilitário Azure Automation](https://github.com/azureautomation/azure_automation_utility). Esse pacote facilita o uso do Python com a Automação do Azure. Para usar o pacote, siga as instruções no repositório do GitHub e adicione-o ao manual. Por exemplo, você `from azure_automation_utility import get_automation_runas_credential` pode usar para importar a função para recuperar a conta Run As.

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

Para começar com os runbooks do Python 2, consulte [Meu primeiro runbook Python 2](automation-first-runbook-textual-python2.md).
