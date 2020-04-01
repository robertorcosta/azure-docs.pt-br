---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419207"
---
Imagens padronizadas de máquinavirtual (VM) permitem que as organizações migrem para a nuvem e garantam consistência nas implantações. As imagens normalmente incluem configurações de segurança e configuração predefinidas e software necessário. Configurar seu próprio pipeline de imagem requer tempo, infra-estrutura e configuração, mas com o Azure VM Image Builder, basta fornecer uma configuração simples descrevendo sua imagem, enviá-la para o serviço e a imagem é construída e distribuída.
 
O Azure VM Image Builder (Azure Image Builder) permite que você comece com uma imagem do Azure Marketplace baseada no Windows ou Linux, imagens personalizadas existentes ou ISO Red Hat Enterprise Linux (RHEL) e comece a adicionar suas próprias personalizações. Como o Construtor de Imagens é construído no [HashiCorp Packer,](https://packer.io/)você também pode importar os scripts existentes do provisionador de conchas Packer. Você também pode especificar onde gostaria que suas imagens fosse hospedadas, na [Azure Shared Image Gallery,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)como uma imagem gerenciada ou um VHD.

> [!IMPORTANT]
> O Azure Image Builder está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Versão prévia dos recursos

Para a visualização, esses recursos são suportados:

- Criação de imagens de linha de base dourada, que inclui sua segurança mínima e configurações corporativas, e permite que os departamentos personalizem-na ainda mais para suas necessidades.
- Patches das imagens existentes, o Image Builder permitirá que você remende continuamente as imagens personalizadas existentes.
- Conecte o construtor de imagens às suas redes virtuais existentes, para que você possa se conectar a servidores de configuração existentes (DSC, Chef, Puppet etc.), compartilhamentos de arquivos ou quaisquer outros servidores/serviços roteáveis.
- A integração com a Azure Shared Image Gallery permite distribuir, visualizar e dimensionar imagens globalmente e oferece um sistema de gerenciamento de imagens.
- Integração com os pipelines de compilação de imagens existentes, basta chamar o Image Builder do seu pipeline ou usar a simples tarefa de desfilamento de devOps do Azure Dozure.
- Migre um pipeline de personalização de imagem existente para o Azure. Use seus scripts, comandos e processos existentes para personalizar imagens.
- Criação de imagens em formato VHD.
 

## <a name="regions"></a>Regiões
O Azure Image Builder Service estará disponível para visualização nessas regiões. As imagens podem ser distribuídas fora dessas regiões.
- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Norte da Europa
- Europa Ocidental

## <a name="os-support"></a>Suporte ao SO
A AIB suportará imagens da base de sO do Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CENTOS 7.6, 7.7
- Less12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multi-sessão/Profissional
- Windows 2016
- Windows 2019

O suporte aos ISOs da RHEL está sendo preterido, por favor, revise a documentação do Template para obter mais detalhes.

## <a name="how-it-works"></a>Como ele funciona


![Desenho conceitual do Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

O Azure Image Builder é um serviço Azure totalmente gerenciado que é acessível por um provedor de recursos do Azure. O processo do Azure Image Builder tem três partes principais: fonte, personalização e distribuição, estas são representadas em um modelo. O diagrama abaixo mostra os componentes, com algumas de suas propriedades. 
 


**Processo de Construtor de Imagens** 

![Desenho conceitual do processo Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Crie o Modelo de Imagem como um arquivo .json. Este arquivo .json contém informações sobre a origem da imagem, personalizações e distribuição. Existem vários exemplos no [repositório GitHub do Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Submetê-lo ao serviço, isso criará um artefato Modelo de imagem no grupo de recursos especificado. Em segundo plano, o Image Builder baixará a imagem de origem ou ISO e os scripts conforme necessário. Eles são armazenados em um grupo de recursos separado que é\<criado automaticamente\<em sua assinatura, no formato: IT_ DestinationResourceGroup>_ TemplateName>. 
1. Uma vez que o Modelo de imagem é criado, você pode então construir a imagem. Em segundo plano, o Image Builder usa o modelo e os arquivos de origem para criar uma\<VM (tamanho padrão: Standard_D1_v2), rede, IP público, NSG e armazenamento no grupo de recursos IT_ DestinationResourceGroup>_\<TemplateName>.
1. Como parte da criação da imagem, o criador de imagens distribui a imagem\<de acordo\<com o modelo e, em seguida, exclui os recursos adicionais no IT_ DestinationResourceGroup>_ TemplateName> grupo de recursos criado para o processo.


## <a name="permissions"></a>Permissões

Para permitir que o Azure VM Image Builder distribua imagens para as imagens gerenciadas ou para uma Galeria de Imagens Compartilhadas, você precisará fornecer permissões 'Contribuinte' para o serviço "Azure Virtual Machine Image Builder" (ID do aplicativo: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) nos grupos de recursos. 

Se você estiver usando uma versão de imagem ou imagem gerenciada personalizada existente, o Azure Image Builder precisará de um mínimo de acesso 'Reader' a esses grupos de recursos.

Você pode atribuir acesso usando o Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Você pode atribuir acesso usando o PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Se a conta de serviço não for encontrada, isso pode significar que a assinatura onde você está adicionando a atribuição da função ainda não foi registrada para o provedor de recursos.


## <a name="costs"></a>Custos
Você incorrerá em alguns custos de computação, rede e armazenamento ao criar, construir e armazenar imagens com o Azure Image Builder. Esses custos são semelhantes aos custos incorridos na criação manual de imagens personalizadas. Pelos recursos, você será cobrado em suas taxas do Azure. 

Durante o processo de criação de imagens, os arquivos são baixados e armazenados no grupo de recursos, o `IT_<DestinationResourceGroup>_<TemplateName>` que incorrerá em um pequeno custo de armazenamento. Se você não quiser mantê-los, exclua o **Modelo de imagem** após a compilação da imagem.
 
O Image Builder cria uma VM usando um tamanho De VM D1v2, e o armazenamento e a rede necessários para a VM. Esses recursos durarão durante a duração do processo de compilação e serão excluídos assim que o Image Builder terminar de criar a imagem. 
 
O Azure Image Builder distribuirá a imagem para as regiões escolhidas, o que pode incorrer em cobranças de saída de rede.
 
## <a name="next-steps"></a>Próximas etapas 
 
Para testar o Azure Image Builder, consulte os artigos para construir imagens [Linux](../articles/virtual-machines/linux/image-builder.md) ou [Windows.](../articles/virtual-machines/windows/image-builder.md)
 
 
