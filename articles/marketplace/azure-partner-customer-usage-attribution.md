---
title: Atribuição de parceiro si e uso do cliente do Mercado Comercial
description: Obtenha uma visão geral do rastreamento do uso do cliente para as soluções do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e061baa8e7eb73bae0a78f4215f0d48610e85fcb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686743"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Atribuição de parceiro si e uso do cliente do Mercado Comercial

A atribuição de uso do cliente é um método para associar os recursos do Azure em execução em assinaturas de clientes, implantados para executar sua solução, com você como parceiro. Formar essas associações em sistemas internos da Microsoft traz maior visibilidade à pegada do Azure executando seu software. Quando você adota esse recurso de rastreamento, você se alinha com as equipes de vendas da Microsoft e ganha crédito para programas de parceiros da Microsoft.

Você pode formar a associação através do Azure Marketplace, do repositório Quickstart, dos repositórios privados do GitHub e dos engajamentos de clientes 1:1 que criam IP duráveis (como o desenvolvimento de um aplicativo).

A atribuição de uso do cliente suporta três opções de implantação:

- Modelos do Azure Resource Manager: Os parceiros podem usar modelos do Gerenciador de Recursos para implantar os serviços do Azure para executar o software do parceiro. Os parceiros podem criar um modelo do Resource Manager para definir a infraestrutura e a configuração de sua solução do Azure. Um modelo do Resource Manager permite que você e seus clientes implantem sua solução em todo o seu ciclo de vida. Você pode ter certeza de que seus recursos são implantados em um estado consistente.
- APIs do Azure Resource Manager: os parceiros podem chamar as APIs do Resource Manager diretamente para implantar um modelo do Resource Manager ou gerar as chamadas da API para provisionar diretamente os serviços do Azure.
- Terraform: Os parceiros podem usar o Terraform para implantar um modelo de Gerenciador de Recursos ou implantar diretamente os serviços do Azure.

>[!IMPORTANT]
>- A atribuição de uso do cliente não se destina a acompanhar o trabalho de integradores de sistemas, provedores de serviços gerenciados ou ferramentas projetadas para implantar e gerenciar softwares em execução no Azure.
>
>- A atribuição de uso do cliente é para novas implantações e NÃO suporta a marcação de recursos existentes que já foram implantados.
>
>- A atribuição de uso do cliente é necessária para as ofertas [do Azure Application](./partner-center-portal/create-new-azure-apps-offer.md) publicadas no Azure Marketplace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Criar GUIDs

Um GUID é um identificador de referência único que tem 32 dígitos hexadecimais. Para criar GUIDs para rastreamento, você deve usar um gerador de GUID. A equipe do Armazenamento do Azure criou um [formulário gerador de GUID](https://aka.ms/StoragePartners) que enviará a você por email um GUID no formato correto e que pode ser reutilizado entre os sistemas de controle diferentes.

> [!NOTE]
> É altamente recomendável que você use [o formulário de gerador GUID do Azure Storage](https://aka.ms/StoragePartners) para criar o seu GUID. Para mais informações, consulte nossas [FAQ](#faq).

Recomendamos que você crie um GUID exclusivo para cada oferta e canal de distribuição para cada produto. Você pode optar por usar um único GUID para os vários canais de distribuição do produto se não quiser que os relatórios sejam divididos.

Se você implantar um produto usando um modelo e ele estiver disponível tanto no Azure Marketplace quanto no GitHub, você pode criar e registrar dois GUIDS distintos:

- Produto A no Azure Marketplace
- Produto A no GitHub

O relatório é feito pelo Microsoft Partner Network ID e GUID.

Você também pode rastrear o uso em um nível mais granular registrando GUIDs adicionais e alterando GUIDs entre planos, onde os planos são variantes de uma oferta.

## <a name="register-guids"></a>Registre GUIDs

Os GUIDs devem ser registrados no Partner Center para permitir a atribuição de uso do cliente.

Depois de adicionar um GUID ao seu modelo ou no agente do usuário e registrar o GUID no Partner Center, futuras implantações serão rastreadas.

1. Faça login no [Partner Center](https://partner.microsoft.com/dashboard).

1. Inscreva-se como [um editor de marketplace comercial.](https://aka.ms/JoinMarketplace)

   * Os parceiros são obrigados a [ter um perfil no Partner Center](https://docs.microsoft.com/azure/marketplace/become-publisher). Você é incentivado a listar a oferta no Azure Marketplace ou no AppSource.
   * Parceiros podem registrar vários GUIDs.
   * Os parceiros podem registrar GUIDs para modelos e ofertas de soluções não-marketplace.

1. No canto superior direito, selecione o ícone de engrenagem de configurações e selecione **Configurações do Desenvolvedor**.

1. Na **página Configurações**da conta, selecione **Adicionar GUIA de rastreamento.**

1. Na caixa **GUID,** digite seu GUID de rastreamento. Insira o GUID sem as **pid -** prefixo. Na caixa **Descrição,** digite seu nome ou descrição da oferta.

1. Para registrar mais de um GUID, selecione **Adicionar GUID de Rastreamento** novamente. Caixas adicionais aparecerão na página.

1. Clique em **Salvar**.

## <a name="use-resource-manager-templates"></a>Use modelos do Gerenciador de Recursos
Muitas soluções de parceiros são implantadas usando modelos do Azure Resource Manager. Se você tiver um modelo de Gerenciador de recursos disponível no Azure Marketplace, no GitHub ou como um Quickstart, o processo para modificar seu modelo para permitir a atribuição de uso do cliente é direto.

> [!NOTE]
> Para saber mais sobre como criar e publicar Modelos de Solução, confira
> * [Crie e implante seu primeiro modelo de Gerenciador de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
>* [Oferta de aplicação do Azure](./partner-center-portal/create-new-azure-apps-offer.md).
>* Vídeo: [Construindo modelos de soluções e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Para adicionar um identificador global exclusivo (GUID), você faz uma única modificação no arquivo de modelo principal:

1. [Crie um GUID](#create-guids) usando o método sugerido e [registre o GUID](#register-guids).

1. Abra o modelo do Resource Manager.

1. Adicione um novo recurso no arquivo de modelo principal. O recurso precisa estar no arquivo **mainTemplate.json** ou **azuredeploy.json**, e não em nenhum modelo aninhado ou vinculado.

1. Digite o valor de GUID após o prefixo **pid-** (por exemplo, pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Verifique o modelo para todos os erros.

1. Republique o modelo nos repositórios apropriados.

1. [Verificar o sucesso da GUID na implantação de modelo](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Exemplo de código de modelo do Resource Manager

Para habilitar recursos de acompanhamento para o modelo, você precisará adicionar o recurso adicional a seguir na seção de recursos. Não se esqueça de modificar o exemplo código abaixo com suas próprias entradas ao adicioná-lo ao arquivo de modelo principal.
O recurso precisa ser adicionado ao arquivo **mainTemplate.json** ou **azuredeploy.json**, e não em nenhum modelo aninhado ou vinculado.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>Use as APIs do Gerenciador de recursos

Em alguns casos, talvez você prefira fazer chamadas diretamente contra as APIs REST do Resource Manager para implantar os serviços do Azure. [Azure dá suporte a vários SDKs](https://docs.microsoft.com/azure/?pivot=sdkstools) para habilitar essas chamadas. Você pode usar um dos SDKs, ou chamar as APIs REST diretamente para implantar recursos.

Se você estiver usando um modelo do Resource Manager, deverá marcar sua solução seguindo as instruções descritas anteriormente. Se você não estiver usando um modelo do Resource Manager e fazendo chamadas diretas à API, ainda poderá marcar sua implantação para associar o uso de recursos do Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Marcar uma implantação com as APIs do Resource Manager

Para habilitar a atribuição de uso do cliente, ao projetar suas chamadas de API, inclua um GUID no cabeçalho do agente de usuário na solicitação. Adicione o GUID para cada oferta ou SKU. Formatar a cadeia de caracteres com o **pid -** de prefixo e incluir o GUID gerado pelo parceiro. Aqui está um exemplo do formato GUID para inserção no agente do usuário:

![Formato GUID de exemplo](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> O formato da cadeia de caracteres é importante. Se o prefixo **pid-** não estiver incluído, não será possível consultar os dados. Diferentes SDKs acompanham de forma diferente. Para implementar esse método, revise a abordagem de suporte e acompanhamento do seu SDK do Azure preferido.

#### <a name="example-the-python-sdk"></a>Exemplo: O SDK do Python

Para o Python, use o **config** atributo. Você só pode adicionar o atributo a um UserAgent. Aqui está um exemplo:

![Adicione o atributo para um agente do usuário](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Adicione o atributo para cada cliente. Não há nenhuma configuração estática global. Você pode marcar uma fábrica de clientes para ter certeza de que todos os clientes estão rastreando. Para obter mais informações, consulte este [exemplo de fábrica do cliente no GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Marca uma implantação usando o Azure PowerShell

Se você implantar recursos por meio do Azure PowerShell, acrescente seu GUID usando o seguinte método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Marca uma implantação usando a CLI do Azure

Quando você usa a CLI do Azure para anexar seu GUID, defina a variável de ambiente **AZURE_HTTP_USER_AGENT**. Você pode definir essa variável dentro do escopo de um script. Você também pode definir a variável globalmente para o escopo da shell:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Para obter mais informações, consulte [Azure SDK for Go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Use terraforme

O suporte para terraform está disponível através da versão 1.21.0 do Azure Provider: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Esse suporte se aplica a todos os parceiros que implantam sua solução via Terraform, e todos os recursos implantados e medidos pelo Provedor Azure (versão 1.21.0 ou posterior).

O provedor do Azure para terraform adicionou um novo campo opcional chamado [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) que é onde você especifica o GUID de rastreamento que você usa para sua solução. O valor deste campo também pode ser originado da *variável ambiente ARM_PARTNER_ID.*

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Os parceiros que desejam obter sua implantação via Terraform rastreado pela atribuição de uso do cliente precisam fazer o seguinte:

* Crie um GUID (o GUID deve ser adicionado para cada Oferta ou SKU)
* Atualize seu Provedor Azure para definir o valor de *partner_id* para o GUID (NÃO pré-fixar o GUID com "pid-", basta configurá-lo para o GUID real)


## <a name="verify-the-guid-deployment"></a>Verificar a implantação de GUID

Depois de modificar seu modelo e executar uma implantação de teste, use o seguinte script do PowerShell para recuperar os recursos implantados e marcados.

Você pode usar o script para verificar se o GUID foi adicionado com êxito ao seu modelo do Resource Manager. O script não se aplica às implantações de API ou Terraform do Gerenciador de recursos.

Entre no Azure. Selecione a assinatura com a implantação que você deseja verificar antes de executar o script. Execute o script dentro do contexto de assinatura da implantação.

O **GUIDE** e o **grupo de recursos** nome da implantação são parâmetros obrigatórios.

Você pode obter [o script original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) no GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Relatório

Você pode encontrar o relatório para atribuição de uso[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)do cliente no painel do Partner Center ( ). Para ver o relatório, você tem que usar suas credenciais do Partner Center para fazer login. Se você encontrar quaisquer problemas com relatório ou login, crie uma solicitação de suporte após a instrução na seção Obter suporte.

Escolha Modelo rastreado na lista suspensa do Tipo de Associação de Parceiros para ver o relatório.

![Relatório para atribuição de uso do cliente](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Notificar os clientes

Os parceiros devem informar seus clientes sobre implantações que usam a atribuição de uso do cliente. A Microsoft relata o uso do Azure associado a essas implantações no parceiro. Os exemplos a seguir incluem conteúdo que você pode usar para notificar seus clientes sobre essas implantações. Nos exemplos, substitua \<parceiro > com o nome da sua empresa. Os parceiros devem certificar-se de que a notificação esteja alinhada com suas políticas de privacidade e coleta de dados, incluindo opções para os clientes serem excluídos do rastreamento.

### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implantações de modelos do Resource Manager

Quando você implanta esse modelo, a Microsoft consegue identificar a instalação do software \<PARTNER> com os recursos do Azure implantados. Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e regidos pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implantações de SDK ou a API

Quando você implanta o software \<PARTNER>, a Microsoft pode identificar a instalação do software \< PARTNER> com os recursos do Azure implantados. Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e regidos pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Obtenha suporte

Existem dois canais de suporte dependendo dos problemas que você está enfrentando.

Se você encontrar algum problema na Central de Parceiros, como ver o relatório de atribuição de uso do cliente ou fazer login, crie uma solicitação de suporte com a equipe de suporte do Partner Center aqui:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Se você precisar de assistência para a atribuição de uso do Marketplace Onboarding e/ou do cliente em geral, como como configurar a atribuição de uso do cliente, siga os passos abaixo:

1. Vá para a [página de suporte](https://go.microsoft.com/fwlink/?linkid=844975).

1. Em **Tipo de problema**, selecione **Onboarding do Marketplace**.

1. Escolha o **categoria** para seu problema:

   - Para problemas de uso de associação, selecione **outros**.
   - Para problemas de acesso com o Azure Marketplace, selecione **Problema de acesso**.

     ![Escolha a categoria do problema](media/marketplace-publishers-guide/lu-article-incident.png)

1. Selecione **Iniciar solicitação**.

1. Na próxima página, insira os valores necessários. Selecione **Continuar**.

1. Na próxima página, insira os valores necessários.

   > [!IMPORTANT]
   > Na caixa **Título do incidente**, insira **Acompanhamento de uso do ISV**. Descreva seu problema em detalhes.

   ![Insira o controle de uso do ISV para o título do incidente](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Preencha o formulário e selecione **Enviar**.

Você também pode receber orientação técnica de um Consultor Técnico de Parceiros da Microsoft para cenários técnicos de pré-venda, implantação e desenvolvimento de aplicativos para entender e incorporar a atribuição de uso do cliente.

### <a name="how-to-submit-a-technical-consultation-request"></a>Como enviar um pedido de consulta técnica

1. Visite. [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney)
1. Selecione infra-estrutura e gerenciamento em nuvem e uma nova página será aberta para você ver a jornada técnica.
1. Em Serviços de implantação, clique no botão Enviar uma solicitação
1. Faça login usando sua conta MSA (MPN) ou sua AAD (Conta do Painel de Parceiros); com base em suas credenciais de login, um formulário de solicitação on-line será aberto:
    * Complete/revise as informações de contato.
    * Os detalhes da consulta podem ser pré-preenchidos ou selecionados entre as gotas.
    * Digite um título e a descrição do problema (forneça o máximo de detalhes possível).
1. Clique em Enviar

Exibir instruções passo a passo [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)com capturas de tela em .

### <a name="whats-next"></a>O que vem a seguir

Você será contatado por um consultor técnico do Parceiro Microsoft para configurar uma chamada para escopo de suas necessidades.

## <a name="faq"></a>Perguntas frequentes

**Qual é o benefício de adicionar o GUIA ao modelo?**

A Microsoft fornece aos parceiros uma visão das implantações dos clientes de suas soluções e insights sobre seu uso influenciado. Tanto a Microsoft quanto o parceiro podem usar essas informações para promover um envolvimento mais próximo entre as equipes de vendas. Tanto a Microsoft quanto o parceiro podem usar os dados para obter uma visão mais consistente do impacto de um parceiro individual no crescimento do Azure.

**Depois de um GUID é adicionado, ela pode ser alterada?**

Sim, um cliente ou parceiro de implementação pode personalizar o modelo e alterar ou remover o GUID. Sugerimos que os parceiros descrevam proativamente o papel do recurso e guid para seus clientes e parceiros para evitar a remoção ou edições para o GUID. Alterar o GUID afeta somente implantações e recursos novos e não existentes.

**Posso acompanhar modelos implantados de um repositório não Microsoft como o GitHub?**

Sim, desde que o GUID esteja presente quando o modelo for implantado, o uso será rastreado. Os parceiros ainda devem registrar seus GUIDs.

**O cliente que recebe a emissão de relatórios também?**

Os clientes podem acompanhar seu uso de recursos individuais ou grupos de recursos definido pelo cliente no portal do Azure. Os clientes não vêem o uso quebrado pela GUID.

**Essa metodologia é semelhante ao Parceiro Digital de Registro (DPOR)?**

Esse novo método de conexão com a implantação e o uso de solução de um parceiro fornece um mecanismo para vincular a uma solução de parceiro ao uso do Azure. A DPOR destina-se a associar um parceiro de consultoria (Integrador de Sistemas) ou de gerenciamento (Provedor de Serviço Gerenciado) à assinatura do Azure de um cliente.

**Qual é o benefício de usar o formulário Gerador de GUID do Armazenamento do Azure?**

O formulário Gerador de GUID do Armazenamento do Azure tem a garantia de gerar um GUID do formato necessário. Além disso, se você estiver usando qualquer um dos métodos de rastreamento de plano de dados do Armazenamento do Azure, poderá aproveitar o mesmo GUID para o rastreamento do plano de controle do Marketplace. Isso permite que você aproveite um único GUIDE unificado para atribuição de parceiros sem precisar manter GUIDES separados.

**Posso usar um VHD privado e personalizado para uma oferta de modelo de solução no Azure Marketplace?**

Não, você não pode. A imagem da máquina virtual deve vir do [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)Azure Marketplace, veja: .

Você pode criar uma oferta de VM no mercado usando seu VHD personalizado e marcá-lo como Private para que ninguém possa vê-lo. Em seguida, consulte este VM em seu modelo de solução.

**Falha ao atualizar a propriedade *contentVersion* para o modelo principal?**

Provavelmente um bug em alguns casos quando o modelo está sendo implantado usando um TemplateLink de outro modelo que espera conteúdo mais antigoVersão por algum motivo. A solução é usar a propriedade de metadados:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
