---
title: Migrar de v1 para v2 - Gateway de aplicativo Do Zure
description: Este artigo mostra como migrar o Azure Application Gateway e o Web Application Firewall de v1 para v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 96f3825288846e86771ef3907eb4da4e58630df3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475181"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrar Gateway de aplicativos do Azure e firewall de aplicativos web de v1 para v2

[O Azure Application Gateway e o Web Application Firewall (WEB Application Firewall) v2](application-gateway-autoscaling-zone-redundant.md) já estão disponíveis, oferecendo recursos adicionais, como autodimensionamento e redundância de zona de disponibilidade. No entanto, os gateways existentes v1 não são atualizados automaticamente para v2. Se você quiser migrar de v1 para v2, siga os passos deste artigo.

Há duas etapas em uma migração:

1. Migrar a configuração
2. Migrar o tráfego do cliente

Este artigo abrange a migração de configuração. A migração de tráfego do cliente varia dependendo do seu ambiente específico. No entanto, algumas recomendações gerais de alto nível [são fornecidas.](#migrate-client-traffic)

## <a name="migration-overview"></a>Visão geral da migração

Um script Do Azure PowerShell está disponível que faz o seguinte:

* Cria um novo gateway de Standard_v2 ou WAF_v2 em uma sub-rede virtual que você especificar.
* Copia perfeitamente a configuração associada ao gateway v1 Standard ou WAF para o gateway de Standard_V2 ou WAF_V2 recém-criado.

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O novo gateway v2 tem novos endereços IP públicos e privados. Não é possível mover os endereços IP associados ao gateway v1 existente perfeitamente para v2. No entanto, você pode alocar um endereço IP público ou privado existente (não alocado) para o novo gateway v2.
* Você deve fornecer um espaço de endereço IP para outra sub-rede dentro de sua rede virtual onde seu gateway v1 está localizado. O script não pode criar o gateway v2 em quaisquer sub-redes existentes que já tenham um gateway v1. No entanto, se a sub-rede existente já tiver um gateway v2, isso ainda pode funcionar desde que haja espaço de endereço IP suficiente.
* Para migrar uma configuração SSL, você deve especificar todos os certs SSL usados em seu gateway v1.
* Se você tiver o modo FIPS ativado para o gateway V1, ele não será migrado para o seu novo gateway v2. O modo FIPS não é suportado em v2.
* v2 não suporta IPv6, então os gateways V1 habilitados para IPv6 não são migrados. Se você executar o script, ele pode não completar.
* Se o gateway v1 tiver apenas um endereço IP privado, o script criará um endereço IP público e um endereço IP privado para o novo gateway v2. Atualmente, os gateways v2 não suportam apenas endereços IP privados.
* Cabeçalhos com nomes que contenham qualquer coisa que não seja letras, dígitos, hífens e sublinhados não são passados para o seu aplicativo. Isso só se aplica a nomes de cabeçalho, não a valores de cabeçalho. Esta é uma mudança de quebra de v1.

## <a name="download-the-script"></a>Baixe o script

Baixe o script de migração da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Use o script

Existem duas opções para você, dependendo da configuração e preferências do ambiente PowerShell local:

* Se você não tiver os módulos Azure Az instalados ou não se importar em desinstalar os módulos `Install-Script` Azure Az, a melhor opção é usar a opção para executar o script.
* Se você precisa manter os módulos Azure Az, sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se você tem os módulos Azure Az instalados, execute `Get-InstalledModule -Name az`. Se você não ver nenhum módulo Az instalado, então `Install-Script` você pode usar o método.

### <a name="install-using-the-install-script-method"></a>Instale usando o método Install-Script

Para usar esta opção, você não deve ter os módulos Azure Az instalados em seu computador. Se estiverem instalados, o comando a seguir exibe um erro. Você pode desinstalar os módulos Azure Az ou usar a outra opção para baixar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzureAppGWMigration`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale usando o script diretamente

Se você tiver alguns módulos Azure Az instalados e não puder desinstalá-los (ou não quiser desinstalá-los), você pode baixar manualmente o script usando a guia **Download manual** no link de download de script. O script é baixado como um arquivo nupkg bruto. Para instalar o script a partir deste arquivo nupkg, consulte [Download manual do pacote](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Use `Connect-AzAccount` para se conectar ao Azure.

1. Use `Import-Module Az` para importar os módulos Az.

1. Execute `Get-Help AzureAppGWMigration.ps1` para examinar os parâmetros necessários:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parâmetros para o script:
   * **resourceId: [String]: Obrigatório** - Este é o ID de recurso do Azure para o gateway Padrão v1 ou WAF v1 existente. Para encontrar esse valor de seqüência, navegue até o portal Dozure, selecione o gateway de aplicativo ou recurso WAF e clique no link **Propriedades** para o gateway. O ID de recursos está localizado nessa página.

     Você também pode executar os seguintes comandos do Azure PowerShell para obter o ID de recursos:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: Obrigatório** - Este é o espaço de endereço IP que você alocou (ou deseja alocar) para uma nova sub-rede que contém seu novo gateway v2. Isso deve ser especificado na notação CIDR. Por exemplo: 10.0.0.0/24. Você não precisa criar esta sub-rede com antecedência. O script cria para você se ele não existe.
   * **appgwName: [String]: Opcional**. Esta é uma string que você especifica para usar como nome para o novo gateway de Standard_v2 ou WAF_v2. Se este parâmetro não for fornecido, o nome do seu gateway v1 existente será usado com o sufixo *_v2* anexado.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Opcional**.  Uma lista separada por comma de objetos PSApplicationGatewaySlCertificate que você cria para representar os certs SSL do seu gateway v1 deve ser carregada no novo gateway v2. Para cada um dos seus certs SSL configurados para o gateway V1 ou WAF v1 padrão, você pode criar um novo objeto PSApplicationGatewaySslCertificate através do `New-AzApplicationGatewaySslCertificate` comando mostrado aqui. Você precisa do caminho para o seu arquivo SSL Cert e a senha.

     Este parâmetro só é opcional se você não tiver ouvintes HTTPS configurados para o seu gateway v1 ou WAF. Se você tiver pelo menos uma configuração de ouvinte HTTPS, você deve especificar este parâmetro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Você pode `$mySslCert1, $mySslCert2` passar (comma-separado) no exemplo anterior como valores para este parâmetro no script.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Opcional**. Uma lista separada por comma de objetos PSApplicationGatewayTrustedRootCertificate que você cria para representar os [certificados Raiz Confiável](ssl-overview.md) para autenticação de suas instâncias de backend a partir do gateway v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Para criar uma lista de objetos PSApplicationGatewayTrustedRootCertificate, consulte [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Opcional**. Um endereço IP privado específico que você deseja associar ao seu novo gateway v2.  Isso deve ser do mesmo VNet que você aloca para o seu novo gateway v2. Se isso não for especificado, o script alocará um endereço IP privado para o gateway v2.
   * **publicIpResourceId: [String]: Opcional**. O recursoId do recurso de endereço IP público (SKU padrão) existente em sua assinatura que você deseja alocar para o novo gateway v2. Se isso não for especificado, o script alocará um novo IP público no mesmo grupo de recursos. O nome é o nome do gateway v2 com *-IP* anexado.
   * **validmigraçãoMigração: [switch]: Opcional**. Use este parâmetro se quiser que o script faça algumas validações básicas de comparação de configuração após a criação do gateway v2 e a cópia de configuração. Por padrão, nenhuma validação é feita.
   * **habilitarAutoScale: [switch]: Opcional**. Use este parâmetro se quiser que o script habilite o AutoScaling no novo gateway v2 depois de criado. Por padrão, o AutoScaling está desativado. Você sempre pode habilitá-lo manualmente mais tarde no gateway v2 recém-criado.

1. Execute o script usando os parâmetros apropriados. Pode levar de cinco a sete minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrar o tráfego do cliente

Primeiro, verifique duas vezes se o script criou com sucesso um novo gateway v2 com a configuração exata migrada do gateway v1. Você pode verificar isso no portal Azure.

Além disso, envie uma pequena quantidade de tráfego através do gateway v2 como um teste manual.
  
Aqui estão alguns cenários em que seu gateway de aplicativo atual (Padrão) pode receber tráfego de clientes e nossas recomendações para cada um:

* **Uma região DNS personalizada (por exemplo, contoso.com) que aponta para o endereço IP frontend (usando um registro A) associado ao gateway V1 ou WAF v1 padrão**.

    Você pode atualizar seu registro de DNS para apontar para o rótulo frontend IP ou DNS associado ao gateway de aplicativo Standard_v2. Dependendo do TTL configurado no seu registro de DNS, pode levar um tempo para que todo o tráfego do cliente migre para o seu novo gateway v2.
* **Uma região DNS personalizada (por exemplo, contoso.com) que aponta para o rótulo DNS (por exemplo: *myappgw.eastus.cloudapp.azure.com* usando um registro CNAME) associada ao seu gateway v1**.

   Você tem duas opções:

  * Se você usar endereços IP públicos no gateway de aplicativo, poderá fazer uma migração controlada e granular usando um perfil do Gerenciador de Tráfego para direcionar gradualmente o tráfego (método de roteamento ponderado de tráfego) para o novo gateway v2.

    Você pode fazer isso adicionando os rótulos DNS dos gateways de aplicativos v1 e v2 ao [perfil do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)e CNAMEing seu registro DNS personalizado (por exemplo, `www.contoso.com`) ao domínio gerenciador de tráfego (por exemplo, contoso.trafficmanager.net).
  * Ou, você pode atualizar seu registro dns de domínio personalizado para apontar para o rótulo DNS do novo gateway de aplicativo v2. Dependendo do TTL configurado no seu registro de DNS, pode levar um tempo para que todo o tráfego do cliente migre para o seu novo gateway v2.
* **Seus clientes se conectam ao endereço IP frontend do gateway de aplicativo**.

   Atualize seus clientes para usar o endereço IP(es) associado ao gateway de aplicativo v2 recém-criado. Recomendamos que você não use endereços IP diretamente. Considere usar o rótulo de nome DNS (por exemplo, yourgateway.eastus.cloudapp.azure.com) associado ao gateway de aplicativo que você pode CNAME para sua própria zona DNS personalizada (por exemplo, contoso.com).

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem alguma limitação com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Ver [Ressalvas/Limitações](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Este artigo e o script Azure PowerShell também são aplicáveis para o produto WAF do Application Gateway? 

Sim.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>O script Azure PowerShell também alterna o tráfego do meu gateway v1 para o gateway v2 recém-criado?

Não. O script Azure PowerShell migra apenas a configuração. A migração real do tráfego é sua responsabilidade e está sob seu controle.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>O novo gateway v2 é criado pelo script Azure PowerShell de tamanho apropriado para lidar com todo o tráfego que é atualmente servido pelo meu gateway v1?

O script Azure PowerShell cria um novo gateway v2 com um tamanho apropriado para lidar com o tráfego no gateway v1 existente. O autoscaling é desativado por padrão, mas você pode ativar o AutoScaling quando executar o script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Configurei meu gateway v1 para enviar logs para o armazenamento do Azure. O script também replica essa configuração para v2?

Não. O script não replica essa configuração para v2. Você deve adicionar a configuração de log separadamente ao gateway v2 migrado.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Este script suporta certificados carregados no Azure KeyVault?

Não. Atualmente, o script não suporta certificados no KeyVault. No entanto, isso está sendo considerado para uma versão futura.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Tive alguns problemas com o uso deste roteiro. Como posso conseguir ajuda?
  
Você pode enviar appgwmigrationsup@microsoft.comum e-mail para, abra um caso de suporte com o Suporte Azure, ou faça ambos.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
