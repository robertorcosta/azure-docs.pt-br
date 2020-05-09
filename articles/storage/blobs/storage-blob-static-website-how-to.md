---
title: Hospedar um site estático no armazenamento do Azure
description: Saiba como servir conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner em uma conta do Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 23a5d2c0e52a22872a8b9a64503d61493018b611
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839157"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hospedar um site estático no armazenamento do Azure

Você pode fornecer conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner em uma conta do Azure Storage GPv2. Para saber mais, confira [Hospedagem de site estático no armazenamento do Azure](storage-blob-static-website.md).

Este artigo mostra como habilitar a hospedagem estática de sites usando o portal do Azure, o CLI do Azure ou o PowerShell.

## <a name="enable-static-website-hosting"></a>Habilitar Hospedagem de site estático

A hospedagem de site estática é um recurso que você precisa habilitar na conta de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com/) para começar.

2. Localize sua conta de armazenamento e exiba a visão geral dela.

3. Selecione **Site estático** para exibir a página de configuração de sites estáticos.

4. Selecione **Ativado** para habilitar a hospedagem de site estático para a conta de armazenamento.

5. No campo **nome do documento de índice** , especifique uma página de índice padrão (por exemplo: *index. html*). 

   A página de índice padrão é exibida quando um usuário navega para a raiz do site estático.  

6. No campo **caminho do documento de erro** , especifique uma página de erro padrão (por exemplo: *404. html*). 

   A página de erro padrão é exibida quando o usuário tenta navegar até uma página que não existe no site estático.

7. Clique em **Save** (Salvar). Agora, o portal do Azure exibe seu ponto de extremidade do site estático. 

    ![Habilitar a hospedagem de site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

<a id="cli" />

Você pode habilitar a hospedagem de site estático usando a [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Primeiro, abra o [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)ou, se você tiver [instalado](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o CLI do Azure localmente, abra um aplicativo de console de comando, como o Windows PowerShell.

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

3. Habilitar Hospedagem de site estático.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

   * Substitua o `<error-document-name>` espaço reservado pelo nome do documento de erro que será exibido aos usuários quando um navegador solicitar uma página no site que não existe.

   * Substitua o `<index-document-name>` espaço reservado pelo nome do documento de índice. Este documento é normalmente "index. html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell" />

Você pode habilitar a hospedagem de site estático usando o módulo Azure PowerShell.

1. Abra uma janela de comando do Windows PowerShell.

2. Verifique se você tem Azure PowerShell módulo AZ versão 0,7 ou posterior.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

3. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

4. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que você deseja usar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

6. Habilitar Hospedagem de site estático.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Substitua o `<error-document-name>` espaço reservado pelo nome do documento de erro que será exibido aos usuários quando um navegador solicitar uma página no site que não existe.

   * Substitua o `<index-document-name>` espaço reservado pelo nome do documento de índice. Este documento é normalmente "index. html".

---

## <a name="upload-files"></a>Carregar arquivos 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Estas instruções mostram como carregar arquivos usando a versão do Gerenciador de Armazenamento que aparece na portal do Azure. No entanto, você também pode usar a versão do [Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) que é executado fora do portal do Azure. Você pode usar o [AzCopy](../common/storage-use-azcopy-v10.md), o PowerShell, a CLI ou qualquer aplicativo personalizado que possa carregar arquivos para o contêiner de **$Web** de sua conta. Para obter um tutorial passo a passo que carrega arquivos usando o Visual Studio Code, consulte [tutorial: hospedar um site estático no armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Selecione **Gerenciador de armazenamento (versão prévia)**.

2. Expanda o nó **contêineres de blob** e selecione o **$Web** contêiner.

3. Escolha o botão **carregar** para carregar arquivos.

   ![Carregar arquivos](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Se você pretende que o navegador exiba o conteúdo do arquivo, verifique se o tipo de conteúdo desse arquivo está definido como `text/html`. 

   ![Verificar tipos de conteúdo](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Gerenciador de Armazenamento define automaticamente essa propriedade como `text/html` para extensões comumente reconhecidas `.html`, como. No entanto, em alguns casos, você terá que definir isso por conta própria. Se você não definir essa propriedade como `text/html`, o navegador solicitará que os usuários baixem o arquivo em vez de renderizar o conteúdo. Para definir essa propriedade, clique com o botão direito do mouse no arquivo e clique em **Propriedades**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Carregue objetos no contêiner de *$Web* de um diretório de origem.

> [!NOTE]
> Se você estiver usando Azure Cloud Shell, certifique-se de adicionar `\` um caractere de escape ao fazer referência `$web` ao contêiner (por exemplo `\$web`:). Se você estiver usando uma instalação local do CLI do Azure, não precisará usar o caractere de escape.

Este exemplo pressupõe que você está executando comandos de Azure Cloud Shell sessão.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
```

> [!NOTE] 
> Se o navegador solicitar que os usuários baixem o arquivo em vez de renderizar o conteúdo, você `--content-type 'text/html; charset=utf-8'` poderá acrescentar ao comando. 

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o `<source-path>` espaço reservado por um caminho para o local dos arquivos que você deseja carregar.

> [!NOTE]
> Se você estiver usando uma instalação de local do CLI do Azure, poderá usar o caminho para qualquer local no computador local (por exemplo: `C:\myFolder`.
>
> Se estiver usando Azure Cloud Shell, você precisará fazer referência a um compartilhamento de arquivos que é visível para o Cloud Shell. Esse local pode ser o compartilhamento de arquivos do compartilhamento de nuvem em si ou um compartilhamento de arquivos existente que você monta da Cloud Shell. Para saber como fazer isso, consulte [arquivos de persistência em Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Carregue objetos no contêiner de *$Web* de um diretório de origem.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Se o navegador solicitar que os usuários baixem o arquivo em vez de renderizar o conteúdo, você `-Properties @{ ContentType = "text/html; charset=utf-8";}` poderá acrescentar ao comando.

* Substitua o `<path-to-file>` valor do espaço reservado pelo caminho totalmente qualificado para o arquivo que você deseja carregar (por exemplo: `C:\temp\index.html`).

* Substitua o `<blob-name>` valor do espaço reservado pelo nome que você deseja dar ao blob resultante (por exemplo: `index.html`).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Localize a URL do site usando o portal do Azure

Você pode exibir as páginas do seu site em um navegador usando a URL pública do site.

### <a name="portal"></a>[Portal](#tab/azure-portal)

<a id="portal-find-url" />

No painel que aparece ao lado da página Visão geral da conta da sua conta de armazenamento, selecione **site estático**. A URL do seu site aparece no campo **ponto de extremidade primário** .

![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

<a id="cli-find-url" />

Localize a URL pública do seu site estático usando o seguinte comando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell-find-url" />

Localize a URL pública do seu site estático usando o seguinte comando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Habilitar métricas em páginas de site estáticos

Depois de habilitar as métricas, as estatísticas de tráfego nos arquivos no contêiner de **$Web** são relatadas no painel de métricas.

1. Clique em **métricas** na seção **Monitor** do menu da conta de armazenamento.

   > [!div class="mx-imgBorder"]
   > ![Link de métricas](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Dados de métricas são gerados pendurando em diferentes APIs de métrica. O portal exibe apenas membros da API usados em um determinado período de tempo para se concentrar apenas em membros que retornam dados. Para garantir que você seja capaz de selecionar o membro de API necessário, a primeira etapa é expandir o intervalo de tempo.

2. Clique no botão intervalo de tempo, escolha um intervalo de tempo e, em seguida, clique em **aplicar**.

   ![Intervalo de tempo de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecione **blob** na lista suspensa *namespace* .

   ![Namespace de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Em seguida, selecione a métrica **Egresso**.

   ![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecione **Somar** do seletor *Agregação*.

   ![Agregação de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Clique no botão **Adicionar filtro** e escolha **nome da API** no seletor de *Propriedade* .

   ![Nome da API de métricas de sies estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Marque a caixa ao lado de **GetWebContent** no seletor de *valores* para preencher o relatório de métricas.

   ![O GetWebContent de métricas de websites estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > A caixa de seleção **GetWebContent** será exibida somente se o membro da API tiver sido usado em um determinado período de tempo. O portal exibe apenas membros da API usados em um determinado período de tempo para se concentrar apenas em membros que retornam dados. Se você não encontrar um membro de API específico nessa lista, expanda o intervalo de tempo.

## <a name="next-steps"></a>Próximas etapas

* Saiba como configurar um domínio personalizado com seu site estático. Consulte [mapear um domínio personalizado para um ponto de extremidade do armazenamento de BLOBs do Azure](storage-custom-domain-name.md).

