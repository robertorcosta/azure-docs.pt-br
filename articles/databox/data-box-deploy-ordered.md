---
title: Tutorial para pedido do Azure Data Box | Microsoft Docs
description: Conheça os pré-requisitos de implantação e como solicitar um Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392465"
---
# <a name="tutorial-order-azure-data-box"></a>Tutorial: Solicitar o Azure Data Box

O Azure Data Box é uma solução híbrida que permite importar dados do local no Azure de maneira rápida, fácil e confiável. Você transfere os dados para um dispositivo de armazenamento de 80 TB fornecido pela Microsoft (capacidade utilizável) e, depois, devolve o dispositivo. Após, esses dados são carregados no Azure.

Este tutorial descreve como você pode solicitar um Azure Data Box. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos para implantar o Data Box
> * Solicitar um Data Box
> * Acompanhar o pedido
> * Cancelar o pedido

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/portal)

Preencha os seguintes pré-requisitos de configuração para o serviço e dispositivo do Data Box antes de implantar o dispositivo:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Você pode entrar no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir:

* Você pode instalar a CLI e executar comandos da CLI localmente.
* Você pode executar comandos da CLI de dentro do portal do Azure, no Azure Cloud Shell.

Usamos a CLI do Azure por meio do Windows PowerShell para o tutorial, mas você pode escolher qualquer uma das opções.

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

* Instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) versão 2.0.67 ou posterior. Como alternativa, você pode [instalar usando o MSI](https://aka.ms/installazurecliwindows).

#### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra uma janela Comando do Windows PowerShell e entre no Azure com o comando [az login](/cli/azure/reference-index#az-login):

```azurecli
PS C:\Windows> az login
```

Aqui está a saída de uma conexão bem-sucedida:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>Instalar a extensão da CLI do Azure Data Box

Para usar os comandos da CLI do Azure Data Box, instale a extensão. As extensões da CLI do Azure fornecem acesso a comandos experimentais e de pré-lançamento que ainda não foram enviados como parte da CLI principal. Para obter mais informações sobre extensões, confira [Usar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).

Para instalar a extensão para o Azure Data Box, execute o seguinte comando: `az extension add --name databox`:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Se a extensão for instalada com êxito, você verá a seguinte saída:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

Você pode usar [Azure Cloud Shell](https://shell.azure.com/), um ambiente de shell interativo hospedado do Azure, por meio de seu navegador para executar comandos da CLI. O Azure Cloud Shell dá suporte a Bash ou Windows PowerShell com serviços do Azure. A CLI do Azure é pré-instalada e configurada para usar com sua conta. Clique no botão Cloud Shell no menu na seção superior direita do portal do Azure:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

O botão inicia um shell interativo que você pode usar para executar as etapas descritas neste artigo de instruções.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Solicitar Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Execute as etapas a seguir no portal do Azure para solicitar um dispositivo.

1. Use suas credenciais do Microsoft Azure para entrar nesta URL: [https://portal.azure.com](https://portal.azure.com).
2. Clique em **+ Criar um recurso** e pesquise *Azure Data Box*. Clique em **Azure Data Box**.

   [![Pesquisar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Clique em **Criar**.

4. Verifique se o serviço Data Box está disponível em sua região. Insira ou selecione as informações a seguir e selecione **Aplicar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Subscription     | Selecione uma assinatura do EA, do CSP ou do Azure Sponsorship para o serviço Data Box. <br> A assinatura está vinculada à sua conta de cobrança.       |
    |Tipo de transferência     | Selecione **Importar no Azure**.        |
    |País/região de origem    |    Selecione o país/região em que os dados residem no momento.         |
    |Região do Azure de destino     |     Selecione a região do Azure para onde você deseja transferir dados.        |

5. Selecione **Data Box**. A capacidade máxima utilizável de um único pedido é de 80 TB. É possível criar vários pedidos para tamanhos maiores de dados.

      [![Selecione Data Box opção 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. No **Pedido**, especifique os **Detalhes do pedido**. Insira ou selecione as informações a seguir e selecione **Avançar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Nome     |  Forneça um nome amigável para acompanhar o pedido. <br> O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. <br> O nome deve começar e terminar com uma letra ou um número.      |
    |Resource group     |    Use um grupo existente ou crie um novo. <br> Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos.         |
    |Região do Azure de destino     | Selecione uma região para sua conta de armazenamento. <br> Para saber mais, acesse [disponibilidade por região](data-box-overview.md#region-availability).        |
    |Destino de armazenamento     | Escolha entre conta de armazenamento, discos gerenciados ou ambos. <br> Com base na região especificada do Azure, selecione uma ou mais contas de armazenamento existente na lista filtrada de uma conta de armazenamento existente. O Data Box pode ser vinculada a até 10 contas de armazenamento. <br> Você também pode criar uma nova conta de **Uso geral v1**, **Uso geral v2** ou de **Armazenamento de Blobs**. <br>Contas de armazenamento com redes virtuais são compatíveis. Para permitir que o serviço do Data Box trabalhe com contas de armazenamento protegido, habilite os serviços confiáveis em definições de firewall de rede da conta de armazenamento. Para obter mais informações, confira como [Adicionar o Azure Data Box como um serviço confiável](../storage/common/storage-network-security.md#exceptions).|

    Se usar a conta de armazenamento como o destino de armazenamento, você verá a seguinte captura de tela:

    ![Ordem do Data Box para a conta de armazenamento](media/data-box-deploy-ordered/order-storage-account.png)

    Se usar o Data Box para criar discos gerenciados dos discos rígidos virtuais (VHDs) locais, você também precisará fornecer as seguintes informações:

    |Configuração  |Valor  |
    |---------|---------|
    |Grupos de recursos     | Se você pretende criar discos gerenciados de VHDs locais, crie novos grupos de recursos. Ao criar um pedido do Data Box para o disco gerenciado pelo serviço Data Box, você poderá usar um grupo de recursos somente se o grupo de recursos tiver sido criado anteriormente. <br> Especifique vários grupos de recursos separados por ponto e vírgula. Há suporte para um limite máximo de dez grupos de recursos.|

    ![Ordem do Data Box para o disco gerenciado](media/data-box-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para os discos gerenciados é usada como uma conta de armazenamento de preparo. O serviço do Data Box carrega os VHDs como blobs de páginas na conta de armazenamento de preparo antes de convertê-los em discos gerenciados e movê-los para os grupos de recursos. Para obter mais informações, confira [Verificar o upload de dados para o Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. No **Endereço para remessa**, forneça seu nome e sobrenome, nome e endereço da empresa e um número de telefone válido. Clique em **Validar endereço**. O serviço valida o endereço de remessa para a disponibilidade do serviço. Se o serviço está disponível para o endereço de remessa especificado, você receberá uma notificação para esse efeito.

   Se você tiver selecionado remessa autogerenciada, receberá uma notificação por email depois que o pedido for feito com sucesso. Para saber mais sobre a remessa autogerenciada, confira [Usar a remessa autogerenciada](data-box-portal-customer-managed-shipping.md).

8. Clique em **Avançar** após os detalhes da remessa tiverem sido validados com êxito.

9. Nos **Detalhes da notificação**, especifique endereços de email. O serviço envia as notificações por email em relação a quaisquer atualizações do status do pedido para os endereços de email especificados.

    É recomendável usar um email de grupo para que você continue a receber notificações caso um administrador do grupo saia.

10. Examine o **Resumo** de informações relacionadas com o pedido, o contato, a notificação e os termos de privacidade. Marque a caixa correspondente ao contrato de termos de privacidade.

11. Clique em **Pedido**. A criação do pedido demora alguns minutos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Execute as seguintes etapas usando a CLI do Azure para solicitar um dispositivo:

1. Anote suas configurações para seu pedido do Data Box. Essas configurações incluem suas informações pessoais/empresariais, nome da assinatura, informações do dispositivo e informações de envio. Você precisará usar essas configurações como parâmetros ao executar o comando da CLI para criar o pedido do Data Box. A seguinte tabela mostra as configurações de parâmetro usadas para `az databox job create`:

   | Configuração (parâmetro) | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group| Use um grupo existente ou crie um novo. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |name| O nome do pedido que você está criando. | "mydataboxorder"|
   |contact-name| O nome associado ao endereço para entrega. | "Gus Poland"|
   |phone| O número de telefone da pessoa ou da empresa que receberá o pedido.| "14255551234"
   |local| A região do Azure mais próxima a você que enviará seu dispositivo.| "Oeste dos EUA"|
   |sku| O dispositivo Data Box específico que você está solicitando. Os valores válidos são: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
   |email-list| Os endereços de email associados ao pedido.| "gusp@contoso.com" |
   |street-address1| O endereço para envio do pedido. | "15700 NE 39th St" |
   |street-address2| As informações de endereço secundário, como número do apartamento ou número do edifício. | "Bld 123" |
   |city| A cidade para a qual o dispositivo será enviado. | "Redmond" |
   |state-or-province| O Estado para o qual o dispositivo será enviado.| "WA" |
   |country| O país para o qual o dispositivo será enviado. | "Estados Unidos" |
   |postal-code| O CEP ou o código postal associado ao endereço para entrega.| "98052"|
   |company-name| O nome da empresa para a qual você trabalha.| "Contoso, LTD" |
   |do Azure| A conta de Armazenamento do Azure da qual você deseja importar dados.| "mystorageaccount"|
   |depurar| Incluir informações de depuração para registro em log detalhado  | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

2. No seu prompt de comando ou terminal preferido, use [az databox job create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) para criar seu pedido do Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Aqui está um exemplo de uso de comando:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Aqui está a saída da execução do comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Todos os comandos da CLI do Azure usarão json como o formato de saída por padrão, a menos que você o altere. Você pode alterar o formato de saída usando o parâmetro global `--output <output-format>`. Alterar o formato para "table" aprimorará a legibilidade da saída.

   Aqui está o mesmo comando que acabamos de executar com um pequeno ajuste para alterar a formatação:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Aqui está a saída da execução do comando:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>Acompanhar o pedido

# <a name="portal"></a>[Portal](#tab/portal)

Depois de fazer o pedido, você pode acompanhar o status do pedido no portal do Azure. Acesse seu pedido do Data Box e, em seguida, acesse **Visão Geral** para exibir o status. O portal mostra o pedido sob o estado **Pedido**.

Se o dispositivo não estiver disponível, você receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identificará o dispositivo para envio e o preparará. Durante a preparação do dispositivo, ocorrerão as seguintes ações:

* Os compartilhamentos SMB são criados para cada conta de armazenamento associada ao dispositivo.
* Para cada compartilhamento, as credenciais de acesso, como nome de usuário e senha, são geradas.
* Também é gerada uma senha do dispositivo que ajuda a desbloqueá-lo.
* O Data Box é bloqueado para impedir o acesso não autorizado ao dispositivo.

Após a conclusão da preparação do dispositivo, o portal mostra o pedido no estado **Processado**.

![O pedido do Data Box é processado](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft preparará e enviará seu dispositivo por meio de uma operadora regional. Após o envio do dispositivo, você receberá um número de acompanhamento. O portal mostra o pedido no estado **Despachado**.

![Pedido do Data Box expedido](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="track-a-single-order"></a>Acompanhar um pedido

Para obter informações de acompanhamento sobre um pedido do Azure Data Box, execute [az databox job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show). O comando exibe informações sobre o pedido como, entre outras: nome, grupo de recursos, informações de acompanhamento, ID da assinatura, informações de contato, tipo de remessa e SKU do dispositivo.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   A seguinte tabela mostra as informações de parâmetro para `az databox job show`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group [Obrigatório]| O nome do grupo de recursos associado ao pedido. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |name [Obrigatório]| O nome do pedido a ser exibido. | "mydataboxorder"|
   |depurar| Incluir informações de depuração para registro em log detalhado | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

   Aqui está um exemplo do comando com o formato de saída definido como "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Aqui está a saída da execução do comando:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Pode haver suporte para o pedido de lista no nível da assinatura, o que torna o grupo de recursos um parâmetro opcional (em vez de um parâmetro obrigatório).

### <a name="list-all-orders"></a>Listar todos os pedidos

Se você tiver pedido vários dispositivos, poderá executar [az databox job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) para ver todos os seus pedidos do Azure Data Box. O comando lista todos os pedidos que pertencem a um grupo de recursos específico. Também exibido na saída: nome do pedido, status da remessa, região do Azure, tipo de entrega, status do pedido. Pedidos cancelados também são incluídos na lista.
O comando também exibe carimbos de data/hora de cada pedido.

```azurecli
az databox job list --resource-group <resource-group>
```

A seguinte tabela mostra as informações de parâmetro para `az databox job list`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group [Obrigatório]| O nome do grupo de recursos que contém os pedidos. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |depurar| Incluir informações de depuração para registro em log detalhado | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

   Aqui está um exemplo do comando com o formato de saída definido como "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Aqui está a saída da execução do comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>Cancelar o pedido

# <a name="portal"></a>[Portal](#tab/portal)

Para cancelar esse pedido, no portal do Azure, vá para **Visão geral** e selecione **Cancelar** na barra de comandos.

Depois de fazer um pedido, você poderá cancelá-lo a qualquer momento antes do status ser marcado como processado.

Para excluir um pedido cancelado, vá para **Visão geral** e selecione **Excluir** na barra de comandos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="cancel-an-order"></a>Cancelar um pedido

Para cancelar um pedido do Azure Data Box, execute [az databox job cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel). Você precisa especificar o motivo para cancelar o pedido.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   A seguinte tabela mostra as informações de parâmetro para `az databox job cancel`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group [Obrigatório]| O nome do grupo de recursos associado ao pedido a ser excluído. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |name [Obrigatório]| O nome do pedido a ser excluído. | "mydataboxorder"|
   |reason [Obrigatório]| O motivo para cancelar o pedido. | "Inseri informações incorretas e precisei cancelar o pedido". |
   |sim| Não solicite confirmação. | --yes (-y)| --yes -y |
   |depurar| Incluir informações de depuração para registro em log detalhado | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

   Aqui está um exemplo do comando com a saída:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Aqui está a saída da execução do comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Excluir um pedido

Se você cancelou um pedido do Azure Data Box, pode executar [az databox job delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) para excluir o pedido.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   A seguinte tabela mostra as informações de parâmetro para `az databox job delete`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group [Obrigatório]| O nome do grupo de recursos associado ao pedido a ser excluído. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |name [Obrigatório]| O nome do pedido a ser excluído. | "mydataboxorder"|
   |subscription| O nome ou a ID (GUID) da sua assinatura do Azure. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |sim| Não solicite confirmação. | --yes (-y)| --yes -y |
   |depurar| Incluir informações de depuração para registro em log detalhado | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

Aqui está um exemplo do comando com a saída:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Aqui está a saída da execução do comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre artigos do Azure Data Box como:

> [!div class="checklist"]
>
> * Pré-requisitos para implantar o Data Box
> * Solicitar Data Box
> * Acompanhar o pedido
> * Cancelar o pedido

Avance para o próximo tutorial para saber como proteger seu Data Box.

> [!div class="nextstepaction"]
> [Configurar seu Azure Data Box](./data-box-deploy-set-up.md)
