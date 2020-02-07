---
title: Instalar o Azure FarmBeats
description: Este artigo descreve como instalar o Azure FarmBeats em sua assinatura do Azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0702b302af1c964014a6649f5f3e86ce47b4600a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048388"
---
# <a name="install-azure-farmbeats"></a>Instalar o Azure FarmBeats

Este artigo descreve como instalar o Azure FarmBeats em sua assinatura do Azure.

O Azure FarmBeats é uma oferta entre empresas disponível no Azure Marketplace. Ele permite a agregação de conjuntos de dados de agricultura entre provedores e geração de informações acionáveis. O Azure FarmBeats faz isso permitindo que você crie modelos de ia (inteligência artificial) ou de ML (aprendizado de máquina) com base em conjuntos de dados com fusível. Os dois principais componentes do Azure FarmBeats são:

- **Datahub**: uma camada de API que habilita a agregação, a normalização e a condefinição de vários conjuntos de dados de agricultura em diferentes provedores.

- **Acelerador**: aplicativo Web criado com base em Datahub. Ele inicia o desenvolvimento e a visualização do modelo. O acelerador usa as APIs FarmBeats do Azure para demonstrar a visualização de dados de sensor ingeridos como gráficos e visualização de saída de modelo como mapas.

## <a name="general-information"></a>Informações gerais

### <a name="components-installed"></a>Componentes instalados

Quando você instala o Azure FarmBeats, os seguintes recursos são provisionados em sua assinatura do Azure:

| Recursos do Azure instalados  | Componente FarmBeats do Azure  |
|---------|---------|
| Application Insights   |      Acelerador de & Datahub      |
| Serviço de Aplicativo     |     Acelerador de & Datahub     |
| Plano do Serviço de Aplicativo   | Acelerador de & Datahub  |
| Conexão da API    |  Datahub       |
| Cache Redis do Azure       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Acelerador de & Datahub      |
| Conta do Lote do Azure    | Datahub   |
| Azure Key Vault |  Acelerador de & Datahub        |
| Conta do Azure Maps       |     Acelerador    |
| Namespace do hub de eventos    |     Datahub      |
| Aplicativo Lógico      |  Datahub       |
| Conta de Armazenamento      |     Acelerador de & Datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Custos incorridos

O custo da FarmBeats do Azure é uma agregação do custo dos serviços do Azure subjacentes. As informações de preço para os serviços do Azure podem ser calculadas usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator). O custo real da instalação total irá variar com base no uso. O custo de estado estacionário para os dois componentes é:

- Datahub-menos de $10 por dia
- Acelerador-menos de $2 por dia

### <a name="regions-supported"></a>Regiões com suporte

Atualmente, o Azure FarmBeats tem suporte em ambientes de nuvem pública nas seguintes regiões:

- Leste da Austrália
- EUA Central
- Leste dos EUA
- Leste dos EUA 2
- Oeste dos EUA
- Oeste dos EUA 2
- Norte da Europa
- Europa Ocidental
- Leste da Ásia
- Sudeste da Ásia

### <a name="time-taken"></a>Tempo decorrido

Toda a configuração do Azure FarmBeats, incluindo a preparação e a instalação, levará menos de uma hora.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Antes de iniciar a instalação real do Azure FarmBeats, você precisará concluir as seguintes etapas:

### <a name="verify-permissions"></a>Verificar permissões

Você precisará das seguintes permissões no locatário do Azure que você está procurando para instalar o Azure FarmBeats-

- Locatário – criador de aplicativos do AAD
- Assinatura-proprietário
- Grupo de recursos no qual o FarmBeats está sendo instalado-proprietário

As duas primeiras permissões são necessárias para [criar a etapa do aplicativo do AAD](#create-an-aad-application) . Se necessário, você pode obter uma pessoa com as permissões apropriadas para criar o aplicativo do AAD. A pessoa que está instalando o FarmBeats precisa ser um proprietário do grupo de recursos no qual o FarmBeats está sendo instalado.

Você pode verificar suas permissões de acesso no portal do Azure seguindo as instruções em [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/check-access)

### <a name="decide-subscription-and-region"></a>Decidir assinatura e região

Você precisará da ID de assinatura do Azure e da região em que deseja instalar o Azure FarmBeats. Escolha uma das regiões listadas na seção [regiões com suporte](#regions-supported) .

Anote a **ID da assinatura do Azure** e a **região do Azure**.

### <a name="create-an-aad-application"></a>Criar um aplicativo do AAD

O Azure FarmBeats requer a criação e o registro do aplicativo Azure Active Directory. Para executar o script de criação do AAD com êxito, as permissões a seguir são necessárias-

- Locatário – criador de aplicativos do AAD
- Assinatura-proprietário

Execute as etapas a seguir em uma instância de Cloud Shell usando o ambiente do PowerShell. Os usuários de primeira hora receberão uma solicitação para selecionar uma assinatura e criar uma conta de armazenamento. Conclua a configuração conforme instruído.

1. Baixar o [script do gerador de aplicativo do AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Por padrão, o arquivo é baixado em seu diretório base. Navegue até o diretório.

    ```azurepowershell-interactive
        cd
    ```

3. Executar o script do AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. O script do AAD leva cerca de 2 minutos para ser executado e gera valores para a tela, bem como para um arquivo JSON no mesmo diretório. Se você tiver outra pessoa que executa o script, peça-lhe para compartilhar essa saída com você.

### <a name="create-sentinel-account"></a>Criar conta do Sentinel

A configuração do Azure FarmBeats permite que você obtenha imagens satélite da missão satélite [Sentinel-2](https://scihub.copernicus.eu/) da Agência de espaço europeu para seu farm. Para configurar essa instalação, você precisa de uma conta do Sentinel.

Siga as etapas para criar uma conta gratuita com o sentinela:

1. Acesse a página de [inscrição](https://aka.ms/SentinelRegistration) oficial.
2. Forneça os detalhes necessários (nome, sobrenome, nome de usuário, senha e ID de email) e preencha o formulário.
3. Um link de verificação será enviado para a ID de email registrado. Selecione o link fornecido no email e conclua a verificação.

O processo de registro é concluído após a conclusão da verificação. Anote seu **nome de usuário Sentinel** e a **senha Sentinel**.

## <a name="install"></a>Instalar

Agora você está pronto para instalar o FarmBeats. Siga as etapas abaixo para iniciar a instalação-

1. Entre no Portal do Azure. Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD no qual você deseja instalar o Azure FarmBeats.

2. Vá para o Azure Marketplace no portal e pesquise **FarmBeats do Azure** no Marketplace.

3. Uma nova janela com uma visão geral do Azure FarmBeats é exibida. Selecione **Criar**.

4. Uma nova janela é exibida. Conclua o processo de inscrição escolhendo a assinatura, o grupo de recursos e o local corretos para os quais você deseja instalar o Azure FarmBeats.

5. Forneça o endereço de email que deve receber alertas de serviço relacionados ao FarmBeats do Azure na seção de **alertas do serviço FarmBeats** . Clique em avançar na parte inferior da página para ir para a guia **dependências** . ![guia noções básicas](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Copie as entradas individuais da saída do [script do AAD](#create-an-aad-application) para as entradas na seção aplicativo do AAD.

7. Insira o nome de usuário e a senha da [conta do Sentinel](#create-sentinel-account) na seção conta do Sentinel. Clique em avançar para ir para a guia **revisar + criar** ![guia dependências](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Depois que os detalhes inseridos forem validados, selecione **OK**. A página Termos de uso é exibida. Examine os termos e selecione **criar** para iniciar a instalação. Você será redirecionado automaticamente para uma página na qual poderá seguir o progresso da instalação.

Depois que a instalação for concluída, você poderá verificar a instalação e começar a usar o portal do FarmBeats navegando até o nome do site fornecido durante a instalação: https://\<FarmBeats-site-Name >. azurewebsites. net. Você deve ver a interface do usuário do FarmBeats com uma opção para criar farms.

**Datahub** pode ser encontrado em https://\<FarmBeats-website-Name >-API. azurewebsites. net/Swagger. Aqui, você verá os diferentes objetos da API FarmBeats e executará operações REST nas APIs.

## <a name="upgrade"></a>Atualizar

Para atualizar o FarmBeats para a versão mais recente, execute as etapas a seguir em uma instância de Cloud Shell usando o ambiente do PowerShell. O usuário precisará ser o proprietário da assinatura na qual o FarmBeats está instalado.
Os usuários de primeira hora receberão uma solicitação para selecionar uma assinatura e criar uma conta de armazenamento. Conclua a configuração conforme instruído.

1. Baixar o [script de atualização](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./update-farmbeats.ps1
    ```

2. Por padrão, o arquivo é baixado em seu diretório base. Navegue até o diretório.

    ```azurepowershell-interactive
        cd
    ```

3. Executar o script de atualização

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

O caminho para o arquivo Input. JSON é opcional. Se não for especificado, o script solicitará todas as entradas necessárias. A atualização deve ser concluída em cerca de 30 minutos.

## <a name="uninstall"></a>Desinstalar

Para desinstalar o FarmBeats Datahub ou acelerador do Azure, conclua as seguintes etapas:

1. Faça logon no portal do Azure e **exclua os grupos de recursos** nos quais esses componentes estão instalados.

2. Vá para Azure Active Directory & **excluir o aplicativo do Azure ad** vinculado à instalação do FarmBeats do Azure.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Você aprendeu a instalar o Azure FarmBeats em sua assinatura do Azure. Agora, saiba como [Adicionar usuários](manage-users-in-azure-farmbeats.md#manage-users) à instância do FarmBeats do Azure.
