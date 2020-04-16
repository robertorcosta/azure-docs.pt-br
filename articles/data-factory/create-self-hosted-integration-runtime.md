---
title: Criar um Integration Runtime auto-hospedado
description: Saiba como criar um tempo de execução de integração auto-hospedado na Fábrica de Dados Do Azure, que permite que as fábricas de dados acessem os armazenamentos de dados em uma rede privada.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/13/2020
ms.openlocfilehash: 6bc0f002c6927cfd9a314797663e1dabbac392b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416632"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Criar e configurar um runtime de integração auto-hospedada

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer funcionalidades de integração de dados entre diferentes ambientes de rede. Para obter detalhes sobre o IR, confira [Visão geral do Integration Runtime](concepts-integration-runtime.md).

Um tempo de execução de integração auto-hospedado pode executar atividades de cópia entre um armazenamento de dados na nuvem e um armazenamento de dados em uma rede privada. Ele também pode despachar atividades de transformação contra recursos de computação em uma rede local ou em uma rede virtual Do Zure. A instalação de um tempo de execução de integração auto-hospedado precisa de uma máquina no local ou de uma máquina virtual dentro de uma rede privada.  

Este artigo descreve como você pode criar e configurar um IR auto-hospedado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Configuração de um tempo de execução de integração auto-hospedado

Para criar e configurar um tempo de execução de integração auto-hospedado, use os seguintes procedimentos.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Crie um IR auto-hospedado via Azure PowerShell

1. Você pode usar o Azure PowerShell para esta tarefa. Veja um exemplo:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Faça o download](https://www.microsoft.com/download/details.aspx?id=39717) e instale o runtime de integração auto-hospedada em um computador local.

3. Recupere a chave de autenticação e registre o runtime de integração auto-hospedada com ela. Aqui está um exemplo do PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Crie um IR auto-hospedado via UI da Fábrica de Dados Do Azure

Use as seguintes etapas para criar um IR auto-hospedado usando a UI azure Data Factory.

1. Na página **Let's get started** da UI Azure Data Factory, selecione a guia **Autor** no painel mais à esquerda.

   ![O botão autor da página inicial](media/doc-common-process/get-started-page-author-button.png)

1. Selecione **Conexões** na parte inferior do painel mais à esquerda e selecione **'Tempos de execução de integração'** na janela **Conexões.** Selecione **+Novo**.

   ![Criar um Integration Runtime](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. Na página **de configuração de tempo de execução Integração,** **selecione Azure, Self-Hosted**e selecione **Continuar**. 

1. Na página seguinte, **selecione Self-Hosted** para criar um IR auto-hospedado e, em seguida, **selecione Continuar**.
   ![Criar um IR auto-hospedado](media/create-self-hosted-integration-runtime/new-selfhosted-ir.png)

1. Digite um nome para o seu IR e selecione **Criar**.

1. Na página **de configuração de tempo de execução Integração,** selecione o link na **Opção 1** para abrir a configuração expressa no computador. Ou siga as etapas da **Opção 2** para configurar manualmente. As seguintes instruções são baseadas na configuração manual:

   ![Configuração do runtime de integração](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Copie e cole a chave de autenticação. Selecione **Baixar e instalar o tempo de execução da integração**.

    1. Baixe o runtime de integração auto-hospedada em uma máquina local do Windows. Execute o instalador.

    1. Na página **'Executar o tempo de execução de integração de registro' (auto-hospedado),** cole a chave salva anteriormente e selecione **Registrar**.
    
       ![Registrar o Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Na página **Novo nó do Integration Runtime (auto-hospedado)**, selecione **Concluir**.

1. Depois que o tempo de execução de integração auto-hospedado é registrado com sucesso, você verá a seguinte janela:

    ![Registro bem-sucedido](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Configure um IR auto-hospedado em uma VM Azure através de um modelo do Azure Resource Manager

Você pode automatizar a configuração de IR auto-hospedada em uma máquina virtual do Azure usando o [modelo De criar iIr de auto-host](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). O modelo fornece uma maneira fácil de ter um IR totalmente funcional e auto-hospedado dentro de uma rede virtual Azure. O IR tem recursos de alta disponibilidade e escalabilidade, desde que você defina a contagem de nós para 2 ou mais.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Configure um IR auto-hospedado existente através do PowerShell local

Você pode usar uma linha de comando para configurar ou gerenciar um IR auto-hospedado existente. Esse uso pode ajudar especialmente a automatizar a instalação e o registro de nódulos de IR auto-hospedados.

O Dmgcmd.exe está incluído no instalador auto-hospedado. Ele está normalmente localizado na pasta C:\Program Files\Microsoft Integration Runtime\3.0\Shared\. Este aplicativo suporta vários parâmetros e pode ser invocado através de uma linha de comando usando scripts em lote para automação.

Use o aplicativo da seguinte forma:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Aqui estão detalhes dos parâmetros e propriedades do aplicativo: 

| Propriedade                                                    | Descrição                                                  | Obrigatório |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegistreNewNode** "`<AuthenticationKey>`"                     | Registre um nó de tempo de execução de integração auto-hospedado com a chave de autenticação especificada. | Não       |
| **RegistreNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | Registre um nó de tempo de execução de integração auto-hospedado com a chave de autenticação especificada e o nome do nó. | Não       |
| **Habilitar o RemoteAccess** "`<port>`" ["`<thumbprint>`"]            | Habilite o acesso remoto no nó atual para configurar um cluster de alta disponibilidade. Ou habilite a configuração de credenciais diretamente contra o IR auto-hospedado sem passar pela Fábrica de Dados Do Azure. Você faz o último usando o **cmdlet New-AzDataFactoryV2LinkedServiceEncryptedCredential** de uma máquina remota na mesma rede. | Não       |
| **HabilitarooremoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | Habilite o acesso remoto ao nó atual quando o nó for executado em um contêiner. | Não       |
| **DesativaroRemoteAccess**                                         | Desabilite o acesso remoto ao nó atual. O acesso remoto é necessário para a configuração de vários nonos. O cmdlet Do **Novo-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell ainda funciona mesmo quando o acesso remoto é desativado. Esse comportamento é verdadeiro desde que o cmdlet seja executado na mesma máquina que o nó IR auto-hospedado. | Não       |
| **Chave** "`<AuthenticationKey>`"                                 | Sobrepor ou atualizar a chave de autenticação anterior. Tenha cuidado com essa ação. Seu nó IR auto-hospedado anterior pode ficar offline se a chave for de um novo tempo de execução de integração. | Não       |
| **Gerarbackupdearquivo** "`<filePath>`" "`<password>`"            | Gerar um arquivo de backup para o nó atual. O arquivo de backup inclui a chave de nó e as credenciais do armazenamento de dados. | Não       |
| **ImportarArquivo de backup** "`<filePath>`" "`<password>`"              | Restaurar o nó de um arquivo de backup.                          | Não       |
| **Reiniciar**                                                     | Reinicie o serviço de host de integração de integração auto-hospedado.   | Não       |
| **Início**                                                       | Inicie o serviço de host de integração de integração auto-hospedado.     | Não       |
| **Parar**                                                        | Interrompa o serviço de host de integração de integração auto-hospedado.        | Não       |
| **StartUpgradeService**                                         | Inicie o serviço de atualização em tempo de execução de integração auto-hospedado.       | Não       |
| **StopUpgradeService**                                          | Interrompa o serviço de atualização em tempo de execução de integração auto-hospedado.        | Não       |
| **Atualização automática de TurnOn**                                            | Ative a atualização automática de tempo de execução de integração auto-hospedada.        | Não       |
| **Atualização automática de desada de desapareça**                                           | Desative a atualização automática de tempo de execução de integração auto-hospedada.       | Não       |
| **SwitchServiceConta** "`<domain\user>`" ["`<password>`"]           | Defina DIAHostService para ser executado como uma nova conta. Use a senha vazia "" para contas do sistema e contas virtuais. | Não       |


## <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados

Quando você move dados entre o local e a nuvem, a atividade usa um tempo de execução de integração auto-hospedado para transferir os dados entre uma fonte de dados local e a nuvem.

Aqui está um resumo de alto nível das etapas de fluxo de dados para copiar com um IR auto-hospedado:

![A visão geral de alto nível do fluxo de dados](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Um desenvolvedor de dados cria um tempo de execução de integração auto-hospedado dentro de uma fábrica de dados do Azure usando um cmdlet PowerShell. Atualmente, o portal Azure não suporta esse recurso.
1. O desenvolvedor de dados cria um serviço vinculado para um armazenamento de dados no local. O desenvolvedor faz isso especificando a instância de tempo de execução de integração auto-hospedada que o serviço deve usar para se conectar a armazenamentos de dados.
1. O nó do runtime de integração auto-hospedada criptografa a credencial usando a DPAPI (Interface de Programação do Aplicativo de Proteção de Dados) do Windows e salva as credenciais localmente. Se vários nós forem definidos para alta disponibilidade, as credenciais serão mais sincronizadas em outros nós. Cada nó criptografa as credenciais usando a DPAPI e as armazena localmente. A sincronização de credenciais é transparente para o desenvolvedor de dados e é tratada pelo IR auto-hospedado.
1. A Fábrica de Dados do Azure comunica-se com o tempo de execução de integração auto-hospedado para agendar e gerenciar trabalhos. A comunicação é através de um canal de controle que usa uma conexão compartilhada [de Relé de Ônibus de Serviço Azure.](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) Quando um trabalho de atividade precisa ser executado, a Data Factory faz fila com a solicitação junto com quaisquer informações de credencial. Ele faz isso caso as credenciais ainda não estejam armazenadas no tempo de execução de integração auto-hospedado. O tempo de execução de integração auto-hospedado inicia o trabalho depois de pesquisar a fila.
1. O tempo de execução de integração auto-hospedado copia dados entre uma loja no local e armazenamento em nuvem. A direção da cópia depende de como a atividade de cópia é configurada no pipeline de dados. Para esta etapa, o tempo de execução de integração auto-hospedado se comunica diretamente com serviços de armazenamento baseados em nuvem, como o armazenamento Azure Blob em um canal HTTPS seguro.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerações para o uso de um IR auto-hospedado

- Você pode usar um único tempo de execução de integração auto-hospedado para várias fontes de dados no local. Você também pode compartilhá-lo com outra fábrica de dados dentro do mesmo inquilino do Azure Active Directory (Azure AD). Para obter mais informações, confira [Compartilhando um runtime de integração auto-hospedada](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Você pode instalar apenas uma instância de um tempo de execução de integração auto-hospedado em qualquer máquina. Se você tiver duas fábricas de dados que precisam acessar fontes de dados no local, use o [recurso de compartilhamento de IR auto-hospedado](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) para compartilhar o IR auto-hospedado ou instale o IR auto-hospedado em dois computadores locais, um para cada fábrica de dados.  
- O tempo de execução de integração auto-hospedado não precisa estar na mesma máquina que a fonte de dados. No entanto, ter o tempo de execução de integração auto-hospedado perto da fonte de dados reduz o tempo de execução da integração auto-hospedada para se conectar à fonte de dados. Recomendamos que você instale o tempo de execução de integração auto-hospedado em uma máquina que difere da que hospeda a fonte de dados local. Quando o tempo de execução de integração auto-hospedado e a fonte de dados estão em diferentes máquinas, o tempo de execução de integração auto-hospedado não compete com a fonte de dados para recursos.
- Você pode ter vários runtimes da integração auto-hospedada em diferentes computadores conectados à mesma fonte de dados local. Por exemplo, se você tiver dois tempos de integração auto-hospedados que atendem duas fábricas de dados, a mesma fonte de dados no local pode ser registrada em ambas as fábricas de dados.
- Se você já tiver um gateway instalado em seu computador para atender a um cenário de Power BI, instale um tempo de execução de integração auto-hospedado separado para fábrica de dados em outra máquina.
- Use um tempo de execução de integração auto-hospedado para suportar a integração de dados em uma rede virtual do Azure.
- Trate a fonte de dados como uma fonte de dados local protegida por um firewall mesmo quando você usar o Microsoft Azure ExpressRoute. Use o tempo de execução de integração auto-hospedado para conectar o serviço à fonte de dados.
- Use o tempo de execução de integração auto-hospedado, mesmo que o armazenamento de dados esteja na nuvem em uma máquina virtual Azure Infrastructure as a Service (IaaS).
- As tarefas podem falhar em um tempo de execução de integração auto-hospedado que você instalou em um servidor Windows para o qual a criptografia compatível com FIPS está ativada. Para contornar esse problema, desabilite a criptografia em conformidade com FIPS no servidor. Para desativar a criptografia compatível com FIPS, altere o valor da subchave de `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`registro a seguir de 1 (habilitado) para 0 (desativado): .

## <a name="prerequisites"></a>Pré-requisitos

- As versões suportadas do Windows são:
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   A instalação do tempo de execução de integração auto-hospedado em um controlador de domínio não é suportada.
- É necessário o .NET Framework 4.6.1 ou posterior. Se você estiver instalando o runtime de integração auto-hospedada em um computador com Windows 7, instale o .NET Framework 4.6.1 ou posterior. Confira [Requisitos de sistema do .NET Framework](/dotnet/framework/get-started/system-requirements) para obter detalhes.
- A configuração mínima recomendada para a máquina de tempo de execução de integração auto-hospedada é um processador de 2 GHz com 4 núcleos, 8 GB de RAM e 80 GB de espaço disponível no disco rígido.
- Se a máquina host hibernar, o tempo de execução de integração auto-hospedado não responderá às solicitações de dados. Configure um plano de energia adequado no computador antes de instalar o runtime de integração auto-hospedada. Se a máquina estiver configurada para hibernar, o instalador de tempo de execução de integração auto-hospedado solicitará uma mensagem.
- Você deve ser um administrador na máquina para instalar e configurar com sucesso o tempo de execução de integração auto-hospedado.
- As corridas de copy-activity acontecem com uma frequência específica. O uso do processador e da RAM na máquina segue o mesmo padrão com os tempos de pico e ociosidade. O uso de recursos também depende muito da quantidade de dados que são movidos. Quando vários trabalhos de cópia estiverem em andamento, você verá o uso do recurso aumentar durante horários de pico.
- As tarefas podem falhar durante a extração de dados nos formatos Parquet, ORC ou Avro. Para obter mais informações sobre o Parquet, consulte [o formato Parquet na Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime). A criação de arquivos é executada na máquina de integração auto-hospedada. Para funcionar como esperado, a criação de arquivos requer os seguintes pré-requisitos:
    - [Visual C++ 2010 Redistributable](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Pacote (x64)
    - Java Runtime (JRE) versão 8 de um provedor JRE como [Adopt OpenJDK](https://adoptopenjdk.net/). Certifique-se `JAVA_HOME` de que a variável de ambiente está definida.

## <a name="installation-best-practices"></a>Melhores práticas de instalação

Você pode instalar o tempo de execução de integração auto-hospedado baixando um pacote de configuração de identidade gerenciada do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Consulte o artigo [Mova dados entre o local e a nuvem](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure um plano de energia na máquina host para o tempo de execução de integração auto-hospedado para que a máquina não hiberne. Se o computador host hibernar, o runtime de integração auto-hospedada ficará offline.
- Faça backup regular das credenciais associadas ao tempo de execução da integração auto-hospedada.
- Para automatizar operações de configuração de IR auto-hospedadas, consulte [Configurar um IR auto-hospedado existente via PowerShell](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Instale e registre um IR auto-hospedado no Microsoft Download Center

1. Acesse a [página de downloads do Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
1. Selecione **Baixar,** selecione a versão de 64 bits e selecione **Next**. A versão de 32 bits não é suportada.
1. Execute o arquivo Identidade Gerenciada diretamente ou salve-o no seu disco rígido e execute-o.
1. Na janela **Bem-vindo,** selecione um idioma e selecione **Next**.
1. Aceite os Termos de Licença para Software Microsoft e selecione **Avançar**.
1. Selecione **pasta** para instalar o runtime de integração auto-hospedada e selecione **Avançar**.
1. Na **página Pronto para instalar,** selecione **Instalar**.
1. Selecione **Concluir** para concluir a instalação.
1. Obtenha a chave de autenticação usando o PowerShell. Aqui há um exemplo do PowerShell para recuperar a chave de autenticação:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. Na janela **'Executar tempo de execução de integração de registro' (auto-hospedado)** do Microsoft Integration Runtime Configuration Manager em execução na máquina, tome as seguintes etapas:

    1. Cole a chave de autenticação na área de texto.

    1. Opcionalmente, selecione **Mostrar chave de autenticação** para ver o texto da chave.

    1. Selecione **Registrar**.

## <a name="high-availability-and-scalability"></a>Alta disponibilidade e escalabilidade

Você pode associar um tempo de execução de integração auto-hospedado com várias máquinas locais ou máquinas virtuais no Azure. Esses computadores são chamados de nós. Você pode ter até quatro nós associados a um runtime de integração auto-hospedada. Os benefícios de ter vários nós em máquinas locais que tenham um gateway instalado para um gateway lógico são:

* Maior disponibilidade do tempo de execução de integração auto-hospedado para que não seja mais o único ponto de falha em sua solução de big data ou integração de dados em nuvem com a Fábrica de Dados. Essa disponibilidade ajuda a garantir a continuidade quando você usa até quatro nós.
* Desempenho e taxa de transferência aprimorados durante a movimentação de dados entre os armazenamentos de dados de nuvem e locais. Obtenha mais informações sobre [comparações de desempenho](copy-activity-performance.md).

Você pode associar vários nós instalando o software de tempo de execução de integração auto-hospedado do [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Em seguida, registre-o usando qualquer uma das chaves de autenticação obtidas do **cmdlet New-AzDataFactoryV2IntegrationRuntimeKey,** conforme descrito no [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Você não precisa criar um novo tempo de execução de integração auto-hospedado para associar cada nó. Você pode instalar o runtime de integração auto-hospedada em outro computador e registrá-lo usando a mesma chave de autenticação.

> [!NOTE]
> Antes de adicionar outro nó para alta disponibilidade e escalabilidade, certifique-se de que a opção **de acesso remoto à intranet** esteja ativada no primeiro nó. Para isso, selecione**configurações** >  **de configuração de execução da Integração da** > Microsoft**Acesso remoto à intranet**.

### <a name="scale-considerations"></a>Considerações de escala

#### <a name="scale-out"></a>Expansão

Quando o uso do processador estiver alto e a memória disponível estiver fraca no IR auto-hospedado, adicione um novo nó para ajudar a dimensionar a carga entre as máquinas. Se as atividades falharem porque elas estão fora do tempo ou o nó IR auto-hospedado estiver offline, isso ajuda se você adicionar um nó ao gateway.

#### <a name="scale-up"></a>Escalar verticalmente

Quando o processador e a RAM disponíveis não são bem utilizados, mas a execução de trabalhos simultâneos atinge os limites de um nó, aumentar o número de trabalhos simultâneos que um nó pode executar. Você também pode querer aumentar a escala quando as atividades se apagarem porque o IR auto-hospedado está sobrecarregado. Conforme mostrado na imagem a seguir, você pode aumentar a capacidade máxima de um nó:  

![Aumente o número de empregos simultâneos que podem ser executados em um nó](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL

Aqui estão os requisitos para o certificado TLS/SSL que você usa para garantir a comunicação entre nós de tempo de execução de integração:

- O certificado deve ser um certificado X509 v3 publicamente confiável. Recomendamos que você use certificados emitidos por uma autoridade de certificação de parceiros públicos (CA).
- Cada nó do runtime de integração deve confiar nesse certificado.
- Não recomendamos certificados de Nome Alternativo de Assunto (SAN) porque apenas o último item de SAN é usado. Todos os outros itens da SAN são ignorados. Por exemplo, se você tiver um certificado SAN cujos SANs são **node1.domain.contoso.com** e **node2.domain.contoso.com,** você pode usar este certificado apenas em uma máquina cujo nome de domínio totalmente qualificado (FQDN) é **node2.domain.contoso.com**.
- O certificado pode usar qualquer tamanho de chave suportado pelo Windows Server 2012 R2 para certificados TLS/SSL.
- Não são suportados certificados que usam chaves DE GNV.  

> [!NOTE]
> Este certificado é usado:
>
> - Para criptografar portas em um nó IR auto-hospedado.
> - Para comunicação de nó a nó para sincronização de estado, que inclui sincronização de credenciais de serviços vinculados em todos os álos.
> - Quando um cmdlet PowerShell é usado para configurações de credencial de serviço vinculado a partir de uma rede local.
>
> Sugerimos que você use este certificado se o ambiente de rede privada não estiver seguro ou se você quiser garantir a comunicação entre nós dentro de sua rede privada.
>
> A movimentação de dados em trânsito de um IR auto-hospedado para outros armazenamentos de dados sempre acontece dentro de um canal criptografado, independentemente de este certificado estar ou não definido.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Crie um tempo de execução de integração auto-hospedado compartilhado na Fábrica de Dados do Azure

É possível reutilizar uma infraestrutura de runtime de integração auto-hospedada existente já configurada em um data factory. Essa reutilização permite que você crie um tempo de execução de integração auto-hospedado vinculado em uma fábrica de dados diferente, fazendo referência a um IR auto-hospedado compartilhado existente.

Para ver uma introdução e demonstração deste recurso, assista ao vídeo de 12 minutos a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **IR compartilhado**: Um IR auto-hospedado original que funciona em uma infra-estrutura física.  
- **IR Vinculado**: Um IR que faz referência a outro IR compartilhado. O IR vinculado é um RI lógico e utiliza a infra-estrutura de outro IR auto-hospedado compartilhado.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Métodos para compartilhar um tempo de execução de integração auto-hospedado

Para compartilhar um tempo de execução de integração autohospedado com várias fábricas de dados, consulte [Criar um tempo de execução de integração auto-hospedado compartilhado](create-shared-self-hosted-integration-runtime-powershell.md) para obter detalhes.

### <a name="monitoring"></a>Monitoramento

#### <a name="shared-ir"></a>RI compartilhado

![Seleções para encontrar um tempo de execução de integração compartilhada](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitore um tempo de execução de integração compartilhada](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>RI vinculado

![Seleções para encontrar um tempo de execução de integração vinculado](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitore um tempo de execução de integração vinculado](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitações conhecidas do compartilhamento de IR auto-hospedado

* A fábrica de dados na qual um IR vinculado é criado deve ter uma [Identidade Gerenciada](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Por padrão, as fábricas de dados criadas no portal Azure ou cmdlets PowerShell têm uma Identidade Gerenciada criada implicitamente. Mas quando uma fábrica de dados é criada através de um modelo de Gerenciador de Recursos do Azure ou SDK, você deve definir a propriedade **Identidade** explicitamente. Essa configuração garante que o Gerenciador de recursos crie uma fábrica de dados que contenha uma identidade gerenciada.

* O Data Factory .NET SDK que suporta esse recurso deve ser a versão 1.1.0 ou posterior.

* Para conceder permissão, você precisa da função Proprietário ou da função de Proprietário herdado na fábrica de dados onde o RI compartilhado existe.

* O recurso de compartilhamento funciona apenas para fábricas de dados dentro do mesmo inquilino azure AD.

* Para [usuários convidados](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)do Azure AD, a funcionalidade de pesquisa na ui, que lista todas as fábricas de dados usando uma palavra-chave de pesquisa, [não funciona](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Mas enquanto o usuário convidado for o proprietário da fábrica de dados, você pode compartilhar o IR sem a funcionalidade de pesquisa. Para a Identidade Gerenciada da fábrica de dados que precisa compartilhar o IR, digite essa Identidade Gerenciada na caixa **Deperção de Atribuição** e selecione **Adicionar** na ui da fábrica de dados.

  > [!NOTE]
  > Este recurso está disponível apenas no Data Factory V2.

## <a name="notification-area-icons-and-notifications"></a>Ícones e notificações da área de notificação

Se você mover o cursor sobre o ícone ou a mensagem na área de notificação, poderá ver detalhes sobre o estado do tempo de execução de integração auto-hospedado.

![Notificações na área de notificação](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Portas e firewalls

Há dois firewalls a considerar:

- O *firewall corporativo* que funciona no roteador central da organização
- O *firewall do Windows* configurado como um daemon na máquina local onde o tempo de execução de integração auto-hospedado é instalado

![Os firewalls](media/create-self-hosted-integration-runtime/firewall.png)

No nível do firewall corporativo, é necessário configurar os seguintes domínios e portas de saída:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

No nível de firewall do Windows ou na máquina, essas portas de saída são normalmente habilitadas. Se não estiverem, você pode configurar os domínios e portas em uma máquina de tempo de execução de integração auto-hospedada.

> [!NOTE]
> Com base na sua origem e nos sinks, você pode precisar permitir domínios adicionais e portas de saída em seu firewall corporativo ou firewall do Windows.
>
> Para alguns bancos de dados em nuvem, como o Azure SQL Database e o Azure Data Lake, talvez seja necessário permitir endereços IP de máquinas de tempo de execução de integração auto-hospedadas em sua configuração de firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados de uma origem para um coletor

Certifique-se de habilitar corretamente as regras de firewall no firewall corporativo, no firewall do Windows da máquina de tempo de execução de integração auto-hospedada e no próprio armazenamento de dados. A ativação dessas regras permite que o tempo de execução de integração auto-hospedado se conecte com sucesso à origem e ao afundamento. Habilite as regras para cada repositório de dados que esteja envolvido na operação de cópia.

Por exemplo, para copiar de um armazenamento de dados local para um dissipador de banco de dados SQL ou um dissipador de data warehouse DoZure SQL, dê as seguintes etapas:

1. Permita a comunicação TCP de saída na porta 1433 tanto para o firewall do Windows quanto para o firewall corporativo.
1. Configure as configurações de firewall do Banco de Dados SQL para adicionar o endereço IP da máquina de tempo de execução de integração auto-hospedada à lista de endereços IP permitidos.

> [!NOTE]
> Se o firewall não permitir a porta de saída 1433, o tempo de execução de integração auto-hospedado não poderá acessar diretamente o banco de dados SQL. Neste caso, você pode usar uma [cópia em etapas](copy-activity-performance.md) para o SQL Database e o SQL Data Warehouse. Neste cenário, você precisa apenas de HTTPS (porta 443) para a movimentação de dados.

## <a name="proxy-server-considerations"></a>Considerações do servidor proxy

Se o ambiente de rede corporativo usar um servidor proxy para acessar a Internet, configure o runtime de integração auto-hospedada para usar as definições de proxy apropriadas. Você pode definir o proxy durante a fase de registro inicial.

![Especifique o proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Quando configurado, o tempo de execução de integração auto-hospedado usa o servidor proxy para se conectar à origem e ao destino do serviço de nuvem (que usam o protocolo HTTP ou HTTPS). É por isso que você seleciona **'Alterar link'** durante a configuração inicial.

![Defina o proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Há três opções de configuração:

- **Não use proxy**: O tempo de execução de integração auto-hospedado não usa explicitamente nenhum proxy para se conectar a serviços na nuvem.
- **Use proxy do sistema**: O tempo de execução de integração auto-hospedado usa a configuração proxy configurada em diahost.exe.config e diawp.exe.config. Se esses arquivos especificarem nenhuma configuração de proxy, o tempo de execução de integração auto-hospedado se conecta diretamente ao serviço de nuvem sem passar por um proxy.
- **Use proxy personalizado**: Configure a configuração de proxy HTTP para usar para o tempo de execução de integração auto-hospedado, em vez de usar configurações em diahost.exe.config e diawp.exe.config. **Os** valores **de endereço e porta** são necessários. **Os** valores de Nome de Usuário e **Senha** são opcionais, dependendo da configuração de autenticação do seu proxy. Todas as configurações são criptografadas com a DPAPI do Windows no Integration Runtime auto-hospedado e armazenados localmente no computador.

O serviço de host em tempo de execução de integração é reiniciado automaticamente após salvar as configurações de proxy atualizadas.

Depois de registrar o tempo de execução de integração auto-hospedado, se quiser visualizar ou atualizar as configurações do proxy, use o Microsoft Integration Runtime Configuration Manager.

1. Abra o **Configuration Manager do Microsoft Integration Runtime**.
1. Selecione a guia **Configurações.**
1. Em **HTTP Proxy,** selecione o link **Alterar** para abrir a caixa de diálogo Definir **HTTP Proxy.**
1. Selecione **Avançar**. Em seguida, você verá um aviso que pede sua permissão para salvar a configuração proxy e reiniciar o serviço de host em tempo de execução de integração.

Você pode usar a ferramenta gerenciador de configuração para visualizar e atualizar o proxy HTTP.

![Exibir e atualizar o proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se você configurar um servidor proxy com autenticação NTLM, o serviço de host em tempo de execução de integração será executado sob a conta de domínio. Se você alterar posteriormente a senha da conta de domínio, lembre-se de atualizar as configurações do serviço e reiniciar o serviço. Por causa desse requisito, sugerimos que você acesse o servidor proxy usando uma conta de domínio dedicada que não exige que você atualize a senha com freqüência.

### <a name="configure-proxy-server-settings"></a>Definir configurações do servidor proxy

Se você selecionar a opção **Usar proxy** do sistema para o proxy HTTP, o tempo de execução de integração auto-hospedado usará as configurações de proxy em diahost.exe.config e diawp.exe.config. Quando esses arquivos não especificam proxy, o tempo de execução de integração auto-hospedado se conecta diretamente ao serviço de nuvem sem passar por um proxy. O procedimento a seguir fornece instruções para atualizar o arquivo diahost.exe.config:

1. No File Explorer, faça uma cópia segura do C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config como um backup do arquivo original.
1. Abra o Bloco de Notas funcionando como administrador.
1. No Bloco de Notas, abra o arquivo de texto C:\Arquivos do programa\Microsoft Integração Runtime\3.0\Shared\diahost.exe.config.
1. Encontre a tag **system.net** padrão conforme mostrado no código a seguir:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Em seguida, você pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo a seguir:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    A tag proxy permite que propriedades `scriptLocation`adicionais especifiquem as configurações necessárias como . Consulte [ \<\> o proxy Element (Configurações de rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) para obter sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Salve o arquivo de configuração em sua localização original. Em seguida, reinicie o serviço de host de integração de integração auto-hospedado, que capta as alterações.

   Para reiniciar o serviço, use os serviços do Painel de Controle. Ou, no Configuration Manager do Integration Runtime, selecione o botão **Interromper serviço** e, depois, selecione **Iniciar serviço**.

   Se o serviço não for inicializado, provavelmente adicionou sintaxe de tag XML incorreta no arquivo de configuração do aplicativo que você editou.

> [!IMPORTANT]
> Não se esqueça de atualizar diahost.exe.config e diawp.exe.config.

Você também precisa ter certeza de que o Microsoft Azure está na lista de de desemitas da sua empresa. Você pode baixar a lista de endereços IP azure válidos do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Possíveis sintomas para problemas relacionados ao firewall e servidor proxy

Se você vir mensagens de erro como as seguintes, a razão provável é a configuração inadequada do firewall ou servidor proxy. Essa configuração impede que o tempo de execução de integração auto-hospedado se conecte à Fábrica de Dados para se autenticar. Para garantir que seu firewall e servidor proxy estejam configurados corretamente, confira a seção anterior.

* Quando você tenta registrar o tempo de execução de integração auto-hospedado, você recebe a seguinte mensagem de erro: "Falha ao registrar este nó Deintegração runtime! Confirme se a chave autenticação é válida e que o serviço de host do serviço de integração está sendo executado nesta máquina."
* Ao abrir o Configuration Manager do Integration Runtime, você vê o status **Desconectado** ou **Conectando**. Quando você visualiza os logs de eventos do Windows, em **Event Viewer** > **Application and Services Logs** > Microsoft Integration**Runtime**, você vê mensagens de erro como esta:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Habilite o acesso remoto a partir de uma intranet

Se você usar o PowerShell para criptografar credenciais de uma máquina em rede que não seja onde você instalou o tempo de execução de integração auto-hospedado, você poderá habilitar a opção **Acesso remoto da Intranet.** Se você executar o PowerShell para criptografar credenciais na máquina onde você instalou o tempo de execução de integração auto-hospedado, você não poderá habilitar **o Acesso Remoto da Intranet**.

Habilite **o acesso remoto da Intranet** antes de adicionar outro nó para alta disponibilidade e escalabilidade.  

Quando você executa a configuração de execução de integração auto-hospedada versão 3.3 ou posterior, por padrão, o instalador de tempo de execução de integração auto-hospedado desativa o **Acesso Remoto da Intranet** na máquina de tempo de execução de integração de integração auto-hospedada.

Quando você usa um firewall de um parceiro ou outros, você pode abrir manualmente a porta 8060 ou a porta configurada pelo usuário. Se você tiver um problema de firewall ao configurar o tempo de execução de integração auto-hospedado, use o seguinte comando para instalar o tempo de execução de integração auto-hospedado sem configurar o firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se você optar por não abrir a porta 8060 na máquina de tempo de execução de integração auto-hospedada, use mecanismos diferentes do aplicativo Configuração Credenciais para configurar credenciais de armazenamento de dados. Por exemplo, você pode usar o cmdlet PowerShell do **New-AzDataFactoryV2LinkedServiceEncryptCredential.**

## <a name="next-steps"></a>Próximas etapas

Para obter instruções passo a passo, consulte [Tutorial: Copie dados no local para nuvem](tutorial-hybrid-copy-powershell.md).
