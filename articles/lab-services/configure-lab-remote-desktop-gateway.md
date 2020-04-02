---
title: Configure um laboratório para usar o Remote Desktop Gateway no Azure DevTest Labs
description: Aprenda a configurar um laboratório no Azure DevTest Labs com um gateway de desktop remoto para garantir acesso seguro às VMs do laboratório sem ter que expor a porta RDP.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2cdafa9a36a5f906151ca6946e18ef82bc7f1e01
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529414"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Configure seu laboratório no Azure DevTest Labs para usar um gateway de desktop remoto
No Azure DevTest Labs, você pode configurar um gateway de desktop remoto para o seu laboratório para garantir acesso seguro às máquinas virtuais de laboratório (VMs) sem ter que expor a porta RDP. O laboratório fornece um lugar central para os usuários do laboratório visualizarem e se conectarem a todas as máquinas virtuais a que tenham acesso. O botão **Conectar** na página **Máquina Virtual** cria um arquivo RDP específico da máquina que você pode abrir para se conectar à máquina. Você pode personalizar e proteger ainda mais a conexão RDP conectando seu laboratório a um gateway de desktop remoto. 

Essa abordagem é mais segura porque o usuário de laboratório autentica diretamente na máquina gateway ou pode usar credenciais da empresa em uma máquina gateway com domínio para se conectar às suas máquinas. O laboratório também suporta o uso de autenticação de token na máquina gateway que permite que os usuários se conectem às suas máquinas virtuais de laboratório sem ter a porta RDP exposta à internet. Este artigo mostra um exemplo de como configurar um laboratório que usa autenticação de tokens para se conectar a máquinas de laboratório.

## <a name="architecture-of-the-solution"></a>Arquitetura da solução

![Arquitetura da solução](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. A ação [Obter conteúdo de arquivo RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) é chamada quando você seleciona o botão **Conectar.1.** 
1. A ação Obter conteúdo de `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` arquivo RDP é invocada para solicitar um token de autenticação.
    1. `{gateway-hostname}`é o nome de host do gateway especificado na página **Configurações do laboratório** para o seu laboratório no portal Azure. 
    1. `{lab-machine-name}`é o nome da máquina que você está tentando conectar.
    1. `{port-number}`é a porta na qual a conexão precisa ser feita. Normalmente essa porta é 3389. Se o laboratório VM estiver usando o recurso [IP compartilhado](devtest-lab-shared-ip.md) no DevTest Labs, a porta será diferente.
1. O gateway de desktop remoto `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` adia a chamada de uma função Azure para gerar o token de autenticação. O serviço DevTest Labs inclui automaticamente a chave de função no cabeçalho de solicitação. A chave da função deve ser salva no cofre do laboratório. O nome para esse segredo a ser mostrado como **o segredo do token gateway** na página de **Configurações do Laboratório** para o laboratório.
1. Espera-se que a função Azure retorne um token para autenticação de token baseada em certificado satisfatoria contra a máquina gateway.  
1. A ação Obter conteúdo de arquivo RDP retorna então o arquivo RDP completo, incluindo as informações de autenticação.
1. Você abre o arquivo RDP usando seu programa de conexão RDP preferido. Lembre-se que nem todos os programas de conexão RDP suportam autenticação de token. O token de autenticação tem uma data de validade, definida pelo aplicativo de função. Faça a conexão com o vm do laboratório antes que o token expire.
1. Uma vez que a máquina de gateway de desktop remota autentica o token no arquivo RDP, a conexão é encaminhada para sua máquina de laboratório.

### <a name="solution-requirements"></a>Requisitos da solução
Para trabalhar com o recurso de autenticação de token do DevTest Labs, existem alguns requisitos de configuração para as máquinas de gateway, serviços de nome de domínio (DNS) e funções.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Requisitos para máquinas de gateway de desktop remotas
- O certificado TLS/SSL deve ser instalado na máquina de gateway para lidar com o tráfego HTTPS. O certificado deve corresponder ao nome de domínio totalmente qualificado (FQDN) do balanceador de carga para a fazenda gateway ou o FQDN da própria máquina se houver apenas uma máquina. Os certificados TLS/SSL de curinga não funcionam.  
- Um certificado de assinatura instalado em máquinas gateway(s). Crie um certificado de assinatura usando o script [Create-SigningCertificate.ps1.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)
- Instale o módulo [de autenticação plugável](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) que suporta autenticação de token para o gateway de desktop remoto. Um exemplo desse módulo `RDGatewayFedAuth.msi` é o que vem com [imagens VMM (System Center Virtual Machine Manager).](/system-center/vmm/install-console?view=sc-vmm-1807) Para obter mais informações sobre o System Center, consulte [a documentação](https://docs.microsoft.com/system-center/) do System Center e [os detalhes dos preços](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- O servidor gateway pode `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`lidar com solicitações feitas para .

    O nome de host de gateway é o FQDN do balanceador de carga da fazenda gateway ou o FQDN da própria máquina se houver apenas uma máquina. É `{lab-machine-name}` o nome da máquina de laboratório que você `{port-number}` está tentando conectar, e a porta em que a conexão será feita.  Por padrão, esta porta é 3389.  No entanto, se a máquina virtual estiver usando o recurso [IP compartilhado](devtest-lab-shared-ip.md) no DevTest Labs, a porta será diferente.
- O módulo solicitação de roteamento de aplicativos para o `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` IIS [(App Routing Request)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) pode ser usado para redirecionar solicitações para a função azure, que lida com a solicitação de obter um token para autenticação.


## <a name="requirements-for-azure-function"></a>Requisitos para a função Azure
A função Azure lida `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` com a solicitação com formato e retorna o token de autenticação com base no mesmo certificado de assinatura instalado nas máquinas de gateway. O `{function-app-uri}` uri é usado para acessar a função. A tecla de função é automaticamente passada no cabeçalho da solicitação. Para obter uma [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)função amostral, consulte . 


## <a name="requirements-for-network"></a>Requisitos para rede

- O DNS para o FQDN associado ao certificado TLS/SSL instalado nas máquinas gateway deve direcionar o tráfego para a máquina gateway ou para o balanceador de carga da fazenda de máquinas gateway.
- Se a máquina de laboratório usa IPs privados, deve haver um caminho de rede da máquina de gateway para a máquina de laboratório, seja através do compartilhamento da mesma rede virtual ou usando redes virtuais peered.

## <a name="configure-the-lab-to-use-token-authentication"></a>Configure o laboratório para usar a autenticação de tokens 
Esta seção mostra como configurar um laboratório para usar uma máquina de gateway de desktop remota que suporta autenticação de token. Esta seção não cobre como configurar uma fazenda de gateway de desktop remota em si. Para obter essas informações, consulte a amostra para criar uma seção [de gateway de desktop remota](#sample-to-create-a-remote-desktop-gateway) no final deste artigo. 

Antes de atualizar as configurações do laboratório, armazene a chave necessária para executar com sucesso a função para retornar um token de autenticação no cofre-chave do laboratório. Você pode obter o valor da chave da função na página **Gerenciar** para a função no portal Azure. Para obter mais informações sobre como salvar um segredo em um cofre de chaves, consulte [Adicionar um segredo ao Key Vault](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Guarde o nome do segredo para uso posterior.

Para encontrar a iD do cofre chave do laboratório, execute o seguinte comando Azure CLI: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configure o laboratório para usar a autenticação do token usando estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione seu **laboratório.**
1. Na página do laboratório, selecione **Configuração e políticas**.
1. No menu à esquerda, na seção **Configurações,** selecione **Configurações de Laboratório**.
1. Na seção **de desktop remota,** digite o nome de domínio totalmente qualificado (FQDN) ou endereço IP da máquina de gateway de serviços de desktop remoto ou fazenda para o campo **hostname do Gateway.** Esse valor deve corresponder ao FQDN do certificado TLS/SSL usado em máquinas de gateway.

    ![Opções de desktop remotas em configurações de laboratório](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Na seção **de desktop remota,** para o segredo **do gateway token,** digite o nome do segredo criado anteriormente. Este valor não é a chave de função em si, mas o nome do segredo no cofre chave do laboratório que detém a chave de função.

    ![Segredo de token gateway em configurações de laboratório](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Salvar** Alterações.

    > [!NOTE] 
    > Ao clicar **em Salvar,** você concorda com os [termos de licença do Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Para obter mais informações sobre o gateway remoto, consulte [Bem-vindo aos Serviços de Desktop Remoto](https://aka.ms/rds) e [Implante seu ambiente remoto de desktop](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Se a configuração do laboratório via automação for preferível, consulte [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) para obter um script PowerShell de exemplo para definir as configurações secretas **do hostname** e **do gateway token.** O [repositório Azure DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) também fornece um modelo do Azure Resource Manager que cria ou atualiza um laboratório com o nome do **host gateway** e as configurações secretas do gateway **token.**

## <a name="configure-network-security-group"></a>Configurar grupo de segurança de rede
Para proteger ainda mais o laboratório, um grupo de segurança de rede (NSG) pode ser adicionado à rede virtual usada pelas máquinas virtuais do laboratório. Para obter instruções sobre como configurar um NSG, consulte [Criar, alterar ou excluir um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).

Aqui está um exemplo de NSG que só permite o tráfego que primeiro passa pelo gateway para chegar a máquinas de laboratório. A fonte nesta regra é o endereço IP da máquina de gateway único, ou o endereço IP do balanceador de carga na frente das máquinas de gateway.

![Grupo de segurança de rede - regras](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Exemplo para criar um gateway de desktop remoto

> [!NOTE] 
> Ao usar os modelos de exemplo, você concorda com os [termos de licença do Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Para obter mais informações sobre o gateway remoto, consulte [Bem-vindo aos Serviços de Desktop Remoto](https://aka.ms/rds) e [Implante seu ambiente remoto de desktop](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

O [repositório Azure DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) fornece algumas amostras para ajudar a configurar os recursos necessários para usar autenticação de token e gateway de desktop remoto com o DevTest Labs. Essas amostras incluem modelos do Azure Resource Manager para máquinas de gateway, configurações de laboratório e aplicativo de função.

Siga essas etapas para configurar uma solução de exemplo para a fazenda de gateway de desktop remota.

1. Crie um certificado de assinatura.  Executar [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Salve a impressão digital, a senha e a codificação Base64 do certificado criado.
2. Obtenha um certificado TLS/SSL. O FQDN associado ao certificado TLS/SSL deve ser para o domínio que você controla. Salve a impressão digital, a senha e a codificação Base64 para este certificado. Para obter impressão digital usando o PowerShell, use os seguintes comandos.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Para obter a codificação Base64 usando o PowerShell, use o seguinte comando.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Baixar arquivos [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)de .

    O modelo requer acesso a alguns outros modelos do Gerenciador de Recursos e recursos relacionados na mesma base uri. Copie todos os [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) arquivos de rdgatewayFedAuth.msi para um recipiente blob em uma conta de armazenamento.  
4. Implantar **azuredeploy.json** de [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). O modelo toma os seguintes parâmetros:
    - adminNome do usuário – Obrigatório.  Nome de usuário do administrador para as máquinas de gateway.
    - adminPassword – Obrigatório. Senha para a conta do administrador para as máquinas de gateway.
    - instanceCount – Número de máquinas de gateway para criar.  
    - alwaysOn – Indica se deve manter o aplicativo Azure Functions criado em um estado quente ou não. Manter o aplicativo Azure Functions evitará atrasos quando os usuários tentarem se conectar pela primeira vez ao seu vm de laboratório, mas isso tem implicações de custos.  
    - tokenLifetime – O tempo que o token criado será válido. O formato é HH:MM:SS.
    - sslCertificate – A codificação Base64 do certificado TLS/SSL para a máquina gateway.
    - sslCertificatePassword – A senha do certificado TLS/SSL para a máquina gateway.
    - sslCertificateThumbprint - A impressão digital do certificado para identificação na loja de certificados locais do certificado TLS/SSL.
    - signCertificate – A codificação Base64 para assinatura de certificado para a máquina gateway.
    - signCertificatePassword – A senha para assinar certificado para a máquina gateway.
    - signCertificateThumbprint - A impressão digital do certificado para identificação na loja de certificados local do certificado de assinatura.
    - _artifactsLocation – LOCAL URI onde todos os recursos de suporte podem ser encontrados. Este valor deve ser um UIR totalmente qualificado, não um caminho relativo.
    - _artifactsLocationSasToken – O token SAS (SAS) de Assinatura de Acesso Compartilhado usado para acessar recursos de suporte, se o local for uma conta de armazenamento Azure.

    O modelo pode ser implantado usando o Azure CLI usando o seguinte comando:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Aqui estão as descrições dos parâmetros:

    - O {storage-account-endpoint} pode ser `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`obtido executando .  O {storage-acct-name} é o nome da conta de armazenamento que contém arquivos que você carregou.  
    - O {container-name} é o nome do contêiner no {storage-acct-name} que contém arquivos que você carregou.  
    - O {sas-token} pode ser `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`obtido executando . 
        - O {storage-acct-name} é o nome da conta de armazenamento que contém arquivos que você carregou.  
        - O {container-name} é o nome do contêiner no {storage-acct-name} que contém arquivos que você carregou.  
        - A data {utc-expir-date} é a data, na UTC, na qual o token SAS expirará e o token SAS não poderá mais ser usado para acessar a conta de armazenamento.

    Registre os valores de gatewayFQDN e gatewayIP a partir da saída de implantação do modelo. Você também precisará salvar o valor da chave de função para a função recém-criada, que pode ser encontrada na guia Configurações do [aplicativo Função.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
5. Configure o DNS para que o FQDN do Cert TLS/SSL direcione para endereço IP do gatewayIP da etapa anterior.

    Depois que a fazenda Remote Desktop Gateway é criada e atualizações apropriadas de DNS são feitas, ela está pronta para ser usada por um laboratório no DevTest Labs. As configurações secretas **hostname** e **gateway token** devem ser configuradas para usar as máquinas de gateway implantadas. 

    > [!NOTE]
    > Se a máquina de laboratório usa IPs privados, deve haver um caminho de rede da máquina de gateway para a máquina de laboratório, seja através do compartilhamento da mesma rede virtual ou usando uma rede virtual peered.

    Uma vez configurado o gateway e o laboratório, o arquivo de conexão criado quando o usuário do laboratório clica no **Connect** incluirá automaticamente as informações necessárias para se conectar usando a autenticação do token.     

## <a name="next-steps"></a>Próximas etapas
Veja o artigo a seguir para saber mais sobre serviços de desktop [remoto: documentação dos serviços de desktop remoto](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


