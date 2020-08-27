---
title: Configurar um dispositivo de migrações para Azure para servidores físicos
description: Saiba como configurar um dispositivo de migrações para Azure para avaliação de servidor físico.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 1b4e875a81c92f74cd7d2db96cf1c313157297eb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923527"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurar um dispositivo para servidores físicos

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando servidores físicos com a ferramenta migrações para Azure: Server Assessment.

O dispositivo de migrações para Azure é um dispositivo leve, usado pela avaliação de servidor de migrações para Azure para fazer o seguinte:

- Descobrir servidores locais.
- Enviar dados de desempenho e metadados para servidores descobertos para avaliação do Azure migrar servidor.

[Saiba mais](migrate-appliance.md) sobre o dispositivo migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo:
- Forneça um nome de dispositivo e gere uma chave de projeto de migrações para Azure no Portal.
- Baixe um arquivo compactado com o script do instalador de Migrações para Azure do portal do Azure.
- Extraia o conteúdo do arquivo compactado. Inicie o console do PowerShell com privilégios administrativos.
- Execute o script do PowerShell para iniciar o aplicativo Web do dispositivo.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure usando a chave de projeto migrações para Azure.

### <a name="generate-the-azure-migrate-project-key"></a>Gerar a chave de projeto de migrações para Azure

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **descobrir computadores**  >  **são seus computadores virtualizados?**, selecione **físico ou outro (AWS, GCP, Xen, etc.)**.
3. Em **1: gerar chave de projeto de migrações para Azure**, forneça um nome para o dispositivo de migrações para Azure que será configurado para a descoberta de servidores físicos ou virtuais. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique em **gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página descobrir computadores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto de migrações para Azure** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante sua configuração.

### <a name="download-the-installer-script"></a>Baixe o script do instalador.

Em **2: baixar o dispositivo de migrações para Azure**, clique em **baixar**.

   ![Seleções para computadores de descoberta](./media/tutorial-assess-physical/servers-discover.png)


   ![Seleções para a chave de geração](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso da nuvem pública: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Exemplo de uso da nuvem governamental: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Verifique a versão mais recente do dispositivo e [as configurações](./tutorial-assess-physical.md#verify-security)de valores de hash.
 

## <a name="run-the-azure-migrate-installer-script"></a>Executar o script de instalador de Migrações para Azure
O script do instalador faz o seguinte:

- Instala agentes e um aplicativo Web para avaliação e descoberta de servidor físico.
- Instala as funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o ISE do PowerShell.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM) com detalhes de configuração persistente para Migrações para Azure.
- Cria os seguintes arquivos sob o caminho:
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Config
    - **Arquivos de configuração**: %Programdata%\Microsoft Azure\Logs

Crie o script da seguinte maneira:

1. Extraia o arquivo compactado para uma pasta no servidor que hospedará o dispositivo.  Você não deve executar o script em um computador em um dispositivo de Migrações para Azure existente.
2. Inicie o PowerShell no servidor acima com privilégio administrativo (elevado).
3. Altere o diretório do PowerShell para a pasta em que o conteúdo foi extraído do arquivo compactado baixado.
4. Execute o script chamado **AzureMigrateInstaller.ps1** executando o seguinte comando:

    - Para a nuvem pública: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Para o Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    O script iniciará o aplicativo Web do dispositivo quando ele for concluído com êxito.

Se você encontrar algum problema, poderá acessar os logs do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Carimbo de data/hora</em>.log para solucionar problemas.



### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. Abra um navegador em qualquer computador que possa se conectar ao dispositivo e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho clicando no atalho do aplicativo.
2. Aceite os **termos de licença**e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Conectividade**: O aplicativo verifica se o servidor tem acesso à Internet. Se o servidor usar um proxy:
        - Clique em **Configurar proxy** para e especifique o endereço do proxy (no formulário http://ProxyIPAddress ou na http://ProxyFQDN) porta de escuta.
        - Especifique as credenciais caso o proxy exija autenticação.
        - Há suporte apenas para o proxy HTTP.
        - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **salvar** para disparar a verificação de conectividade novamente.
    - **Sincronização do horário**: o horário é verificado. O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta do servidor funcione corretamente.
    - **Instalar atualizações**: avaliação do servidor de migrações para Azure verifica se o dispositivo tem as atualizações mais recentes instaladas. Depois que a verificação for concluída, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave de projeto de migrações para Azure** copiada do Portal. Se você não tiver a chave, vá para **avaliação do servidor> descobrir> gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração de chave e copie a chave correspondente.
1. Clique em **fazer logon**. Ele abrirá um prompt de logon do Azure em uma nova guia do navegador. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Depois de fazer logon com êxito, volte para o aplicativo Web. 
4. Se a conta de usuário do Azure usada para registro em log tiver as [permissões](tutorial-prepare-physical.md) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Agora, conecte-se do dispositivo aos servidores físicos a serem descobertos e inicie a descoberta.

1. Na **etapa 1: fornecer credenciais para a descoberta de servidores físicos ou virtuais do Windows e Linux**, clique em **Adicionar credenciais** para especificar um nome amigável para as credenciais, adicionar **nome de usuário** e **senha** para um servidor Windows ou Linux. Clique em **Save**.
1. Se você quiser adicionar várias credenciais ao mesmo tempo, clique em **adicionar mais** para salvar e adicionar mais credenciais. Há suporte para várias credenciais para descoberta de servidores físicos.
1. Na **etapa 2: fornecer detalhes do servidor virtual ou físico**, clique em **Adicionar origem de descoberta** para especificar o **endereço IP do servidor/FQDN** e o nome amigável para as credenciais a serem conectadas ao servidor.
1. Você pode **Adicionar um único item** por vez ou **adicionar vários itens** em um só lugar. Também há uma opção para fornecer detalhes do servidor por meio de **importação CSV**.

    ![Seleções para adicionar origem de descoberta](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Se você escolher **Adicionar um único item**, poderá escolher o tipo de sistema operacional, especificar nome amigável para credenciais, adicionar **endereço IP do servidor/FQDN** e clicar em **salvar**.
    - Se você escolher **adicionar vários itens**, poderá adicionar vários registros ao mesmo tempo especificando o **endereço IP do servidor/FQDN** com o nome amigável para credenciais na caixa de texto. **Verifique** os registros adicionados e clique em **salvar**.
    - Se você escolher **importar CSV** _(selecionado por padrão)_, poderá baixar um arquivo de modelo CSV, preencher o arquivo com o **endereço IP do servidor/FQDN** e nome amigável para credenciais. Em seguida, importe o arquivo para o dispositivo, **Verifique** os registros no arquivo e clique em **salvar**.

1. Ao clicar em salvar, o dispositivo tentará validar a conexão com os servidores adicionados e mostrará o **status de validação** na tabela em cada servidor.
    - Se a validação falhar para um servidor, examine o erro clicando em **validação falha** na coluna status da tabela. Corrija o problema e valide novamente.
    - Para remover um servidor, clique em **excluir**.
1. Você pode **revalidar** a conectividade a servidores a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar descoberta**, para iniciar a descoberta dos servidores validados com êxito. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta em cada servidor na tabela.


Isso iniciará a descoberta. Leva aproximadamente 2 minutos por servidor para que os metadados do servidor descoberto apareçam na portal do Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a conclusão da descoberta, você pode verificar se os servidores aparecem no Portal.

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique no ícone que exibe a contagem de **Servidores descobertos**.


## <a name="next-steps"></a>Próximas etapas

Experimente a [avaliação de servidores físicos](tutorial-assess-physical.md) com a avaliação de servidor de migrações para Azure.
