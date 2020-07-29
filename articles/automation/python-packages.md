---
title: Gerenciar pacotes do Python 2 no Automação do Azure
description: Este artigo informa como gerenciar pacotes do Python 2 na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.custom: tracking-python
ms.openlocfilehash: f3ba52c1396928d8c76fb85fda3f29c625e60919
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84561861"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerenciar pacotes do Python 2 no Automação do Azure

A Automação do Azure permite executar runbooks do Python 2 no Azure e no Hybrid Runbook Worker do Linux. Para ajudar na simplificação de runbooks, é possível usar pacotes do Python para importar os módulos que forem necessários. Este artigo descreve como gerenciar e usar pacotes do Python na Automação do Azure.

## <a name="import-packages"></a>Importar pacotes

Na sua conta de Automação, selecione **Pacotes do Python 2** em **Recursos Compartilhados**. Clique em **+ Adicionar um pacote do Python 2**.

![Adicionar pacote do Python](media/python-packages/add-python-package.png)

Na página Adicionar Pacote Python 2, selecione um pacote local para carregar. O pacote pode ser um arquivo **.whl** ou **.tar.gz**. Quando o pacote for selecionado, clique em **OK** para carregá-lo.

![Adicionar pacote do Python](media/python-packages/upload-package.png)

Depois que um pacote tiver sido importado, ele será listado na página de pacotes do Python 2 na sua conta de Automação. Se você precisar remover um pacote, selecione o pacote e clique em **Excluir**.

![Lista de pacotes](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importar pacotes com dependências

A Automação do Azure não resolve dependências de pacotes Python durante o processo de importação. Há duas maneiras de importar um pacote com todas as dependências. Somente uma das etapas a seguir precisa ser adotada a fim de importar os pacotes para sua conta de Automação.

### <a name="manually-download"></a>Baixar manualmente

Em um computador com Windows de 64 bits com [Python 2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) instalados, execute o seguinte comando para baixar um pacote e todas as suas dependências:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Depois que os pacotes forem baixados, você poderá importá-los para sua conta de automação.

### <a name="runbook"></a>Runbook

 Para obter um runbook, [importe os pacotes Python 2 do pypi para a conta de Automação do Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509), da galeria para sua conta de Automação. Verifique se as configurações de execução estão definidas como **Azure** e inicie o runbook com os parâmetros. O runbook requer uma conta Executar como para que a conta de Automação funcione. Para cada parâmetro, certifique-se de iniciá-lo com a opção conforme mostrado na seguinte lista e imagem:

* -s\<subscriptionId\>
* -g\<resourceGroup\>
* -a\<automationAccount\>
* -m\<modulePackage\>

![Lista de pacotes](media/python-packages/import-python-runbook.png)

O runbook permite que você especifique qual pacote baixar. Por exemplo, o uso do parâmetro `Azure` baixa todos os módulos do Azure e todas as dependências (cerca de 105).

Depois que o runbook for concluído, você poderá verificar os **Pacotes Python 2** em **Recursos Compartilhados** na sua conta de Automação para verificar se o pacote foi importado corretamente.

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um runbook

Com um pacote importado, você pode usá-lo em um runbook. O exemplo a seguir usa o [ pacote utilitário de Automação do Azure](https://github.com/azureautomation/azure_automation_utility). Esse pacote facilita o uso do Python com a Automação do Azure. Para usar o pacote, siga as instruções no repositório GitHub e adicione-o ao runbook. Por exemplo, você pode usar `from azure_automation_utility import get_automation_runas_credential` a fim de importar a função para recuperar a conta Executar como.

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

Para desenvolver e testar os runbooks do Python 2 offline, você pode usar o módulo [Ativos emulados do Python da Automação do Azure](https://github.com/azureautomation/python_emulated_assets) no GitHub. Esse módulo permite referenciar os recursos compartilhados como credenciais, variáveis, conexões e certificados.

## <a name="next-steps"></a>Próximas etapas

Para preparar um runbook do Python, confira [Criar um runbook do Python](learn/automation-tutorial-runbook-textual-python2.md).
