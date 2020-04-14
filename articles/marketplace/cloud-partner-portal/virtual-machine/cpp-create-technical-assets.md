---
title: Crie ativos técnicos para uma oferta de máquina virtual para o Azure Marketplace
description: Explica como criar os recursos técnicos para uma oferta de máquina virtual no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: a62af1d8d751d36150c236280077cde8f6547385
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273963"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Criar recursos técnicos para uma oferta de máquina virtual

> [!IMPORTANT]
> A partir de 13 de abril de 2020, iniciaremos a gestão móvel das ofertas da Sua Máquina Virtual Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [Criar seus ativos técnicos da Máquina Virtual do Azure](https://aka.ms/AzureVMTechAsset) para gerenciar suas ofertas migradas.

Esta seção irá guiá-lo na criação e configuração dos recursos técnicos para uma oferta de VM (máquina virtual) do Azure Marketplace.  Uma VM contém dois componentes: o VHD (disco rígido virtual) da solução e os discos de dados associados opcionais.  

- *VHDs (discos rígidos virtuais)*, contendo o sistema operacional e a solução que você implantará com a oferta do Azure Marketplace. O processo de preparação de VHD será diferente dependendo se é uma VM baseada em Linux, baseada em Windows ou em uma VM personalizada.
- *Discos de dados* representam armazenamento dedicado e persistente para uma máquina virtual. *Não* use a solução de VHD (por exemplo, a unidade) `C:` para armazenar informações persistentes.

Uma imagem de VM contém um disco de sistema operacional e zero ou mais discos de dados. É necessário um VHD por disco. Mesmo discos de dados em branco exigem a criação de um VHD.
É necessário configurar o sistema operacional da VM, o tamanho da VM, as portas a serem abertas e até 15 discos de dados conectados.

> [!TIP] 
> Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários para a SKU. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação. 

> [!IMPORTANT]
> *Não altere a contagem de discos em uma nova versão da imagem.*  Se você precisar reconfigurar os discos de Dados da imagem, defina um novo SKU. A publicação de uma nova versão de imagem com diferentes contagens de disco terá o potencial de quebrar a nova implantação com base na nova versão de imagem em casos de escalonamento automático, implantações automáticas de soluções por meio de modelos do Azure Resource Manager e outros cenários.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar esses ativos leva tempo e requer conhecimento técnico tanto da plataforma Azure quanto das tecnologias usadas para construir a oferta. Além do domínio da solução, sua equipe de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft: 
-    Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/) 
-    Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
-    Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
-    Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-    Conhecimento prático de [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Ferramentas sugeridas 

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar VHDs e VMs:
-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao ambiente de desenvolvimento: 

-    [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Também é recomendável que você revise as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/tools/) e, se estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Próximas etapas

Os artigos subsequentes desta seção irão orientá-lo nas etapas de criação e registro desses recursos de VM:

1. [Criar um disco rígido virtual compatível com Azure](./cpp-create-vhd.md) explica como criar um VHD baseado em Linux ou Windows compatível com Azure.  Ele inclui as melhores práticas como dimensionamento, aplicação de patch e preparação da VM para upload.

2. [Conectar-se à máquina virtual ](./cpp-connect-vm.md) explica como conectar-se remotamente à sua VM recém-criada e entrar nela.  Este artigo também explica como parar a VM para economizar nos custos de uso.

3. [Configurar a máquina virtual](./cpp-configure-vm.md) explica como escolher o tamanho correto do VHD, generalizar a imagem, aplicar atualizações recentes (patches) e programar configurações personalizadas.

4. [Implantar uma máquina virtual a partir de um disco rígido virtual](./cpp-deploy-vm-vhd.md) explica como registrar uma VM a partir de um VHD implantado no Azure.  Ele lista as ferramentas necessárias e como usá-las para criar uma imagem de VM do usuário e implantá-la no Azure usando o [portal do Microsoft Azure](https://ms.portal.azure.com/) ou scripts do PowerShell. 

5. [Certificar uma imagem de máquina virtual](./cpp-certify-vm.md) explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace. Explica onde obter a ferramenta *Ferramenta de Teste de Certificação para Certificado do Azure* e como usar essa ferramenta para certificar a imagem de VM. 

6. [Obter URI de SAS](./cpp-get-sas-uri.md) explica como obter o URI de SAS (Assinatura de Acesso Compartilhado) para as imagens de VM.
 
Como um artigo de suporte, [Problemas comuns de URL de assinatura de acesso compartilhado](./cpp-common-sas-url-issues.md) lista alguns problemas comuns que você pode encontrar usando URIs de SAS e as possíveis soluções correspondentes.

Após concluir todas essas etapas, você estará pronto para [publicar sua oferta de VM](./cpp-publish-offer.md) no Azure Marketplace.
