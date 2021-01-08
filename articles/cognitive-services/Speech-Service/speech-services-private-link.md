---
title: Como usar pontos de extremidade privados com o serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar o serviço de fala com pontos de extremidade privados fornecidos pelo link privado do Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: d5822b6eeecfc61a5092519618ddfcaf88a625ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018520"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>Usar o serviço de fala por meio de um ponto de extremidade privado

O [link privado do Azure](../../private-link/private-link-overview.md) permite que você se conecte a serviços no Azure usando um [ponto de extremidade privado](../../private-link/private-endpoint-overview.md).
Um ponto de extremidade privado é um endereço IP privado acessível somente dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md) específica e uma sub-rede.

Este artigo explica como configurar e usar pontos de extremidade privados e de vínculo privado com os serviços de fala cognitiva do Azure.

> [!NOTE]
> Este artigo explica as especificidades de configuração e uso do link privado com os serviços de fala cognitiva do Azure. Antes de continuar, examine como [usar redes virtuais com serviços cognitivas](../cognitive-services-virtual-networks.md).

Execute as seguintes tarefas para usar um serviço de fala por meio de um ponto de extremidade privado:

1. [Criar nome de domínio personalizado do recurso de fala](#create-a-custom-domain-name)
2. [Criar e configurar pontos de extremidade particulares](#enable-private-endpoints)
3. [Ajustar aplicativos e soluções existentes](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Para remover pontos de extremidade privados posteriormente, mas ainda usar o recurso de fala, você executará as tarefas encontradas nesta [seção](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Criar um nome de domínio personalizado

Os pontos de extremidade privados exigem um [nome de subdomínio personalizado dos serviços cognitivas](../cognitive-services-custom-subdomains.md). Siga as instruções abaixo para criar um para seu recurso de fala.

> [!WARNING]
> Um recurso de fala com o nome de domínio personalizado habilitado usa uma maneira diferente de interagir com o serviço de fala.
> Você provavelmente deve ajustar o código do aplicativo para o [ponto de extremidade privado habilitado](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) e não para cenários de [  ponto de extremidade privada habilitados](#use-speech-resource-with-custom-domain-name-without-private-endpoints) .
>
> Quando você habilita um nome de domínio personalizado, a operação [**não é reversível**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). A única maneira de voltar para o [nome regional](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) é criar um novo recurso de fala.
>
> Se o seu recurso de fala tiver muitos modelos personalizados e projetos associados criados por meio do [Speech Studio](https://speech.microsoft.com/) , é **altamente** recomendável tentar a configuração com um recurso de teste antes de modificar o recurso usado na produção.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para criar um nome de domínio personalizado usando portal do Azure, siga estas etapas:

1. Acesse [portal do Azure](https://portal.azure.com/) e entre em sua conta do Azure.
1. Selecione o recurso de fala necessário.
1. No grupo **Gerenciamento de recursos** no painel de navegação esquerdo, clique em **rede**.
1. Na guia **firewalls e redes virtuais** , clique em **gerar nome de domínio personalizado**. Um novo painel direito é exibido com instruções para criar um subdomínio personalizado exclusivo para seu recurso.
1. No painel gerar nome de domínio personalizado, insira uma parte do nome de domínio personalizado. Seu domínio personalizado completo terá a seguinte aparência: `https://{your custom name}.cognitiveservices.azure.com` . 
    **Depois de criar um nome de domínio personalizado, ele _não pode_ ser alterado! Leia novamente o alerta de aviso acima.** Depois de inserir seu nome de domínio personalizado, clique em **salvar**.
1. Após a conclusão da operação, no grupo **Gerenciamento de recursos** , clique em **chaves e ponto de extremidade**. Confirme se o novo nome do ponto de extremidade do recurso é iniciado dessa forma:

    `https://{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um nome de domínio personalizado usando o PowerShell, confirme se o computador tem o PowerShell versão 7. x ou posterior com o módulo Azure PowerShell versão 5.1.0 ou posterior. para ver as versões dessas ferramentas, siga estas etapas:

1. Em uma janela do PowerShell, digite:

    `$PSVersionTable`

    Confirme se o valor de PSVersion é maior que 7. x. Para atualizar o PowerShell, siga as instruções em [Instalando várias versões do PowerShell](/powershell/scripting/install/installing-powershell) para atualizar.

1. Em uma janela do PowerShell, digite:

    `Get-Module -ListAvailable Az`

    Se nada aparecer ou se Azure PowerShell versão do módulo for inferior a 5.1.0, siga as instruções em [instalar Azure PowerShell módulo](/powershell/azure/install-Az-ps) para atualizar.

Antes de continuar, execute `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="verify-custom-domain-name-is-available"></a>Verifique se o nome de domínio personalizado está disponível

Verifique se o domínio personalizado que você deseja usar está disponível. Siga estas etapas para confirmar se o domínio está disponível usando a operação [verificar disponibilidade de domínio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) na API REST dos serviços cognitivas.

> [!TIP]
> O código a seguir **não** funcionará no Azure cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare OAuth token to use in request to Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Se o nome desejado estiver disponível, você verá uma resposta como esta:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Se o nome já estiver em uso, você verá a seguinte resposta:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Criar seu nome de domínio personalizado

Para habilitar o nome de domínio personalizado para o recurso de fala selecionado, usamos o cmdlet [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> Depois que o código abaixo for executado com êxito, você criará um nome de domínio personalizado para seu recurso de fala.
> Esse nome **não pode** ser alterado. Veja mais informações no alerta de **aviso** acima.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Esta seção requer a versão mais recente do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="verify-the-custom-domain-name-is-available"></a>Verifique se o nome de domínio personalizado está disponível

Verifique se o domínio personalizado que você gostaria de usar é gratuito. Usaremos o método [verificar disponibilidade do domínio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) da API REST de serviços cognitivas.

Copie o bloco de código abaixo, insira seu nome de domínio personalizado preferencial e salve-o no arquivo `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Copie o arquivo para a pasta atual ou carregue-o no Azure Cloud Shell e execute o comando a seguir. (Substitua `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` pela sua ID de assinatura do Azure).

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Se o nome desejado estiver disponível, você verá uma resposta como esta:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Se o nome já estiver em uso, você verá a seguinte resposta:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-custom-domain-name"></a>Habilitar nome de domínio personalizado

Para habilitar o nome de domínio personalizado para o recurso de fala selecionado, usamos o comando [AZ cognitivaservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Selecione a assinatura do Azure que contém o recurso de fala. Se sua conta do Azure tiver apenas uma assinatura ativa, você poderá ignorar esta etapa. (Substitua `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` pela sua ID de assinatura do Azure).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Defina o nome de domínio personalizado para o recurso selecionado. Substitua os valores de parâmetro de exemplo pelos reais e execute o comando a seguir.

> [!WARNING]
> Após a execução bem-sucedida do comando abaixo, você criará um nome de domínio personalizado para seu recurso de fala. Esse nome **não pode** ser alterado. Veja mais informações no alerta de cuidado acima.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enable-private-endpoints"></a>Habilitar pontos de extremidade privados

É recomendável usar a [zona DNS privada](../../dns/private-dns-overview.md) anexada à rede virtual com as atualizações necessárias para os pontos de extremidade particulares, que criamos por padrão durante o processo de provisionamento. No entanto, se você estiver usando seu próprio servidor DNS, talvez também precise alterar a configuração do DNS, conforme mostrado no _DNS para pontos de extremidade privados_, abaixo. Decida sobre a estratégia DNS _ *antes* de * provisionar pontos de extremidade privados para um recurso de fala de produção e teste suas alterações de DNS, especialmente se você usar seu próprio servidor DNS.

Use um dos artigos a seguir para criar pontos de extremidade (s) particulares. Os artigos usam um aplicativo Web como um recurso de exemplo para habilitar com pontos de extremidade privados. Você usará esses parâmetros em vez daqueles no artigo:

| Configuração             | Valor                                    |
|---------------------|------------------------------------------|
| Tipo de recurso       | **Microsoft. Cognitivaservices/contas** |
| Recurso            | **\<your-speech-resource-name>**         |
| Sub-recurso de destino | **conta**                              |

- [Criar um ponto de extremidade privado usando o portal do Azure](../../private-link/create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado usando Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Criar um ponto de extremidade privado usando CLI do Azure](../../private-link/create-private-endpoint-cli.md)

**DNS para pontos de extremidade privados:** Examine os princípios gerais do [DNS para pontos de extremidade privados em recursos de serviços cognitivas](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Em seguida, confirme se a configuração de DNS está funcionando corretamente executando estas verificações:

### <a name="resolve-dns-from-the-virtual-network"></a>Resolver o DNS da rede virtual

Essa verificação é **necessária**.

Siga estas etapas para testar a entrada DNS personalizada de sua rede virtual.

1. Faça logon em uma máquina virtual localizada na rede virtual à qual você anexou seu ponto de extremidade privado. 
1. Abra o prompt de comando do Windows ou o Shell do bash, execute `nslookup` e confirme que ele resolve com êxito o nome de domínio personalizado do recurso.

```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```

3. Confirme se o endereço IP corresponde ao endereço IP do seu ponto de extremidade privado.

### <a name="resolve-dns-from-other-networks"></a>Resolver o DNS de outras redes

Somente Execute essa verificação se você planeja usar o recurso de fala habilitado para o ponto de extremidade privado no modo "híbrido", no qual você habilitou **todas as redes** ou redes selecionadas e a opção acesso de **pontos de extremidades privados** na seção **rede** do recurso. Se você planeja acessar o recurso usando apenas um ponto de extremidade privado, você pode ignorar esta seção.

1. Faça logon em um computador conectado a uma rede com permissão para acessar o recurso.
2. Abra o prompt de comando do Windows ou o Shell do bash, execute `nslookup` e confirme que ele resolve com êxito o nome de domínio personalizado do recurso.

```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
Address:  13.69.67.71
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          my-private-link-speech.privatelink.cognitiveservices.azure.com
          westeurope.prod.vnet.cog.trafficmanager.net
```

3. Confirme se o endereço IP corresponde ao endereço IP do seu ponto de extremidade privado.

> [!NOTE]
> O endereço IP resolvido aponta para um ponto de extremidade de proxy de rede virtual, que expede o tráfego de rede para o ponto de extremidade privado para o recurso de serviços cognitivas. O comportamento será diferente para um recurso com um nome de domínio personalizado, mas *sem* pontos de extremidade privados. Consulte [esta seção](#dns-configuration) para obter detalhes.

## <a name="adjust-existing-applications-and-solutions"></a>Ajustar aplicativos e soluções existentes

Um recurso de fala com um domínio personalizado habilitado usa uma maneira diferente de interagir com os serviços de fala. Isso é verdadeiro para um recurso de fala habilitado para domínio personalizado com e sem pontos de extremidade privados. As informações nesta seção se aplicam a ambos os cenários.

### <a name="use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Usar o recurso de fala com o nome de domínio personalizado e o ponto de extremidade privado habilitado

Um recurso de fala com nome de domínio personalizado e ponto de extremidade privado habilitado usa uma maneira diferente de interagir com os serviços de fala. Esta seção explica como usar esse recurso com a API REST dos serviços de fala e o [SDK de fala](speech-sdk.md).

> [!NOTE]
> Observe que um recurso de fala sem pontos de extremidade privados, mas com o **nome de domínio personalizado** habilitado também tem uma maneira especial de interagir com os serviços de fala, mas dessa maneira é diferente do cenário de um recurso de fala habilitado para ponto de extremidade privado. Se você tiver esse recurso (digamos, você tinha um recurso com pontos de extremidade privados, mas decidiu removê-los), certifique-se de se familiarizar com a [seção correspondente](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Recurso de fala com nome de domínio personalizado e ponto de extremidade privado. Uso com a API REST

Usaremos `my-private-link-speech.cognitiveservices.azure.com` como um nome DNS de recurso de fala de exemplo (domínio personalizado) para esta seção.

##### <a name="note-on-speech-services-rest-api"></a>Observação sobre a API REST dos serviços de fala

Os serviços de fala têm a API REST para [conversão de fala em texto](rest-speech-to-text.md) e [texto em fala](rest-text-to-speech.md). O seguinte deve ser considerado para o cenário de ponto de extremidade privado habilitado.

A conversão de fala em texto tem duas APIs REST diferentes. Cada API atende a uma finalidade diferente, usa pontos de extremidade diferentes e requer uma abordagem diferente quando usado individualmente no cenário de ponto de extremidades privado habilitado.

As APIs REST de conversão de fala em texto são:
- [A API REST de fala em texto v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) é usada para a transcrição e a [fala personalizada](custom-speech-overview.md)do [lote](batch-transcription.md) . o v 3.0 é um [sucessor da v 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- [A API REST de conversão de fala em texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) é usada para transcrição online. 

O uso da API REST de fala em texto para a API REST de áudio curto e de conversão de texto em fala no cenário de ponto de extremidade privado é o mesmo e equivalente ao [caso do SDK de fala](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) descrito posteriormente neste artigo. 

A API REST de conversão de fala em texto v 3.0 está usando um conjunto diferente de pontos de extremidade e, portanto, requer uma abordagem diferente para o cenário do Endpoint privado habilitado.

Ambos os casos são descritos nas próximas subseções.


##### <a name="speech-to-text-rest-api-v30"></a>API REST de conversão de fala em texto v 3.0

Normalmente, os recursos de fala usam [pontos de extremidade regionais de serviços cognitivas](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) para se comunicar com a [API REST de fala em texto v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Esses recursos têm o seguinte formato de nomenclatura: <p/>`{region}.api.cognitive.microsoft.com`

Este é um exemplo de URL de solicitação:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Depois de habilitar o domínio personalizado para um recurso de fala (que é necessário para pontos de extremidade privados), esse recurso usará o seguinte padrão de nome DNS para o ponto de extremidade básico da API REST: <p/>`{your custom name}.cognitiveservices.azure.com`

Isso significa que, em nosso exemplo, o nome do ponto de extremidade da API REST será: <p/>`my-private-link-speech.cognitiveservices.azure.com`

E a URL de solicitação de exemplo acima precisa ser convertida em:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Essa URL deve ser acessível da rede virtual com o ponto de extremidade privado anexado (forneceu a [resolução de DNS correta](#resolve-dns-from-the virtual-network)).

Normalmente, depois de habilitar o nome de domínio personalizado para um recurso de fala, você substituirá hostname em todas as URLs de solicitação pelo novo nome de host de domínio personalizado. Todas as outras partes da solicitação (como o caminho `/speechtotext/v3.0/transcriptions` no exemplo acima) permanecem as mesmas.

> [!TIP]
> Alguns clientes desenvolveram aplicativos que usam a parte da região do nome DNS do ponto de extremidade regional (por exemplo, para enviar a solicitação para o recurso de fala implantado na região específica do Azure).
>
> O nome de domínio personalizado do recurso de fala **não** contém informações sobre a região em que o recurso foi implantado. Portanto, a lógica do aplicativo descrita acima **não** funcionará e precisará ser alterada.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST de fala em texto para API REST de áudio curto e de conversão de texto em fala

[API REST de fala em texto para API REST de áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) e de [conversão de texto em fala](rest-text-to-speech.md) usa dois tipos de pontos de extremidade:
- [Pontos de extremidade regionais de serviços cognitivas](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) para se comunicar com a API REST de serviços cognitivas para obter um token de autorização
- Pontos de extremidade especiais para todas as outras operações

A descrição detalhada dos pontos de extremidade especiais e de como sua URL deve ser transformada para um recurso de fala habilitado para ponto de extremidades privado é fornecida nesta [subseção](#general-principle) da seção "uso com o SDK de fala" abaixo. O mesmo princípio descrito para o SDK se aplica à API REST de conversão de fala em texto v 1.0 e de conversão de texto em fala.

Familiarize-se com o material na subseção mencionada no parágrafo anterior e veja o exemplo a seguir. (O exemplo descreve a API REST de conversão de texto em fala; o uso da API REST de fala em texto para áudio curto é totalmente equivalente)

> [!NOTE]
> Ao usar a **API REST de fala em texto para um áudio curto** em cenários de ponto de extremidade privado, use um token de autorização [passado pelo](rest-speech-to-text.md#request-headers) `Authorization` [cabeçalho](rest-speech-to-text.md#request-headers). Passar a chave de assinatura de fala para o ponto de extremidade especial por meio do `Ocp-Apim-Subscription-Key` cabeçalho **não** funcionará e gerará o erro 401.

**Exemplo de uso da API REST de conversão de texto em fala.**

Usaremos Europa Ocidental como uma região do Azure de exemplo e `my-private-link-speech.cognitiveservices.azure.com` como um nome DNS de recurso de fala de exemplo (domínio personalizado). O nome de domínio personalizado `my-private-link-speech.cognitiveservices.azure.com` em nosso exemplo pertence ao recurso de fala criado em Europa ocidental região.

Para obter a lista das vozes com suporte na região, é necessário realizar as duas operações a seguir:

- Obter token de autorização:
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Usando o token, obtenha a lista de vozes:
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(Veja mais detalhes nas etapas acima na [documentação da API REST de conversão de texto em fala](rest-text-to-speech.md))

Para o ponto de extremidade privado habilitado, recurso de fala as URLs de ponto de extremidade para a mesma sequência de operação precisam ser modificadas. A mesma sequência terá a seguinte aparência:
- Obter token de autorização via
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(consulte a explicação detalhada na subseção [API REST de fala para texto v 3.0](#speech-to-text-rest-api-v30) acima)
- Usando o token obtido, obtenha a lista de vozes via
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(consulte a explicação detalhada na subseção [princípio geral](#general-principle) da seção "uso com o SDK de fala" abaixo)

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Recurso de fala com nome de domínio personalizado e ponto de extremidade privado. Uso com o SDK de fala

O uso do SDK de fala com o nome de domínio personalizado e os recursos de fala habilitados para o ponto de extremidade é necessário para examinar e provavelmente alterações do código do aplicativo.

Usaremos `my-private-link-speech.cognitiveservices.azure.com` como um nome DNS de recurso de fala de exemplo (domínio personalizado) para esta seção.

##### <a name="general-principle"></a>Princípio geral

Geralmente, em cenários de SDK (bem como nos cenários de API REST de conversão de texto em fala), os recursos de fala usam os pontos de extremidade regionais dedicados para diferentes ofertas de serviço. O formato de nome DNS para esses pontos de extremidade é: </p>`{region}.{speech service offering}.speech.microsoft.com`

Exemplo: </p>`westeurope.stt.speech.microsoft.com`

Todos os valores possíveis para a região (primeiro elemento do nome DNS) são listados [aqui](regions.md) esta tabela a seguir apresenta o valor possível para a oferta de serviços de fala (segundo elemento do nome DNS):

| Valor do nome DNS | Oferta de serviços de fala                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Comandos personalizados](custom-commands.md)                       |
| `convai`       | [Transcrição de Conversas](conversation-transcription.md) |
| `s2s`          | [Tradução de Fala](speech-translation.md)                 |
| `stt`          | [Conversão de fala em texto](speech-to-text.md)                         |
| `tts`          | [Conversão de texto em fala](text-to-speech.md)                         |
| `voice`        | [Voz personalizada](how-to-custom-voice.md)                      |

Portanto, o exemplo acima ( `westeurope.stt.speech.microsoft.com` ) representa o ponto de extremidade de fala para texto em Europa Ocidental.

Pontos de extremidade habilitados para Endpoint privado se comunicam com os serviços de fala por meio de um proxy especial e, por isso **, você deve alterar as URLs de conexão do ponto de extremidade** 

Uma URL de ponto de extremidade "padrão" é semelhante A: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Uma URL de ponto de extremidade particular é semelhante A: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Exemplo 1.** O aplicativo está se comunicando usando a seguinte URL (reconhecimento de fala usando o modelo de base para inglês americano em Europa Ocidental):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Para usá-lo no cenário de ponto de extremidade privado habilitado quando o nome de domínio personalizado do recurso de fala é `my-private-link-speech.cognitiveservices.azure.com` necessário modificar a URL desta forma:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Observe os detalhes:

- O nome do host `westeurope.stt.speech.microsoft.com` é substituído pelo nome de host do domínio personalizado `my-private-link-speech.cognitiveservices.azure.com` .
- O segundo elemento do nome DNS original ( `stt` ) torna-se o primeiro elemento do caminho da URL e precede o caminho original. Portanto, a URL original `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` se torna `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Exemplo 2.** O aplicativo usa a seguinte URL para sintetizar a fala em Europa Ocidental usando um modelo de voz personalizado):
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

A seguir está uma URL equivalente que usa um ponto de extremidade privado habilitado onde o nome de domínio personalizado do recurso de fala é `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

O mesmo princípio, como no exemplo 1, é aplicado, mas o elemento key desta vez é `voice` .

##### <a name="modify-applications"></a>Modificar aplicativos

Siga estas etapas para modificar seu código:

**1. determinar a URL do ponto de extremidade do aplicativo**

- [Habilite o registro em log para seu aplicativo](how-to-use-logging.md) e execute-o para registrar a atividade.
- No arquivo de log, procure `SPEECH-ConnectionUrl` . Em linhas correspondentes, o `value` parâmetro contém a URL completa que seu aplicativo usou para acessar o serviço de fala.

Exemplo:

```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```

Portanto, a URL usada pelo aplicativo neste exemplo é:

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

**2. criar `SpeechConfig` instância usando a URL completa do ponto de extremidade**

Modifique o ponto de extremidade que você determinou na seção anterior, conforme descrito em [princípio geral](#general-principle) acima.

Agora, modifique como você cria a instância do `SpeechConfig` . Provavelmente, o aplicativo de hoje está usando algo assim:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Isso não funcionará para o recurso de fala habilitado para ponto de extremidade privado devido às alterações de nome de host e URL descritas nas seções anteriores. Se você tentar executar seu aplicativo existente sem modificações usando a chave de um recurso de ponto de extremidade privado habilitado, receberá um erro de autenticação (401).

Para fazê-lo funcionar, modifique como você instancia a `SpeechConfig` classe e use a inicialização "do ponto de extremidade"/"com ponto de extremidade". Suponha que tenhamos as duas variáveis definidas a seguir:
- `subscriptionKey` que contém a chave do recurso de fala habilitado para ponto de extremidade privado
- `endPoint` que contém a URL do ponto de extremidade **modificado** completo (usando o tipo exigido pela linguagem de programação correspondente). Em nosso exemplo, essa variável deve conter
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Em seguida, crie uma `SpeechConfig` instância:
```csharp
var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
```
```cpp
auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
```
```java
SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
```

> [!TIP]
> Os parâmetros de consulta especificados no URI do ponto de extremidade não são alterados, mesmo que sejam definidos por outras APIs. Por exemplo, se o idioma de reconhecimento for definido no URI como parâmetro de consulta "Language = en-US" e também for definido como "ru-RU" por meio da propriedade correspondente, a configuração de idioma no URI será usada e o idioma efetivo será "en-US". Os parâmetros definidos no URI do ponto de extremidade sempre usam precidence. Somente os parâmetros que não são especificados no URI do ponto de extremidade podem ser substituídos por outras APIs.

Após essa modificação, seu aplicativo deve funcionar com os recursos de fala habilitados para privado. Estamos trabalhando em um suporte mais contínuo do cenário de ponto de extremidade privado.

### <a name="use-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Usar o recurso de fala com o nome de domínio personalizado sem pontos de extremidade privados

Neste artigo, vimos várias vezes, que habilitar o domínio personalizado para um recurso de fala é **irreversível** , e esse recurso usará uma maneira diferente de se comunicar com os serviços de fala comparando-os com os "usuais" (ou seja, que estão usando [nomes de ponto de extremidade regionais](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)).

Esta seção explica como usar um recurso de fala com o nome de domínio personalizado habilitado, mas **sem** nenhum ponto de extremidade privado com a API REST dos serviços de fala e o [SDK de fala](speech-sdk.md). Esse pode ser um recurso que era usado em um cenário de ponto de extremidade privado, mas, em seguida, tinha seus pontos de extremidade particulares excluídos.

#### <a name="dns-configuration"></a>Configuração de DNS

Lembre-se de como um nome DNS de domínio personalizado do recurso de fala habilitado do ponto de extremidade privado é [resolvido de redes públicas](#resolve-dns-from-other-networks). Nesse caso, o endereço IP resolvido aponta para um ponto de extremidade de proxy de VNet, que é usado para distribuir o tráfego de rede para o recurso de serviços cognitivas habilitados para ponto de extremidade privado.

No entanto, quando **todos os** pontos de extremidade privados do recurso forem removidos (ou logo após a habilitação do nome de domínio personalizado), o registro CNAME do recurso de fala será reprovisionado e apontará para o endereço IP do [ponto de extremidade regional dos serviços cognitivas](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Portanto, a saída do `nslookup` comando terá a seguinte aparência:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Compare-o com a saída [desta seção](#resolve-dns-from-other-networks).

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Recurso de fala com nome de domínio personalizado sem pontos de extremidade privados. Uso com a API REST

##### <a name="speech-to-text-rest-api-v30"></a>API REST de conversão de fala em texto v 3.0

O uso da API REST de fala em texto v 3.0 é totalmente equivalente ao caso de [recursos de fala habilitados para ponto de extremidade privado](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST de fala em texto para API REST de áudio curto e de conversão de texto em fala

Nesse caso, a API REST de fala em texto para o uso da API REST de áudio curto e de conversão de texto em fala não tem nenhuma diferença para o caso geral, com uma exceção para a API REST de conversão de fala em texto para áudio curto (veja a observação abaixo). Ambas as APIs devem ser usadas conforme descrito em [API REST de fala para texto para documentação de API REST de áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) e de [conversão de texto em fala](rest-text-to-speech.md) .

> [!NOTE]
> Ao usar a **API REST de fala em texto para um áudio curto** em cenários de domínio personalizado, use um token de autorização [passado pelo](rest-speech-to-text.md#request-headers) `Authorization` [cabeçalho](rest-speech-to-text.md#request-headers). Passar a chave de assinatura de fala para o ponto de extremidade especial por meio do `Ocp-Apim-Subscription-Key` cabeçalho **não** funcionará e gerará o erro 401.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Recurso de fala com nome de domínio personalizado sem pontos de extremidade privados. Uso com o SDK de fala

Usando o SDK de fala com o nome de domínio personalizado, os recursos de fala habilitados **sem** pontos de extremidade privados exigem a revisão e as alterações prováveis do código do aplicativo. Observe que essas alterações são comparadas **diferentes** ao caso de um [recurso de fala habilitado para ponto de extremidade privado](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Estamos trabalhando em um suporte mais contínuo de cenário de ponto de extremidade privado/domínio personalizado.

Usaremos `my-private-link-speech.cognitiveservices.azure.com` como um nome DNS de recurso de fala de exemplo (domínio personalizado) para esta seção.

Na seção sobre o [recurso de fala habilitado para ponto de extremidade privado](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) , explicamos como determinar a URL do ponto de extremidade usada, modificá-lo e fazê-lo funcionar por meio da inicialização "do ponto de extremidade"/"com ponto de extremidade" da `SpeechConfig` instância de classe.

No entanto, se você tentar executar o mesmo aplicativo depois de ter todos os pontos de extremidade privados removidos (permitindo algum tempo para o registro de DNS do correspondente, você obterá um erro de serviço interno (404). O motivo é o [registro DNS](#dns-configuration) que agora aponta para o ponto de extremidade de serviços cognitivas regionais em vez do proxy VNet, e os caminhos de URL como `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` não serão encontrados lá, portanto, o erro "não encontrado" (404).

Se você "reverta" seu aplicativo para a instanciação "padrão" do `SpeechConfig` no estilo de
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
seu aplicativo será encerrado com o erro de autenticação (401).

##### <a name="modifying-applications"></a>Modificando aplicativos

Para permitir que seu aplicativo use um recurso de fala com um nome de domínio personalizado e sem pontos de extremidade privados, siga estas etapas:

**1. solicitar o token de autorização da API REST dos serviços cognitivas**

[Este artigo](../authentication.md#authenticate-with-an-authentication-token) mostra como obter o token usando a API REST de serviços cognitivas.

Use seu nome de domínio personalizado na URL do ponto de extremidade, que está em nosso exemplo, essa URL é:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Você pode encontrar essa URL em portal do Azure. Na página de recursos de fala, sob o no grupo **Gerenciamento de recursos** , selecione **chaves e ponto de extremidade**.

**2. Crie uma `SpeechConfig` instância usando o método "do token de autorização"/"com o token de autorização".**

Crie uma `SpeechConfig` instância usando o token de autorização obtido na seção anterior. Suponha que tenhamos as seguintes variáveis definidas:

- `token`: o token de autorização obtido na seção anterior
- `azureRegion`: o nome da [região](regions.md) de recursos de fala (exemplo: `westeurope` )
- `outError`: (somente para o caso do [objetivo C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) )

Em seguida, crie uma `SpeechConfig` instância:

```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> O chamador precisa garantir que o token de autorização seja válido.
> Antes que o token de autorização expire, o chamador precisa atualizá-lo chamando esse setter com um novo token válido.
> À medida que os valores de configuração são copiados ao criar um novo reconhecedor ou sintetizador, o novo valor de token não se aplicará a reconhecedores ou sintetizadores que já foram criados.
> Para isso, defina o token de autorização do reconhecedor ou sintetizador correspondente para atualizar o token.
> Se você não atualizar o token, o reconhecedor ou sintetizador encontrará erros durante a operação.

Após essa modificação, seu aplicativo deve funcionar com recursos de fala que usam um nome de domínio personalizado sem pontos de extremidade privados.

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Saiba mais

* [Link Privado do Azure](../../private-link/private-link-overview.md)
* [SDK da fala](speech-sdk.md)
* [API REST de conversão de fala em texto](rest-speech-to-text.md)
* [API REST conversão de texto em fala](rest-text-to-speech.md)
