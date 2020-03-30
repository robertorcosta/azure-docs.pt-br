---
title: Upgrade de Público Básico para Público Padrão - Azure Load Balancer
description: Este artigo mostra como atualizar o Azure Public Load Balancer do Basic SKU para o Standard SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: a4c8b029b199915cce9a417430e67675a03d327f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659944"
---
# <a name="upgrade-azure-public-load-balancer"></a>Upgrade Azure Public Load Balancer
[O Azure Standard Load Balancer](load-balancer-overview.md) oferece um rico conjunto de funcionalidades e alta disponibilidade através da redundância de zona. Para saber mais sobre load balancer SKU, consulte [tabela de comparação](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Há três estágios em uma atualização:

1. Migrar a configuração
2. Adicionar VMs aos pools back-end do Standard Load Balancer
3. Crie uma regra de saída no balanceador de carga para conexão de saída

Este artigo abrange a migração de configuração. A adição de VMs em pools de back-end pode variar dependendo do seu ambiente específico. No entanto, algumas recomendações gerais de alto nível [são fornecidas.](#add-vms-to-backend-pools-of-standard-load-balancer)

## <a name="upgrade-overview"></a>Visão geral da atualização

Um script Do Azure PowerShell está disponível que faz o seguinte:

* Cria um Balanceador de carga SKU padrão no grupo de recursos e localização que você especificar.
* Copia perfeitamente as configurações do Balanceador de Carga SKU Básico para o recém-criado Standard Load Balancer.

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O script só suporta atualização do Balancer de Carga Pública. Para o upgrade do Balancer de carga básica interna, crie um Balancer de carga interna padrão se a conectividade de saída não for desejada e crie um Balancer de carga interna padrão e um balanceador de carga pública padrão se a conectividade de saída for necessária.
* O Balanceador de Carga Padrão tem um novo endereço público. É impossível mover os endereços IP associados ao Balancer de Carga Básica existente perfeitamente para o Standard Load Balancer, uma vez que eles têm SKUs diferentes.
* Se o balanceador de carga Padrão for criado em uma região diferente, você não poderá associar as VMs existentes na região antiga ao recém-criado Standard Load Balancer. Para contornar essa limitação, certifique-se de criar uma nova VM na nova região.
* Se o balanceador de carga não tiver nenhuma configuração de IP frontend ou pool de back-end, é provável que você acerte um erro executando o script. Por favor, certifique-se de que eles não estão vazios.

## <a name="download-the-script"></a>Baixe o script

Baixe o script de migração da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/1.0).
## <a name="use-the-script"></a>Use o script

Existem duas opções para você, dependendo da configuração e preferências do ambiente PowerShell local:

* Se você não tiver os módulos Azure Az instalados ou não se importar em desinstalar os módulos `Install-Script` Azure Az, a melhor opção é usar a opção para executar o script.
* Se você precisa manter os módulos Azure Az, sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se você tem os módulos Azure Az instalados, execute `Get-InstalledModule -Name az`. Se você não ver nenhum módulo Az instalado, então `Install-Script` você pode usar o método.

### <a name="install-using-the-install-script-method"></a>Instale usando o método Install-Script

Para usar esta opção, você não deve ter os módulos Azure Az instalados em seu computador. Se estiverem instalados, o comando a seguir exibe um erro. Você pode desinstalar os módulos Azure Az ou usar a outra opção para baixar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzurePublicLBUpgrade`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale usando o script diretamente

Se você tiver alguns módulos Azure Az instalados e não puder desinstalá-los (ou não quiser desinstalá-los), você pode baixar manualmente o script usando a guia **Download manual** no link de download de script. O script é baixado como um arquivo nupkg bruto. Para instalar o script a partir deste arquivo nupkg, consulte [Download manual do pacote](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Use `Connect-AzAccount` para se conectar ao Azure.

1. Use `Import-Module Az` para importar os módulos Az.

1. Examine os parâmetros necessários:

   * **oldRgName: [String]: Obrigatório** – Este é o grupo de recursos para o balanceador de carga básico existente que você deseja atualizar. Para encontrar esse valor de seqüência, navegue até o portal Azure, selecione a fonte do Balancer de carga básica e clique na **visão geral** do balanceador de carga. O Grupo de Recursos está localizado nessa página.
   * **oldLBName: [String]: Obrigatório** – Este é o nome do seu Balanceador Básico existente que você deseja atualizar. 
   * **newrgName: [String]: Obrigatório** – Este é o grupo de recursos no qual o Balanceador de Carga Padrão será criado. Pode ser um novo grupo de recursos ou um já existente. Se você escolher um grupo de recursos existente, observe que o nome do Balanceador de carga deve ser único dentro do grupo de recursos. 
   * **newlocation: [String]: Obrigatório** – Este é o local em que o Balanceador de Carga Padrão será criado. Recomenda-se herdar a mesma localização do Balanceador de Carga Básica escolhido ao Balanceador de Carga Padrão para uma melhor associação com outros recursos existentes.
   * **newLBName: [String]: Obrigatório** – Este é o nome do Balanceador de Carga Padrão a ser criado.
1. Execute o script usando os parâmetros apropriados. Pode levar de cinco a sete minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Adicionar VMs aos pools back-end do Standard Load Balancer

Primeiro, verifique duas vezes se o script criou com sucesso um novo Balanceador de Carga Pública Padrão com a configuração exata migrada do balanceador de carga pública básico. Você pode verificar isso no portal Azure.

Certifique-se de enviar uma pequena quantidade de tráfego através do Balanceador de Carga Padrão como um teste manual.
  
Aqui estão alguns cenários de como você adiciona VMs aos pools de back-end do recém-criado Standard Public Load Balancer pode ser configurado, e nossas recomendações para cada um:

* **Movendo VMs existentes de pools backend do antigo Balancer de Carga Pública Básica para pools de backend do recém-criado Standard Public Load Balancer**.
    1. Para realizar todas as tarefas deste início rápido, entre no [portal do Azure](https://portal.azure.com).
 
    1. Selecione **Todos os recursos** no menu esquerdo e selecione o balanceador de carga padrão **recém-criado** na lista de recursos.
   
    1. Em **Configurações**, selecione **Pools de back-end**.
   
    1. Selecione o pool de backend que corresponde ao pool de backend do Balancer de carga básica, selecione o seguinte valor: 
      - **Máquina Virtual**: Desça e selecione as VMs no pool de backend correspondente do Balanceador de Carga Básica.
    1. Selecione **Salvar**.
    >[!NOTE]
    >Para VMs que possuem IPs públicos, você precisará criar endereços IP padrão primeiro onde o mesmo endereço IP não é garantido. Desassilocem As VMs dos IPs Básicos e asassociam-nas aos endereços IP padrão recém-criados. Em seguida, você poderá seguir as instruções para adicionar VMs no pool de back-end do Standard Load Balancer. 

* **Criação de novas VMs para adicionar aos pools de backend do recém-criado Standard Public Load Balancer**.
    * Mais instruções sobre como criar VM e associá-la ao Standard Load Balancer podem ser encontradas [aqui](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Crie uma regra de saída para conexão de saída

Siga as [instruções](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) para criar uma regra de saída para que você possa
* Defina o NAT de saída do zero.
* Dimensione e ajuste o comportamento do NAT de saída existente.

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem alguma limitação com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Ver [Ressalvas/Limitações](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script Azure PowerShell também alterna o tráfego do meu Balanceador de Carga Básica para o recém-criado Standard Load Balancer?

Não. O script Azure PowerShell migra apenas a configuração. A migração real do tráfego é sua responsabilidade e está sob seu controle.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Tive alguns problemas com o uso deste roteiro. Como posso conseguir ajuda?
  
Você pode enviar slbupgradesupport@microsoft.comum e-mail para, abra um caso de suporte com o Suporte Azure, ou faça ambos.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Balanceador de Carga Padrão](load-balancer-overview.md)
