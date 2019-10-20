---
title: Meu primeiro runbook do Python na automação do Azure
description: Tutorial que orienta você durante a criação, o teste e a publicação de um runbook simples do Python.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 626f446c18acf1f07f458fb1b4238f182546e479
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596941"
---
# <a name="my-first-python-runbook"></a>Meu primeiro runbook do Python

> [!div class="op_single_selector"]
> - [Gráfico](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Este tutorial orienta você durante a criação de um [runbook do Python](automation-runbook-types.md#python-runbooks) na automação do Azure. Você começa com um runbook simples que você testa e publica. Em seguida, você modifica o runbook para realmente gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure. Por fim, você torna o runbook mais robusto adicionando parâmetros de runbook.

> [!NOTE]
> Não há suporte para o uso de um webhook para iniciar um runbook do Python.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

- do Microsoft Azure. Se você ainda não tiver um, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure. Essa conta deve ter permissão para iniciar e parar a máquina virtual.
- Uma máquina virtual do Azure. Pare e inicie esse computador para que ele não seja uma VM de produção.

## <a name="create-a-new-runbook"></a>Criar um novo runbook

Você começa criando um runbook simples que gera o texto *Olá, mundo*.

1. Na portal do Azure, abra sua conta de automação.

    A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos são os módulos incluídos automaticamente em uma nova conta de automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).<br>

1. Selecione **runbooks** em **Gerenciamento de processo** para abrir a lista de runbooks.
1. Selecione **+ Adicionar um runbook** para criar um novo runbook.
1. Dê ao runbook o nome *MyFirstRunbook-Python*.
1. Nesse caso, você criará um [runbook do Python](automation-runbook-types.md#python-runbooks) , portanto, selecione **Python 2** para **tipo de runbook**.
1. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Agora, você adiciona um comando simples para imprimir o texto "Olá, Mundo":

```python
print("Hello World!")
```

Clique em **Salvar** para salvar o runbook.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, você deseja testá-lo para certificar-se de que ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1. Clique em **Painel de teste** para abrir o Painel de teste.
1. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
1. Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido.
   O status do trabalho começa como *na fila* , indicando que está aguardando que um runbook Worker na nuvem fique disponível. Ele é movido para *iniciando* quando um trabalho alega o trabalho e, em seguida, é *executado* quando o runbook realmente começa a ser executado.
1. Quando o trabalho do runbook é concluído, sua saída é exibida. Nesse caso, você deve ver *Olá, mundo*.
1. Feche o painel de teste para retornar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook que você criou ainda está no modo de rascunho. Você precisa publicá-lo antes de poder executá-lo em produção.
Ao publicar um runbook, você substitui a versão publicada existente pela versão de rascunho.
Nesse caso, você ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks** agora, ele mostrará um **status de criação** **publicado**.
1. Role de volta à direita para exibir o painel para **MyFirstRunbook-Python**.
   As opções na parte superior nos permitem iniciar o runbook, exibir o runbook ou agendá-lo para iniciar em algum momento no futuro.
2. Você deseja iniciar o runbook, portanto, clique em **Iniciar** e em **OK** quando a folha iniciar runbook for aberta.
3. Um painel de trabalho é aberto para o trabalho de runbook que você criou. Você pode fechar esse painel, mas, nesse caso, deixá-lo aberto para que você possa observar o progresso do trabalho.
1. O status do trabalho é mostrado no **Resumo do trabalho** e corresponde aos status que você viu quando testou o runbook.
2. Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. O painel saída é aberto e você pode ver sua *Olá, mundo*.
3. Feche o painel Saída.
4. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só deve ver *Olá, mundo* no fluxo de saída, mas isso pode mostrar outros fluxos para um trabalho de runbook, como detalhado e erro se o runbook gravar neles.
5. Feche o painel fluxos e o painel trabalho para retornar ao painel MyFirstRunbook-Python.
6. Clique em **Trabalhos** para abrir o painel de trabalhos para este runbook. Isso lista todos os trabalhos criados por esse runbook. Você só deve ver um trabalho listado, pois você executou o trabalho apenas uma vez.
7. Você pode clicar nesse trabalho para abrir o mesmo painel de trabalho que você exibiu quando iniciou o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerenciar recursos do Azure

Você testou e publicou seu runbook, mas até o momento ele não faz nada útil. Você deseja que ele gerencie os recursos do Azure.
Para gerenciar recursos do Azure, o script precisa se autenticar usando as credenciais de sua conta de automação. Para ajudá-lo, você pode usar o [pacote do utilitário de automação do Azure](https://github.com/azureautomation/azure_automation_utility) para facilitar a autenticação e a interação com os recursos do Azure.

> [!NOTE]
> A conta de automação deve ter sido criada com o recurso de entidade de serviço para que haja um certificado executar como.
> Se sua conta de automação não tiver sido criada com a entidade de serviço, você poderá autenticar usando o método descrito em [autenticar com as bibliotecas de gerenciamento do Azure para Python](/azure/python/python-sdk-azure-authenticate).

1. Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-Python.

2. Adicione o seguinte código para autenticar no Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adicionar código para criar o cliente de computação do Python e iniciar a VM

Para trabalhar com VMs do Azure, crie uma instância do [cliente de computação do Azure para Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Use o cliente de computação para iniciar a VM. Adicione o seguinte código ao runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Em que _MyResource_ Group é o nome do grupo de recursos que contém a VM e _TestVM_ é o nome da VM que você deseja iniciar.

Teste e execute o runbook novamente para ver que ele inicia a VM.

## <a name="use-input-parameters"></a>Usar parâmetros de entrada

Atualmente, o runbook usa valores embutidos em código para os nomes do grupo de recursos e da VM.
Agora, vamos adicionar o código que obtém esses valores dos parâmetros de entrada.

Você usa a variável `sys.argv` para obter os valores de parâmetro.
Adicione o seguinte código ao runbook imediatamente após as outras instruções `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Isso importa o módulo `sys` e cria duas variáveis para armazenar os nomes do grupo de recursos e da VM.
Observe que o elemento da lista de argumentos, `sys.argv[0]`, é o nome do script e não é inserido pelo usuário.

Agora você pode modificar as duas últimas linhas do runbook para usar os valores de parâmetro de entrada em vez de usar valores embutidos em código:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Ao iniciar um runbook do Python (na página de **teste** ou como um runbook publicado), você pode inserir os valores dos parâmetros na página **Iniciar runbook** em **parâmetros**.

Depois que você começar a inserir um valor na primeira caixa, um segundo será exibido, e assim por diante, para que você possa inserir quantos valores de parâmetro forem necessários.

Os valores estão disponíveis para o script como a matriz de `sys.argv` como no código que você acabou de adicionar.

Insira o nome do seu grupo de recursos como o valor do primeiro parâmetro e o nome da VM para iniciar como o valor do segundo parâmetro.

![Inserir valores de parâmetro](media/automation-first-runbook-textual-python/runbook-python-params.png)

Clique em **OK** para iniciar o runbook. O runbook é executado e inicia a VM que você especificou.

## <a name="error-handling-in-python"></a>Tratamento de erros no Python

Você também pode usar as seguintes convenções para recuperar vários fluxos de seus runbooks do Python, incluindo fluxos de **aviso**, **erro**e **depuração** .

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

O exemplo a seguir mostra essa convenção usada em um bloco de `try...except`.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Não há suporte para **Sys. stderr** na automação do Azure.

## <a name="next-steps"></a>Próximos passos

- Para começar a usar os runbooks do PowerShell, consulte [meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
- Para começar a usar runbooks gráficos, consulte [meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
- Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
- Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
- Para saber mais sobre como desenvolver para o Azure com Python, confira [Azure para desenvolvedores de Python](/azure/python/)
- Para exibir os runbooks de exemplo do Python 2, consulte o [GitHub de automação do Azure](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
