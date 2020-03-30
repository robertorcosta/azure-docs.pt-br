---
title: Hospede um site estático no Azure Storage
description: Aprenda a servir conteúdo estático (ARQUIVOS HTML, CSS, JavaScript e imagens) diretamente de um contêiner em uma conta GPv2 de armazenamento Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247003"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hospede um site estático no Azure Storage

Você pode servir conteúdo estático (ARQUIVOS HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner em uma conta GPv2 de armazenamento Azure. Para saber mais, consulte [static website hosting in Azure Storage](storage-blob-static-website.md).

Este artigo mostra como habilitar a hospedagem de sites estáticos usando o portal Azure, o Azure CLI ou o PowerShell.

## <a name="enable-static-website-hosting"></a>Habilite a hospedagem de sites estáticos

Hospedagem de sites estáticos é um recurso que você tem que habilitar na conta de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com/) para começar.

2. Localize sua conta de armazenamento e exiba a visão geral dela.

3. Selecione **Site estático** para exibir a página de configuração de sites estáticos.

4. Selecione **Ativado** para habilitar a hospedagem de site estático para a conta de armazenamento.

5. No campo Nome do **documento Índice,** especifique uma página de índice padrão (Por exemplo: *index.html*). 

   A página de índice padrão é exibida quando um usuário navega para a raiz do site estático.  

6. No **campo 'Erro',** especifique uma página de erro padrão (Por exemplo: *404.html*). 

   A página de erro padrão é exibida quando o usuário tenta navegar até uma página que não existe no site estático.

7. Clique em **Salvar**. Agora, o portal do Azure exibe seu ponto de extremidade do site estático. 

    ![Habilitar a hospedagem de site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

Você pode habilitar a hospedagem de sites estáticos usando a [Cli (Command-Line Interface) do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Primeiro, abra o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), ou se você [tiver instalado](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure CLI localmente, abra um aplicativo de console de comando, como o Windows PowerShell.

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua `<subscription-id>` o valor do espaço reservado pelo ID da sua assinatura.

3. Habilite a hospedagem de sites estáticos.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

   * Substitua `<error-document-name>` o espaço reservado pelo nome do documento de erro que aparecerá aos usuários quando um navegador solicitar uma página em seu site que não existe.

   * Substitua `<index-document-name>` o espaço reservado pelo nome do documento de índice. Este documento é comumente "index.html".

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell" />

Você pode habilitar a hospedagem de sites estáticos usando o módulo Azure PowerShell.

1. Abra uma janela de comando do Windows PowerShell.

2. Verifique se você tem a versão Az do módulo Azure PowerShell 0.7 ou posterior.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

3. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

4. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para assinatura da conta de armazenamento que hospedará seu site estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua `<subscription-id>` o valor do espaço reservado pelo ID da sua assinatura.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que você deseja usar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua `<resource-group-name>` o valor do espaço reservado pelo nome do seu grupo de recursos.

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

6. Habilite a hospedagem de sites estáticos.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Substitua `<error-document-name>` o espaço reservado pelo nome do documento de erro que aparecerá aos usuários quando um navegador solicitar uma página em seu site que não existe.

   * Substitua `<index-document-name>` o espaço reservado pelo nome do documento de índice. Este documento é comumente "index.html".

---

## <a name="upload-files"></a>Carregar arquivos 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Estas instruções mostram como carregar arquivos usando a versão do Storage Explorer que aparece no portal Azure. No entanto, você também pode usar a versão do [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) que é executada fora do portal Azure. Você pode usar [AzCopy,](../common/storage-use-azcopy-v10.md)PowerShell, CLI ou qualquer aplicativo personalizado que possa carregar arquivos para o contêiner **$web** de sua conta. Para obter um tutorial passo-a-passo que carrega arquivos usando o código visual studio, consulte [Tutorial: Hospede um site estático no Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Selecione **O Explorador de Armazenamento (visualização)**.

2. Expanda o nó **BLOB CONTAINERS** e selecione o **$web** recipiente.

3. Escolha o botão **Carregar** para carregar arquivos.

   ![Carregar arquivos](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Se você pretende que o navegador exiba o conteúdo do arquivo, certifique-se de que o tipo de conteúdo desse arquivo está definido como `text/html`. 

   ![Verifique os tipos de conteúdo](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > O Storage Explorer define `text/html` automaticamente essa propriedade para `.html`extensões comumente reconhecidas, como . No entanto, em alguns casos, você terá que definir isso você mesmo. Se você não definir essa `text/html`propriedade para , o navegador solicitará aos usuários que baixem o arquivo em vez de renderizar o conteúdo. Para definir essa propriedade, clique com o botão direito do mouse no arquivo e clique **em Propriedades**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Faça upload de objetos para o recipiente *$web* de um diretório de origem.

> [!NOTE]
> Se você estiver usando o Azure Cloud `\` Shell, certifique-se `$web` de adicionar um `\$web`caractere de fuga ao se referir ao contêiner (Por exemplo: ). Se você estiver usando uma instalação local do Azure CLI, então você não terá que usar o caractere de fuga.

Este exemplo pressupõe que você está executando comandos da sessão Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua `<source-path>` o espaço reservado por um caminho para a localização dos arquivos que deseja carregar.

> [!NOTE]
> Se você estiver usando uma instalação de localização do Azure CLI, então você pode `C:\myFolder`usar o caminho para qualquer local em seu computador local (Por exemplo: .
>
> Se você estiver usando o Azure Cloud Shell, você terá que fazer referência a um compartilhamento de arquivos visível para o Cloud Shell. Esse local pode ser o compartilhamento de arquivos do próprio compartilhamento de nuvem ou um compartilhamento de arquivos existente que você monta a partir do Cloud Shell. Para saber como fazer isso, consulte [Os arquivos Persist no Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Faça upload de objetos para o recipiente *$web* de um diretório de origem.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Substitua `<path-to-file>` o valor do espaço reservado pelo caminho totalmente qualificado para `C:\temp\index.html`o arquivo que deseja carregar (Por exemplo: ).

* Substitua `<blob-name>` o valor do espaço reservado pelo nome que deseja dar `index.html`à bolha resultante (Por exemplo: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Encontre a URL do site usando o portal Azure

Você pode visualizar as páginas do seu site a partir de um navegador usando a URL pública do site.

### <a name="portal"></a>[Portal](#tab/azure-portal)

<a id="portal-find-url" />

No painel que aparece ao lado da página de visão geral da conta de armazenamento, selecione **Static Website**. A URL do seu site é exibida no campo **ponto final** principal.

![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

Encontre a URL pública do seu site estático usando o seguinte comando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua `<resource-group-name>` o valor do espaço reservado pelo nome do seu grupo de recursos.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell-find-url" />

Encontre a URL pública do seu site estático usando usando o seguinte comando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Substitua `<resource-group-name>` o valor do espaço reservado pelo nome do seu grupo de recursos.

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Habilite métricas em páginas de sites estáticos

Depois de habilitar métricas, estatísticas de tráfego em arquivos no **$web** contêiner são relatadas no painel de métricas.

1. Clique **em Métricas** na seção **Monitor** do menu da conta de armazenamento.

   > [!div class="mx-imgBorder"]
   > ![Link de métricas](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Dados de métricas são gerados pendurando em diferentes APIs de métrica. O portal exibe apenas membros da API usados em um determinado período de tempo para se concentrar apenas em membros que retornam dados. Para garantir que você seja capaz de selecionar o membro da API necessário, o primeiro passo é expandir o período de tempo.

2. Clique no botão de período de tempo, escolha um período de tempo e clique **em Aplicar**.

   ![Intervalo de tempo de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecione **Blob** na queda do *Namespace.*

   ![Namespace de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Em seguida, selecione a métrica **Egresso**.

   ![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecione **Somar** do seletor *Agregação*.

   ![Agregação de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Clique no botão **Adicionar filtro** e escolha o nome **da API** no seletor *de propriedades.*

   ![Nome da API de métricas de sies estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Marque a caixa ao lado **de GetWebContent** no seletor *Valores* para preencher o relatório de métricas.

   ![O GetWebContent de métricas de websites estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > A caixa de seleção **GetWebContent** só aparece se esse membro da API for usado dentro de um determinado período de tempo. O portal exibe apenas membros da API usados em um determinado período de tempo para se concentrar apenas em membros que retornam dados. Se você não conseguir encontrar um membro específico da API nesta lista, expanda o período de tempo.

## <a name="next-steps"></a>Próximas etapas

* Saiba como configurar um domínio personalizado com seu site estático. Veja [Mapear um domínio personalizado para um ponto final de armazenamento do Azure Blob](storage-custom-domain-name.md).

