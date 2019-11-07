---
title: Criar um tempo de execução de integração auto-hospedado no Azure Data Factory
description: Saiba como criar um tempo de execução da integração auto-hospedada no Azure Data Factory, que permite que os data factories acessem armazenamentos de dados em uma rede privada.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 594d4b941f3ed67daa4e1cfd57c2f5539e0cb9ee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677179"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Criar e configurar um tempo de execução da integração auto-hospedada
O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer funcionalidades de integração de dados entre diferentes ambientes de rede. Para obter detalhes sobre o IR, confira [Visão geral do Integration Runtime](concepts-integration-runtime.md).

Um tempo de execução da integração auto-hospedada consegue executar as atividades de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados na rede privada, além de expedir atividades de transformação em recursos de computação em uma rede local ou em uma rede virtual do Azure. A instalação de um tempo de execução da integração auto-hospedada precisa de uma VM (máquina virtual) ou de um computador local dentro de uma rede privada.  

Este documento descreve como você pode criar e configurar o IR auto-hospedado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Configurando um Integration Runtime auto-hospedado

Para criar e configurar um tempo de execução de integração auto-hospedado, você pode usar os seguintes métodos:

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Criar um IR auto-hospedado via Azure PowerShell 

1. Você pode usar Azure PowerShell para esta tarefa. Aqui está um exemplo:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Faça o download](https://www.microsoft.com/download/details.aspx?id=39717) e instale o tempo de execução da integração auto-hospedada em um computador local.

3. Recupere a chave de autenticação e registre o tempo de execução da integração auto-hospedada com ela. Aqui está um exemplo do PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Criar um IR auto-hospedado via interface do usuário do Azure Data Factory

Você pode seguir as etapas abaixo para criar um IR auto-hospedado usando a interface do usuário do Azure Data Factory. 

1. Na **página** de introdução da interface do usuário do Azure data Factory, selecione a guia **autor** no painel esquerdo.

   ![Botão autor da página inicial](media/doc-common-process/get-started-page-author-button.png)
2. Clique em **conexões** na parte inferior do painel esquerdo e alterne para os **tempos de execução de integração** na janela conexões. Selecione **+ novo**.

   ![Tempo de execução de integração](media/create-self-hosted-integration-runtime/new-integration-runtime.png)
3. Na janela **instalação do Integration Runtime** , selecione *executar atividades de movimentação e distribuição de dados para cálculos externos*e clique em **continuar**.
4. Insira um nome para o IR e selecione **criar**.
5. Você pode usar a **opção 1** para iniciar a instalação expressa no seu computador. Como alternativa, use a **opção 2** para configurar manualmente. As instruções a seguir são baseadas na **configuração manual**:

   ![Configuração do tempo de execução de integração](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Copie e cole a chave de autenticação. Selecione **baixar e instalar o Integration Runtime**.
        
    1. Baixe o tempo de execução de integração auto-hospedado em uma máquina local do Windows. Execute a instalação.
        
    1. Na página **Registrar Integration Runtime (auto-hospedado)** , cole a chave que você salvou na seção anterior e selecione **Registrar**.
    
       ![Registrar o Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Na página **novo nó Integration Runtime (auto-hospedado)** , selecione **concluir**. 

6. Você verá a seguinte mensagem quando o Integration Runtime auto-hospedado for registrado com êxito:

    ![Registrado com êxito](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-azure-vm-via-azure-resource-manager-template"></a>Configurar um IR auto-hospedado na VM do Azure por meio do modelo de Azure Resource Manager 
É possível automatizar a configuração do IR auto-hospedado em uma máquina virtual do Azure usando [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). O modelo fornece uma maneira fácil de ter um IR auto-hospedado totalmente funcional dentro da rede virtual do Azure com recursos de alta disponibilidade e escalabilidade (contanto que você defina a contagem de nós como 2 ou superior).

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Configurar um IR para hospedagem interna existente via PowerShell local

Você pode usar a linha de comando para configurar ou gerenciar um IR autônomo existente. Isso pode ser usado especialmente para automatizar a instalação, o registro de nós IR hospedados internamente. 

O **Dmgcmd. exe** está incluído na instalação hospedada internamente, normalmente localizada: C:\Arquivos de Programas\microsoft Integration Runtime\3.0\Shared\ pasta. Isso dá suporte a vários parâmetros e pode ser invocado via prompt de comando usando scripts do lote para automação. 

*Uso:* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Detalhes (parâmetros/Propriedade):* 

| Propriedade                                                    | DESCRIÇÃO                                                  | Obrigatório |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Registrar Integration Runtime (auto-hospedado) nó com a chave de autenticação especificada | Não       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Habilitar o acesso remoto no nó atual para configurar um cluster de alta disponibilidade e/ou habilitar a configuração de credenciais diretamente em relação ao IR de hospedagem interna (sem passar pelo serviço ADF) usando  **Cmdlet New-AzDataFactoryV2LinkedServiceEncryptedCredential** de um computador remoto na mesma rede. | Não       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Habilitar o acesso remoto ao nó atual quando o nó estiver em execução no contêiner | Não       |
| DisableRemoteAccess                                         | Desabilite o acesso remoto ao nó atual. O acesso remoto é necessário para a instalação de vários nós. O cmdlet New-**AzDataFactoryV2LinkedServiceEncryptedCredential** do PowerShell ainda funciona mesmo quando o acesso remoto é desabilitado, desde que seja executado no mesmo computador que o nó ir hospedado internamente. | Não       |
| Chave "`<AuthenticationKey>`"                                 | Substituir/atualizar a chave de autenticação anterior. Tenha cuidado, pois isso pode fazer com que o nó IR para hospedagem interna anterior fique offline, se a chave for de um tempo de execução de integração novo. | Não       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | Gerar arquivo de backup para o nó atual, o arquivo de backup inclui a chave do nó e as credenciais do repositório de dados | Não       |
| ImportBackupFile "`<filePath>`" "`<password>`"              | Restaurar o nó de um arquivo de backup                          | Não       |
| Reiniciar                                                     | Reiniciar o serviço de Host Integration Runtime (auto-hospedado)   | Não       |
| Iniciar                                                       | Iniciar o serviço de Host Integration Runtime (auto-hospedado)     | Não       |
| Parar                                                        | Parar o serviço de atualização de Integration Runtime (auto-hospedado)        | Não       |
| StartUpgradeService                                         | Iniciar o serviço de atualização de Integration Runtime (auto-hospedado)       | Não       |
| StopUpgradeService                                          | Parar o serviço de atualização de Integration Runtime (auto-hospedado)        | Não       |
| TurnOnAutoUpdate                                            | Ativar Integration Runtime (auto-hospedado) atualização automática        | Não       |
| TurnOffAutoUpdate                                           | Desligar Integration Runtime (auto-hospedado) atualização automática       | Não       |
| SwitchServiceAccount "< domínio \ usuário >" ["senha"]           | Defina DIAHostService para executar como uma nova conta. Usar senha vazia ("") para conta do sistema ou conta virtual | Não       |
| LogLevel `<logLevel>`                                       | Definir nível de log do ETW (desativado, erro, detalhado ou todos). Geralmente usado pelo suporte da Microsoft durante a depuração. | Não       |


## <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando você move os dados entre a nuvem e o local, a atividade usa um tempo de execução da integração auto-hospedada para transferir os dados da fonte de dados local para a nuvem e vice-versa.

Aqui está o fluxo de dados de alto nível para e o resumo das etapas para a cópia com um IR auto-hospedado:

![Visão geral de alto nível](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. O desenvolvedor de dados cria um tempo de execução da integração auto-hospedada dentro de um Azure Data Factory usando o cmdlet do PowerShell. Atualmente, o Portal do Azure não dá suporte a esse recurso.
2. O desenvolvedor de dados cria um serviço vinculado para um armazenamento de dados local especificando a instância do tempo de execução da integração auto-hospedada que ele deve usar para se conectar a armazenamentos de dados.
3. O nó do tempo de execução da integração auto-hospedada criptografa a credencial usando a DPAPI (Interface de Programação do Aplicativo de Proteção de Dados) do Windows e salva as credenciais localmente. Se vários nós forem definidos para alta disponibilidade, as credenciais serão mais sincronizadas em outros nós. Cada nó criptografa as credenciais usando a DPAPI e as armazena localmente. A sincronização de credenciais é transparente para o desenvolvedor de dados e é tratada pelo IR auto-hospedado.    
4. O serviço Data Factory se comunica com o tempo de execução de integração auto-hospedado para agendamento e gerenciamento de trabalhos por meio de um *canal de controle* que usa uma [retransmissão do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay)compartilhada. Quando um trabalho de atividade precisa ser executado, o Data Factory enfileira a solicitação juntamente com quaisquer informações de credenciais (no caso de as credenciais ainda não estarem armazenadas no Integration Runtime auto-hospedado). O tempo de execução da integração auto-hospedada inicia o trabalho depois da sondagem da fila.
5. O tempo de execução da integração auto-hospedada copia dados de um repositório local para um armazenamento na nuvem, ou vice-versa, dependendo de como a atividade de cópia estiver configurada no pipeline de dados. Para esta etapa, o tempo de execução da integração auto-hospedada se comunica diretamente com os serviços de armazenamento baseado em nuvem, como Armazenamento de Blobs do Azure por um canal seguro (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerações para o uso de um IR auto-hospedado

- Um único Integration Runtime auto-hospedado pode ser usado para várias fontes de dados locais. Um único tempo de execução da integração auto-hospedada pode ser compartilhado com outro data factory no mesmo locatário do Azure Active Directory. Para obter mais informações, confira [Compartilhando um tempo de execução da integração auto-hospedada](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Você pode ter apenas uma instância do tempo de execução da integração auto-hospedada instalada em um único computador. Se você tiver duas fábricas de dados que precisam acessar fontes de dados locais, use o recurso de [compartilhamento de ir](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) via hospedagem interna para compartilhar o tempo de execução de integração auto-hospedado ou instale o tempo de execução de integração auto-hospedado em dois computadores locais, um para cada data factory.  
- O tempo de execução da integração auto-hospedada não precisa estar no mesmo computador que a fonte de dados. No entanto, ter o tempo de execução da integração auto-hospedada mais perto da fonte de dados reduz o tempo para o tempo de execução da integração auto-hospedada se conectar à fonte de dados. É recomendável instalar o Integration Runtime auto-hospedado em um computador que seja diferente daquele que hospeda a fonte de dados local. Quando a fonte de dados e o tempo de execução da integração auto-hospedada estão em computadores diferentes, o tempo de execução da integração auto-hospedada não compete por recursos com a fonte de dados.
- Você pode ter vários tempos de execução da integração auto-hospedada em diferentes computadores conectados à mesma fonte de dados local. Por exemplo, pode ter dois tempos de execução da integração auto-hospedada servindo dois data factories, mas a mesma fonte de dados local é registrada com ambos os data factories.
- Se você já tiver um gateway instalado no computador para atender um cenário do Power BI, instale um tempo de execução da integração auto-hospedada separado para o Azure Data Factory em outro computador.
- O tempo de execução da integração auto-hospedada deve ser usado para dar suporte à integração de dados na rede virtual do Azure.
- Trate a fonte de dados como uma fonte de dados local protegida por um firewall mesmo quando você usar o Microsoft Azure ExpressRoute. Use o Integration Runtime auto-hospedado para estabelecer a conectividade entre o serviço e a fonte de dados.
- Você deverá usar o tempo de execução da integração auto-hospedada mesmo se o armazenamento de dados estiver na nuvem em uma máquina virtual de IaaS do Azure.
- As tarefas podem falhar em um tempo de execução da integração auto-hospedada instalado em um Windows Server no qual a criptografia em conformidade com FIPS está habilitada. Para contornar esse problema, desabilite a criptografia em conformidade com FIPS no servidor. Para desabilitar a criptografia compatível com FIPS, altere o valor de registro de 1 (habilitado) a seguir para 0 (desabilitado): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Pré-requisitos

- As versões de sistema operacional com suporte são o Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 e Windows Server 2019. A instalação do tempo de execução da integração auto-hospedada em um controlador de domínio não tem suporte.
- É necessário o .NET Framework 4.6.1 ou posterior. Se você estiver instalando o tempo de execução da integração auto-hospedada em um computador com Windows 7, instale o .NET Framework 4.6.1 ou posterior. Confira [Requisitos de sistema do .NET Framework](/dotnet/framework/get-started/system-requirements) para obter detalhes.
- A configuração recomendada para o computador do tempo de execução da integração auto-hospedada é de, no mínimo, 2 GHz, quatro núcleos, 8 GB de RAM e um disco de 80 GB.
- Se o computador host hibernar, o Integration Runtime auto-hospedado não responderá às solicitações de dados. Configure um plano de energia adequado no computador antes de instalar o tempo de execução da integração auto-hospedada. Se o computador estiver configurado para hibernar, a instalação do Integration Runtime auto-hospedado exibirá uma mensagem.
- Você deve ser um administrador no computador local para instalar e configurar com êxito o Integration Runtime auto-hospedado.
- Execuções de atividade de cópia ocorrem em uma frequência específica. O uso de recursos (CPU, memória) no computador segue o mesmo padrão em horários ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados sendo movida. Quando vários trabalhos de cópia estiverem em andamento, você verá o uso do recurso aumentar durante horários de pico.
- As tarefas podem falhar ao extrair dados nos formatos parquet, ORC ou Avro. A criação do arquivo é executada no computador de integração auto-hospedado e requer que os pré-requisitos a seguir funcionem conforme o esperado (consulte o [formato parquet no Azure data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Pacote C++ redistribuível do Visual 2010](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) (x64)
    - Java Runtime (JRE) versão 8 de um provedor do JRE, como [adotar o OpenJDK](https://adoptopenjdk.net/), garantindo que a variável de ambiente `JAVA_HOME` esteja definida.

## <a name="installation-best-practices"></a>Melhores práticas de instalação
É possível instalar o tempo de execução da integração auto-hospedada baixando um pacote de instalação MSI no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Veja o artigo [Mover dados entre locais e a nuvem](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure um plano de energia no computador host para o tempo de execução da integração auto-hospedada para que o computador não hiberne. Se o computador host hibernar, o tempo de execução da integração auto-hospedada ficará offline.
- Faça backup das credenciais associadas ao Integration Runtime auto-hospedado regularmente.
- Para automatizar as operações de configuração de IR de hospedagem interna, consulte **configurar um ir de autohospedado existente por meio do PowerShell** nesta [seção](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Instalar e registrar o IR auto-hospedado no Centro de Download

1. Acesse a [página de downloads do Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Selecione **Baixar**, selecione a versão de 64 bits (a de 32 bits não é compatível) e selecione **Avançar**.
3. Execute o arquivo MSI diretamente ou salve-o em seu disco rígido e execute-o.
4. Na página de **Boas-vindas**, selecione um idioma e selecione **Avançar**.
5. Aceite os Termos de Licença para Software Microsoft e selecione **Avançar**.
6. Selecione **pasta** para instalar o tempo de execução da integração auto-hospedada e selecione **Avançar**.
7. Na página **Pronto para instalar**, selecione **Instalar**.
8. Clique em **Concluir** para finalizar a instalação.
9. Obtenha a chave de autenticação usando o Azure PowerShell. Aqui há um exemplo do PowerShell para recuperar a chave de autenticação:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na página **Registrar Integration Runtime (auto-hospedado)** do Configuration Manager do Microsoft Integration Runtime em seu computador, realize as seguintes etapas:

    a. Cole a chave de autenticação na área de texto.

    b. Opcionalmente, selecione **Mostrar chave de autenticação** para ver o texto da chave.

    c. Selecione **Registrar**.


## <a name="high-availability-and-scalability"></a>Alta disponibilidade e escalabilidade
Um tempo de execução de integração auto-hospedado pode ser associado a vários computadores locais ou máquinas virtuais no Azure. Esses computadores são chamados de nós. Você pode ter até quatro nós associados a um tempo de execução da integração auto-hospedada. Os benefícios de ter vários nós (computadores locais com um gateway instalado) para um gateway lógico são:
* Disponibilidade superior do tempo de execução da integração auto-hospedada para que ele não seja o único ponto de falha na sua solução de Big Data ou integração de dados de nuvem com o Azure Data Factory, garantindo a continuidade com até quatro nós.
* Desempenho e taxa de transferência aprimorados durante a movimentação de dados entre os armazenamentos de dados de nuvem e locais. Obtenha mais informações sobre [comparações de desempenho](copy-activity-performance.md).

Para associar vários nós, instale o software do tempo de execução da integração auto-hospedada no [Centro de Download](https://www.microsoft.com/download/details.aspx?id=39717). Em seguida, registre-o usando qualquer uma das chaves de autenticação obtidas do cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey** , conforme descrito no [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Você não precisa criar um novo tempo de execução da integração auto-hospedada para associar cada nó. Você pode instalar o tempo de execução da integração auto-hospedada em outro computador e registrá-lo usando a mesma chave de autenticação. 

> [!NOTE]
> Antes de adicionar outro nó para alta disponibilidade e escalabilidade, verifique se a opção **Acesso remoto à intranet** está habilitada no primeiro nó (**Configuration Manager do Microsoft Integration Runtime** > **Configurações** > **Acesso remoto à intranet**). 

### <a name="scale-considerations"></a>Considerações de escala

#### <a name="scale-out"></a>Expansão

Quando a memória disponível no IR auto-hospedado for baixa e o uso da CPI for alto, adicionar um novo nó ajuda a expandir a carga entre os computadores. Se as atividades falharem por causa de tempo limite ou porque o nó do IR auto-hospedado ficou offline, ajudará se você adicionar um nó ao gateway.

#### <a name="scale-up"></a>Escalar verticalmente

Quando a memória disponível e a CPU não são bem utilizadas, mas a execução de tarefas simultâneas está atingindo o limite, você deve aumentar o número de tarefas simultâneas que podem ser executadas em um nó. Convém também escalar verticalmente quando as atividades estiverem atingindo o tempo limite porque o IR auto-hospedado está sobrecarregado. Conforme mostrado na imagem a seguir, você pode aumentar a capacidade máxima de um nó:  

![Aumentando trabalhos simultâneos que podem ser executados em um nó](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL

Aqui estão os requisitos para o certificado TLS/SSL usado para proteger as comunicações entre os nós do tempo de execução de integração:

- O certificado deve ser um certificado X509 v3 publicamente confiável. É recomendável que você use certificados emitidos por uma AC (autoridade de certificação) pública (parceira).
- Cada nó do tempo de execução de integração deve confiar nesse certificado.
- Não recomendamos certificados SAN (nome alternativo da entidade) porque apenas o último item de SAN será usado; todos os outros serão ignorados devido às limitações atuais. Por exemplo, se você tiver um certificado SAN cujos SANs são **node1.domain.contoso.com** e **node2.domain.contoso.com**, só poderá usar esse certificado no computador cujo FQDN é **node2.domain.contoso.com**.
- O certificado dá suporte a qualquer tamanho de chave com suporte pelo Windows Server 2012 R2 para certificados SSL.
- Não há suporte a certificados que usam chaves CNG.  

> [!NOTE]
> Esse certificado é usado para criptografar portas no nó IR auto-hospedado, usado para **comunicação de nó para nó** (para sincronização de estado que inclui sincronização de credenciais de serviços vinculados entre nós) e ao **usar o cmdlet do PowerShell para configuração de credencial de serviço vinculada** de dentro da rede local. Sugerimos usar este certificado se o seu ambiente de rede privada não é seguro ou se você gostaria de proteger a comunicação entre os nós na sua rede privada também. A movimentação de dados em trânsito do IR auto-hospedado para outros armazenamentos de dados sempre ocorre por meio de um canal criptografado, independentemente desse certificado estar definido ou não. 

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Criar um tempo de execução de integração auto-hospedado compartilhado no Azure Data Factory

É possível reutilizar uma infraestrutura de tempo de execução da integração auto-hospedada existente já configurada em um data factory. Isso permite que você crie um *tempo de execução da integração auto-hospedada vinculado* em um data factory diferente, referenciando um IR auto-hospedado existente (compartilhado).

Para uma introdução de doze minutos e demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **IR compartilhado**: o IR auto-hospedado original executado em uma infraestrutura física.  
- **IR vinculado**: o tempo de execução de integração que faz referência a outro IR compartilhado. Esse é um IR lógico e usa a infraestrutura de outro IR auto-hospedado (compartilhado).

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Métodos para compartilhar um tempo de execução de integração auto-hospedado

Para compartilhar um tempo de execução de integração auto-hospedado com várias fábricas de dados, consulte [esta instrução](create-shared-self-hosted-integration-runtime-powershell.md) para obter detalhes.

### <a name="monitoring"></a>Monitoramento

- **IR compartilhado**

  ![Seleções para encontrar um tempo de execução de integração compartilhado](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Guia para monitoramento](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **IR vinculado**

  ![Seleções para encontrar um tempo de execução de integração vinculado](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Guia para monitoramento](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitações conhecidas do compartilhamento de IR auto-hospedado

* O data factory em que um tempo de execução de integração vinculado será criado precisa ter um [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Por padrão, os data factories criados no portal do Azure ou nos cmdlets do PowerShell têm uma MSI criada implicitamente. No entanto, quando um data factory é criado por meio de um modelo ou SDK do Azure Resource Manager, a propriedade **Identidade** deve ser definida explicitamente para garantir que o Azure Resource Manager crie um data factory que contenha uma MSI. 

* O SDK do .NET do Azure Data Factory que dá suporte a esse recurso é a versão 1.1.0 ou posterior.

* Para conceder permissão, o usuário precisa da função Proprietário ou da função Proprietário herdada na Data Factory em que o tempo de execução de integração compartilhado existe.

* O recurso de compartilhamento só funciona para Fábricas de dados no mesmo locatário do Microsoft Azure Active Directory.

* Para [usuários convidados](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) do Active Directory, a funcionalidade de pesquisa (que lista todos os data factories usando uma palavra-chave de pesquisa) na interface do usuário [não funciona](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). No entanto, desde que o usuário convidado seja o Proprietário do data factory, pode compartilhar o tempo de execução de integração sem a funcionalidade de pesquisa, digitando diretamente a MSI do data factory com que o tempo de execução de integração precisa ser compartilhado na caixa de texto **Atribuir permissão** e selecionando **Adicionar** na interface do usuário do Azure Data Factory. 

  > [!NOTE]
  > Esse recurso está disponível apenas no Azure Data Factory V2. 

## <a name="notification-area-icons-and-notifications"></a>Ícones e notificações da área de notificação

Se você mover o cursor sobre o ícone ou mensagem na área de notificação, poderá encontrar detalhes sobre o estado do tempo de execução da integração auto-hospedada.

![Notificações na área de notificação](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portas e firewall

Há dois firewalls a considerar: o *firewall corporativo* em execução no roteador central da organização e o *Firewall do Windows* configurado como um daemon no computador local em que o tempo de execução da integração auto-hospedada está instalado.

![Firewall](media/create-self-hosted-integration-runtime/firewall.png)

No nível do *firewall corporativo*, é necessário configurar os seguintes domínios e portas de saída:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


No nível do *Firewall do Windows* (nível do computador), essas portas de saída normalmente estão habilitadas. Caso contrário, você poderá configurar as portas e os domínios adequadamente em um computador com tempo de execução da integração auto-hospedada.

> [!NOTE]
> Com base na fonte e nos coletores, talvez seja necessário permitir domínios adicionais e portas de saída no firewall corporativo ou no firewall do Windows.
>
> Para alguns bancos de dados de nuvem (por exemplo, banco de dados SQL do Azure e Azure Data Lake), talvez seja necessário permitir endereços IP de máquinas de tempo de execução de integração auto-hospedados em sua configuração de firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados de uma origem para um coletor

Verifique se as regras de firewall estão habilitadas corretamente no firewall corporativo, no Firewall do Windows no computador do tempo de execução da integração auto-hospedada e no próprio armazenamento de dados. Habilitar essas regras permite ao Integration Runtime auto-hospedado se conectar com êxito à origem e ao coletor. Habilite as regras para cada repositório de dados que esteja envolvido na operação de cópia.

Por exemplo, para copiar de um armazenamento de dados local para um coletor do Banco de Dados SQL do Azure ou um coletor do SQL Data Warehouse do Azure, siga as etapas abaixo:

1. Permitir a comunicação TCP de saída na porta 1433 para o Firewall do Windows e o firewall corporativo.
2. Configurar as definições de firewall do Banco de Dados SQL do Azure para adicionar o endereço IP do computador do tempo de execução da integração auto-hospedada à lista de endereços IP permitidos.

> [!NOTE]
> Se o firewall não permitir a porta de saída 1433, o tempo de execução da integração auto-hospedada não poderá acessar diretamente o Banco de Dados SQL do Azure. Nesse caso, você pode usar uma [cópia em etapas](copy-activity-performance.md) para o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. Neste cenário, você precisaria apenas de HTTPS (porta 443) para a movimentação de dados.

## <a name="proxy-server-considerations"></a>Considerações do servidor proxy

Se o ambiente de rede corporativo usar um servidor proxy para acessar a Internet, configure o tempo de execução da integração auto-hospedada para usar as definições de proxy apropriadas. Você pode definir o proxy durante a fase de registro inicial.

![Especificar o proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Quando configurado, o Integration Runtime de hospedagem interna usa o servidor proxy para se conectar ao serviço de nuvem, origem/destino (aqueles que usam o protocolo HTTP/HTTPS). Isso é selecionar **Alterar link** durante a configuração inicial. Você verá a caixa de diálogo de configuração de proxy.

![Configurar proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Há três opções de configuração:

- **Não usar proxy**: o tempo de execução da integração auto-hospedada não usa explicitamente qualquer proxy para se conectar aos serviços de nuvem.
- **Usar o proxy do sistema**: o Integration Runtime de hospedagem interna usa a configuração de proxy configurada em diahost. exe. config e diawp. exe. config. Se nenhum proxy estiver configurado em diahost. exe. config e diawp. exe. config, o Integration Runtime de hospedagem interna se conectará diretamente ao serviço de nuvem sem passar por um proxy.
- **Usar proxy personalizado**: defina a configuração de proxy http a ser usada para o tempo de execução de integração auto-hospedado, em vez de usar as configurações em diahost. exe. config e diawp. exe. config. O **endereço** e a **porta** são necessários. O **Nome de Usuário** e a **Senha** são opcionais, dependendo da configuração de autenticação do proxy. Todas as configurações são criptografadas com a DPAPI do Windows no Integration Runtime auto-hospedado e armazenados localmente no computador.

O Serviço de Host do Integration Runtime é reiniciado automaticamente depois que você salva as configurações de proxy atualizadas.

Depois que o tempo de execução da integração auto-hospedada tiver sido registrado com êxito, se você quiser exibir ou atualizar as configurações de proxy, use o Configuration Manager do Integration Runtime.

1. Abra o **Configuration Manager do Microsoft Integration Runtime**.
2. Alterne para a guia **Configurações** .
3. Selecione o link **Alterar** na seção **Proxy HTTP** para abrir a caixa de diálogo **Configurar proxy HTTP**.
4. Selecione **Avançar**. Você verá um aviso solicitando sua permissão para salvar a configuração de proxy e reiniciar o serviço de host de tempo de execução de integração.

É possível exibir e atualizar o proxy HTTP usando a ferramenta Configuration Manager.

![Exibir proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se você configurar um servidor proxy com autenticação NTLM, o serviço de host de tempo de execução de integração será executado sob a conta de domínio. Se você alterar a senha da conta de domínio posteriormente, lembre-se de atualizar as definições de configuração para o serviço e reiniciá-lo adequadamente. Por conta desse requisito, sugerimos o uso de uma conta de domínio dedicada para acessar o servidor proxy que não exija a atualização da senha com frequência.

### <a name="configure-proxy-server-settings"></a>Definir configurações do servidor proxy

Se você selecionar a configuração **usar proxy do sistema** para o proxy http, o tempo de execução de integração auto-hospedado usará a configuração de proxy em diahost. exe. config e diawp. exe. config. Se nenhum proxy for especificado em diahost. exe. config e diawp. exe. config, o Integration Runtime de hospedagem interna se conectará diretamente ao serviço de nuvem sem passar pelo proxy. O procedimento a seguir fornece instruções para atualizar o arquivo diahost.exe.config:

1. No Explorador de Arquivos, faça uma cópia de segurança de C:\Arquivos de Programas\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config para fazer backup do arquivo original.
2. Abra o notepad. exe em execução como administrador e abra o arquivo de texto C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Localize a marca padrão para system.net, conforme mostrado no código a seguir:

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

    Propriedades adicionais são permitidas dentro da tag de proxy para especificar as configurações necessárias, como `scriptLocation`. Confira [Elemento proxy (Configurações de Rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) para encontrar a sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Salve o arquivo de configuração no local original. Depois, reinicie o serviço de host de tempo de execução da integração auto-hospedada, que assimila as alterações. 

   Para reiniciar o serviço, use o applet de serviços no painel de controle. Ou, no Configuration Manager do Integration Runtime, selecione o botão **Interromper serviço** e, depois, selecione **Iniciar serviço**. 
   
   Se o serviço não inicia, é provável que uma sintaxe de marca XML incorreta tenha sido adicionada ao arquivo de configuração de aplicativo que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar diahost.exe.config e diawp.exe.config.

Você também precisa ter certeza de que Microsoft Azure está na lista de permissões da sua empresa. É possível baixar a lista de endereços IP válidos do Microsoft Azure no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possíveis sintomas de problemas relacionados ao firewall e ao servidor proxy

Se você encontrar erros similares aos descritos a seguir, eles provavelmente serão devidos à configuração incorreta do servidor proxy ou firewall, que impedirá o tempo de execução da integração auto-hospedada de se conectar ao Data Factory para se autenticar. Para garantir que seu firewall e servidor proxy estejam configurados corretamente, confira a seção anterior.

* Quando você tenta registrar o Integration Runtime auto-hospedado, recebe o seguinte erro: “Falha ao registrar esse nó do Integration Runtime. Confirme se a chave de autenticação é válida e o serviço de host de serviço de integração está em execução neste computador".
* Ao abrir o Configuration Manager do Integration Runtime, você vê o status **Desconectado** ou **Conectando**. Ao exibir os logs de eventos do Windows, em **Visualizador de Eventos** > **Logs de aplicativos e serviços** > **Microsoft Integration Runtime**, você vê mensagens de erro como esta:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Habilitação de acesso remoto por uma intranet 

Se você usar o PowerShell para criptografar credenciais de outro computador (na rede) diferente de onde o tempo de execução de integração auto-hospedado está instalado, você poderá habilitar a opção **acesso remoto da intranet** . Se você executar o PowerShell para criptografar credenciais no mesmo computador em que o tempo de execução de integração auto-hospedado está instalado, não será possível habilitar o **acesso remoto da intranet**.

Você deve habilitar a opção **Acesso remoto pela intranet** antes de adicionar outro nó para alta disponibilidade e escalabilidade.  

Durante a configuração do tempo de execução da integração auto-hospedada (da versão 3.3.xxxx.x em diante), a instalação do tempo de execução da integração auto-hospedada desabilita, por padrão, a opção **Acesso remoto pela intranet** no computador do tempo de execução da integração auto-hospedada.

Se estiver usando um firewall de terceiros, poderá abrir manualmente a porta 8060 (ou a porta configurada pelo usuário). Se tiver um problema de firewall durante a configuração do tempo de execução da integração auto-hospedada, tente usar o comando a seguir para instalar o tempo de execução da integração auto-hospedada sem configurar o firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se optar por não abrir a porta 8060 no computador do tempo de execução da integração auto-hospedada, use mecanismos diferentes do aplicativo Definindo Credenciais para configurar as credenciais do armazenamento de dados. Por exemplo, você pode usar o cmdlet do PowerShell **New-AzDataFactoryV2LinkedServiceEncryptCredential** .


## <a name="next-steps"></a>Próximas etapas
Confira o tutorial a seguir para obter instruções passo a passo: [Tutorial: cópia de dados locais para a nuvem](tutorial-hybrid-copy-powershell.md).
