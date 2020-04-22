---
title: Upgrade de Básico Interno para Padrão Interno - Balanceador de carga do Azure
description: Este artigo mostra como atualizar o Azure Internal Load Balancer do Basic SKU para o Standard SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770381"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Atualizar balanceador de carga interna do Azure- Sem necessidade de conexão de saída
[O Azure Standard Load Balancer](load-balancer-overview.md) oferece um rico conjunto de funcionalidades e alta disponibilidade através da redundância de zona. Para saber mais sobre load balancer SKU, consulte [tabela de comparação](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Este artigo introduz um script PowerShell que cria um Balanceador de Carga Padrão com a mesma configuração do Balancer de Carga Básica, juntamente com a migração do tráfego de Balanceador de Carga Básica para Balanceador de Carga Padrão.

## <a name="upgrade-overview"></a>Visão geral da atualização

Um script Do Azure PowerShell está disponível que faz o seguinte:

* Cria um Balanceador de carga SKU interno padrão no local especificado. Observe que nenhuma [conexão de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) não será fornecida pelo Balanceador de carga interna padrão.
* Copia perfeitamente as configurações do Balancer de carga SKU básico para o recém-criado Standard Load Balancer.
* Mova perfeitamente os IPs privados do Basic Load Balancer para o recém-criado Standard Load Balancer.
* Mova perfeitamente as VMs do pool de backend do Balancer de Carga Básica para o pool de backend do Balanceador de Carga Padrão

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O script só suporta a atualização do Balancer de Carga Interna onde nenhuma conexão de saída é necessária. Se você precisou [de conexão de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) para algumas de suas VMs, consulte esta [página](upgrade-InternalBasic-To-PublicStandard.md) para obter instruções. 
* Se o balanceador de carga Padrão for criado em uma região diferente, você não poderá associar as VMs existentes na região antiga ao recém-criado Standard Load Balancer. Para contornar essa limitação, certifique-se de criar uma nova VM na nova região.
* Se o balanceador de carga não tiver nenhuma configuração de IP frontend ou pool de back-end, é provável que você acerte um erro executando o script. Certifique-se de que eles não estão vazios.

## <a name="download-the-script"></a>Baixe o script

Baixe o script de migração da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Use o script

Existem duas opções para você, dependendo da configuração e preferências do ambiente PowerShell local:

* Se você não tiver os módulos Azure Az instalados ou não se importar em desinstalar os módulos `Install-Script` Azure Az, a melhor opção é usar a opção para executar o script.
* Se você precisa manter os módulos Azure Az, sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se você tem os módulos Azure Az instalados, execute `Get-InstalledModule -Name az`. Se você não ver nenhum módulo Az instalado, então `Install-Script` você pode usar o método.

### <a name="install-using-the-install-script-method"></a>Instale usando o método Install-Script

Para usar esta opção, você não deve ter os módulos Azure Az instalados em seu computador. Se estiverem instalados, o comando a seguir exibe um erro. Você pode desinstalar os módulos Azure Az ou usar a outra opção para baixar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzureILBUpgrade`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale usando o script diretamente

Se você tiver alguns módulos Azure Az instalados e não puder desinstalá-los (ou não quiser desinstalá-los), você pode baixar manualmente o script usando a guia **Download manual** no link de download de script. O script é baixado como um arquivo nupkg bruto. Para instalar o script a partir deste arquivo nupkg, consulte [Download manual do pacote](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Use `Connect-AzAccount` para se conectar ao Azure.

1. Use `Import-Module Az` para importar os módulos Az.

1. Examine os parâmetros necessários:

   * **rgName: [String]: Obrigatório** – Este é o grupo de recursos para o seu Balanceador de Carga Básico existente e novo Balanceador de Carga Padrão. Para encontrar esse valor de seqüência, navegue até o portal Azure, selecione a fonte do Balancer de carga básica e clique na **visão geral** do balanceador de carga. O Grupo de Recursos está localizado nessa página.
   * **oldLBName: [String]: Obrigatório** – Este é o nome do seu Balanceador Básico existente que você deseja atualizar. 
   * **newlocation: [String]: Obrigatório** – Este é o local em que o Balanceador de Carga Padrão será criado. Recomenda-se herdar a mesma localização do Balanceador de Carga Básica escolhido ao Balanceador de Carga Padrão para uma melhor associação com outros recursos existentes.
   * **newLBName: [String]: Obrigatório** – Este é o nome do Balanceador de Carga Padrão a ser criado.
1. Execute o script usando os parâmetros apropriados. Pode levar de cinco a sete minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem alguma limitação com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Ver [Ressalvas/Limitações](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script Azure PowerShell também alterna o tráfego do meu Balanceador de Carga Básica para o recém-criado Standard Load Balancer?

Sim, migra o tráfego. Se você quiser migrar o tráfego pessoalmente, use [este script](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) que não mova VMs para você.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Tive alguns problemas com o uso deste roteiro. Como posso conseguir ajuda?
  
Você pode enviar slbupgradesupport@microsoft.comum e-mail para, abra um caso de suporte com o Suporte Azure, ou faça ambos.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Balanceador de Carga Padrão](load-balancer-overview.md)
