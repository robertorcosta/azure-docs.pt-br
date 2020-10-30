---
title: Criar planos para uma oferta de máquina virtual no Azure Marketplace
description: Saiba como criar planos para uma oferta de máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: bc5e98484560fcc15e0ea3e289069c84687f158c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040521"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Como criar planos para uma oferta de máquina virtual

Você pode fornecer uma variedade de opções de plano dentro da mesma oferta no Partner Center. Uma oferta requer pelo menos um plano (anteriormente chamado de SKU), que pode variar de acordo com o público monetização, a região do Azure, os recursos ou as imagens de VM.

Você pode criar até 100 planos para cada oferta: até 45 deles podem ser privados. Saiba mais sobre planos privados em [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

Depois de criar seus planos, selecione a guia **Visão geral do plano** para exibir:

- Nomes de plano
- Modelos de licença
- Público-alvo (público ou privado)
- Status da publicação atual
- Ações disponíveis

As ações disponíveis no painel **Visão geral do plano** variam de acordo com o status atual do plano.

- Se o status do plano for um rascunho, selecione **Excluir rascunho** .
- Se o status do plano for publicado e estiver ativo, selecione **Interromper o plano de venda** ou **Sincronizar público-alvo privado** .

## <a name="create-a-new-plan"></a>Criar um plano

Selecione **Criar plano** na parte superior. A caixa de diálogo **Novo plano** é exibida.

Na caixa **ID do plano** , crie uma ID de plano exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes no endereço Web do produto. Use apenas letras minúsculas e números, traços ou sublinhados e, no máximo, 50 caracteres.

> [!NOTE]
> A ID do plano não poderá ser alterada depois que você selecionar a opção **Criar** .

Na caixa **Nome do plano** , insira um nome para esse plano. Os clientes veem esse nome quando estão decidindo qual plano selecionar dentro de sua oferta. Crie um nome exclusivo que indica claramente as diferenças entre os planos. Por exemplo, você pode inserir os planos **Windows Server** com *Pago conforme o uso* , *BYOL* , *Avançado* e *Enterprise* .

Selecione **Criar** .

## <a name="plan-setup"></a>Configuração do plano

Defina a configuração de alto nível para o tipo de plano, especifique se ele reutiliza uma configuração técnica de outro plano e identifique as regiões do Azure em que o plano deve estar disponível. Suas seleções aqui determinam quais campos são exibidos em outros painéis para o mesmo plano.

### <a name="reuse-a-technical-configuration"></a>Reutilizar uma configuração técnica

Se você tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, você poderá selecionar **Este plano reutiliza a configuração técnica de outro plano** . Essa opção permite selecionar um dos outros planos do mesmo tipo para essa oferta e permite que você reutilize a configuração técnica dele.

> [!NOTE]
> Quando você reutiliza a configuração técnica de outro plano, toda a guia de **Configuração técnica** desaparece desse plano. Os detalhes de configuração técnica do outro plano, incluindo todas as atualizações que você fizer no futuro, também serão usadas para esse plano. Essa configuração não pode ser alterada após a publicação do plano.

### <a name="azure-regions"></a>Regiões do Azure

Seu plano precisa ser disponibilizado em pelo menos uma região do Azure.

Selecione a opção **Azure Global** para disponibilizar seu plano para clientes em todas as regiões do Azure Global que têm integração ao marketplace comercial. Para obter mais informações, confira [Disponibilidade geográfica e suporte a moeda](marketplace-geo-availability-currencies.md).

Selecione a opção **Azure Government** para disponibilizar seu plano na região [Azure Government](../azure-government/documentation-government-welcome.md). Essa região fornece acesso controlado para clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como para parceiros qualificados para atendê-los. Você, como editor, é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas. O Azure Government usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA).

Antes de publicar para o [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), teste e valide seu plano no ambiente, pois certos pontos de extremidade podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de avaliação na página de [Avaliação do Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois que o plano for publicado e estiver disponível em uma região específica do Azure, você não poderá remover essa região.

### <a name="azure-government-certifications"></a>Certificações do Azure Government

Essa opção só será visível se você tiver selecionado **Azure Government** como a região do Azure na seção anterior.

Os serviços do Azure Government manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para oferecer reconhecimento das suas certificações para esses programas, você pode fornecer até 100 links que as descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus sites. Esses links são visíveis somente para clientes do Azure Government.

Selecione **Salvar rascunho** para continuar.

## <a name="plan-listing"></a>Listagem de planos

Nesta seção, você configurará os detalhes da listagem do plano. Esse painel exibe informações específicas, que podem ser diferentes de outros planos na mesma oferta.

### <a name="plan-name"></a>Nome do plano

Esse campo é preenchido automaticamente com o nome que você atribuiu ao seu plano quando o criou. Esse nome aparece no Azure Marketplace como o título deste plano. Ele é limitado a 100 caracteres.

### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano, não da oferta. Este resumo é limitado a 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software exclusivo e descreva as diferenças entre os planos em sua oferta. Descreva apenas o plano, não a oferta. A descrição do plano pode conter até 2.000 caracteres.

Selecione **Salvar rascunho** para continuar.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Neste painel, você configura:

- Mercados em que esse plano está disponível. Cada plano deve estar disponível em pelo menos um [mercado](marketplace-geo-availability-currencies.md).
- O preço por hora.
- Se deseja tornar o plano visível para todos ou apenas para clientes específicos (um público-alvo privado).

### <a name="markets"></a>Mercados

Cada plano precisa estar disponível em pelo menos um mercado. Marque a caixa de seleção para cada localização do mercado em que esse plano deve estar disponível para compra. (Os usuários nesses mercados ainda podem implantar a oferta em todas as regiões do Azure selecionadas na seção ["Configuração do plano"](#plan-setup).) O botão **Imposto Remetido** mostra países/regiões nos quais a Microsoft remete o imposto de vendas e uso em seu nome. A publicação na China é limitada a planos que são *Gratuitos* ou BYOL ( *traga sua própria licença* ).

Se você já tiver definido preços para seu plano em dólares americanos (US$) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Sempre examine o preço de cada mercado antes de publicar. Examine seus preços selecionando **Exportar preços (xlsx)** depois de salvar as alterações.

Quando você remove um mercado, os clientes desse mercado que estiverem usando implantações ativas não poderão criar implantações nem escalar verticalmente as implantações existentes deles. As implantações existentes não são afetadas.

### <a name="pricing"></a>Preços

Para o **Modelo de licença** , selecione **Plano faturado mensalmente baseado em uso** para configurar os preços para esse plano ou selecione **Traga sua própria licença** para permitir que os clientes usem esse plano com a licença existente deles.

Para um plano cobrado mensalmente baseado em uso, use uma das seguintes três opções de entrada de preços:

- **Por núcleo** : forneça preços por núcleo em US$. A Microsoft calcula o preço por tamanho de núcleo e o converte em moedas locais usando a taxa de câmbio atual.
- **Por tamanho de núcleo** : forneça preços por tamanho de núcleo em US$. A Microsoft calcula o preço e o converte em moedas locais usando a taxa de câmbio atual.
- **Por mercado e tamanho de núcleo** : forneça preços para cada tamanho de núcleo para todos os mercados. Você pode importar os preços de uma planilha.

> [!NOTE]
> Salve as alterações de preços para habilitar a exportação de dados sobre preços. Depois que um preço para um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente. Para garantir que os preços sejam adequados antes de publicá-los, exporte a planilha de preços e examine os preços em cada mercado.

### <a name="free-trial"></a>Avaliação gratuita

Você pode oferecer uma *avaliação gratuita* de um mês ou três meses ou de seis meses para seus clientes.

### <a name="visibility"></a>Visibilidade

Você pode criar cada plano para ser visível para todos ou apenas a um público-alvo previamente selecionado. Atribua associações neste público-alvo restrito usando as IDs da assinatura do Azure.

**Público** : seu plano pode ser visto por todos.

**Público-alvo privado** : torne seu plano visível apenas para um público-alvo pré-selecionado. Depois de ele ser publicado como um plano privado, você pode atualizar o público-alvo ou alterá-lo para público. Depois de você tornar um plano público, ele precisa permanecer como público. Ele não pode ser alterado de volta para um plano privado.

> [!NOTE]
> Um público-alvo privado ou restrito é diferente do público-alvo de versão prévia que você definiu no painel **Versão prévia** . Um público-alvo de versão prévia pode acessar sua oferta _antes_ de ela ser publicada como ativa no Azure Marketplace. Embora a escolha de público-alvo privado se aplique somente a um plano específico, o público-alvo da versão prévia pode ver todos os planos públicos e privados para fins de validação.

**Público-alvo restrito (IDs da assinatura do Azure)** : atribua o público-alvo que terá acesso a esse plano privado usando as IDs da assinatura do Azure. Opcionalmente, inclua uma descrição de cada ID de assinatura do Azure que você atribuiu. Adicione até dez IDs de assinatura manualmente ou até 20 mil IDs se você estiver importando uma planilha CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras precisam estar em minúsculas.

>[!Note]
>Não há suporte para ofertas privadas com assinaturas do Azure estabelecidas por meio de um revendedor do CSP (programa de provedor de soluções na nuvem).

### <a name="hide-a-plan"></a>Ocultar um plano

Se a máquina virtual for destinada a ser usada apenas indiretamente quando referenciada por outro modelo de solução ou aplicativo gerenciado, marque essa caixa de seleção para publicar a máquina virtual, mas ocultá-la de clientes que podem estar pesquisando ou procurando por ela diretamente.

> [!NOTE]
> Os planos ocultos não são compatíveis com links de versão prévia.

Selecione **Salvar rascunho** para continuar.

## <a name="technical-configuration"></a>Configurações técnicas

Forneça as imagens e outras propriedades técnicas associadas a este plano. Para obter mais informações, consulte [Configure VM offer List Details](azure-vm-create-listing.md).

> [!NOTE]
> A guia **Configuração técnica** não será exibida se você tiver configurado esse plano para reutilizar pacotes de outro plano na guia **Configuração do plano** .

### <a name="operating-system"></a>Sistema operacional

No painel **Sistema operacional** , faça o seguinte:

- Para **Família de sistemas operacionais** , selecione o sistema operacional **Windows** ou **Linux** .
- Para **Versão** ou **Fornecedor** , selecione a versão do Windows ou o fornecedor do Linux.
- Para **Nome amigável do sistema operacional** , insira um nome de sistema operacional amigável. Esse nome é visível para os clientes.

### <a name="recommended-vm-sizes"></a>Tamanhos de VM recomendados

Selecione até seis tamanhos de máquina virtual recomendados para exibição no Azure Marketplace.

### <a name="open-ports"></a>Abrir portas

Abra portas públicas ou privadas em uma máquina virtual implantada.

### <a name="storage-option-for-deployment"></a>Opção de armazenamento para implantação

Para **Opção de implantação de disco** , selecione o tipo de implantação de disco que seus clientes podem usar para a máquina virtual. A Microsoft recomenda limitar a implantação para somente **Implantação de disco gerenciado** .

### <a name="properties"></a>Propriedades

Para **Suporte à Rede Acelerada** , selecione se sua VM é ou não compatível com [Rede Acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Gerações

A geração de uma máquina virtual define o hardware virtual que ele usa. Com base nas necessidades do seu cliente, você pode publicar uma VM de geração 1, uma VM de geração 2 ou ambas.

1. Ao criar uma nova oferta, selecione um **tipo de geração** e insira os detalhes da imagem solicitada:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details.png" alt-text="Uma exibição da caixa suspensa geração.":::

2. Para adicionar outra geração a um plano, selecione **Adicionar geração** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Uma exibição da caixa suspensa geração.":::

    Em seguida, insira os detalhes da geração:

    :::image type="content" source="./media/create-vm/azure-vm-generations-details.png" alt-text="Uma exibição da caixa suspensa geração.":::

    A **ID de geração** que você escolher ficará visível para os clientes em locais como URLs de produto e modelos de ARM (se aplicável). Use somente caracteres minúsculos, alfanuméricos, traços ou sublinhados; Ela não pode ser modificada depois de publicada.

3. Para atualizar uma VM existente que tenha uma geração 1 já publicada, basta editar detalhes na página de **configuração técnica** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-updating.png" alt-text="Uma exibição da caixa suspensa geração.":::

Para saber mais sobre as diferenças entre os recursos de geração 1 e geração 2, consulte [suporte para VMs de geração 2 no Azure](../virtual-machines/generation-2.md).

### <a name="vm-images"></a>Imagens de VM

Forneça uma versão de disco e o URI de SAS (Assinatura de Acesso Compartilhado) para as imagens de máquina virtual. Adicione até 16 discos de dados para cada imagem de VM. Forneça apenas uma nova versão de imagem por plano em um envio especificado. Depois que uma imagem for publicada, você não poderá editá-la, mas poderá excluí-la. A exclusão de uma versão impede que os usuários novos e existentes implantem uma nova instância da versão excluída.

- **Versão do disco** : a versão da imagem que você está fornecendo.
- **URI da SAS** : a localização na sua conta de armazenamento do Azure em que você armazenou o VHD do sistema operacional. Para saber como obter um URI de SAS, consulte [obter URI de assinatura de acesso compartilhado para sua imagem de VM](azure-vm-get-sas-uri.md).
- As imagens de disco de dados também são URIs de assinatura de acesso compartilhado de VHD que são armazenadas nas respectivas contas de armazenamento do Azure.
- Adicione apenas uma imagem por envio em um plano.

Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados que a solução exige. Durante a implantação, os clientes não podem remover discos que fazem parte de uma imagem, mas podem sempre adicionar discos durante ou após a implantação.

Selecione **Salvar rascunho** antes de continuar e retornar para **Visão geral do plano** .

## <a name="next-steps"></a>Próximas etapas

- [Adicionar um público-alvo de versão prévia](azure-vm-create-preview.md)
