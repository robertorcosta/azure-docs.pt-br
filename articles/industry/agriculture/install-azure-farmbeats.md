---
title: Instalar o Azure FarmBeats
description: Este artigo descreve como instalar o Azure FarmBeats em sua assinatura do Azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479552"
---
# <a name="install-azure-farmbeats"></a>Instalar o Azure FarmBeats

Este artigo descreve como instalar o Azure FarmBeats em sua assinatura do Azure.

O Azure FarmBeats é uma oferta entre empresas disponível no Azure Marketplace. Permite a agregação de conjuntos de dados agrícolas entre provedores e geração de insights acionáveis. O Azure FarmBeats faz isso permitindo que você construa modelos de inteligência artificial (IA) ou machine learning (ML) com base em conjuntos de dados fundidos. Os dois principais componentes do Azure FarmBeats são:

- **Datahub**: Uma camada de API que permite a agregação, normalização e contextualização de vários conjuntos de dados agrícolas em diferentes provedores.

- **Acelerador**: Aplicativo web que é construído em cima do Datahub. Ele inicia o desenvolvimento e visualização do seu modelo. O acelerador usa APIs Do Azure FarmBeats para demonstrar a visualização de dados de sensores ingeridos como gráficos e visualização da saída do modelo como mapas.

## <a name="general-information"></a>Informações gerais

### <a name="components-installed"></a>Componentes instalados

Quando você instala o Azure FarmBeats, os seguintes recursos são provisionados na sua assinatura do Azure:

| Recursos do Azure Instalados  | Componente Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Datahub & Accelerator      |
| Serviço de Aplicativo     |     Datahub & Accelerator     |
| Plano do Serviço de Aplicativo   | Datahub & Accelerator  |
| Conexão da API    |  Datahub       |
| Cache Redis do Azure       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & Accelerator      |
| Conta do Lote do Azure    | Datahub   |
| Cofre de Chave do Azure |  Datahub & Accelerator        |
| Conta do Azure Maps       |     Acelerador    |
| Espaço de nomes do Hub de Eventos    |     Datahub      |
| Aplicativo Lógico      |  Datahub       |
| Conta de Armazenamento      |     Datahub & Accelerator      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Custos incorridos

O custo do Azure FarmBeats é um agregado do custo dos serviços subjacentes do Azure. As informações de preços dos serviços do Azure podem ser calculadas usando a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator). O custo real da instalação total variará de acordo com o uso. O custo de estado estável para os dois componentes é:

- Datahub - menos de $10 por dia
- Acelerador - menos de $2 por dia

### <a name="regions-supported"></a>Regiões suportadas

Atualmente, o Azure FarmBeats é suportado em ambientes de nuvem pública nas seguintes regiões:

- Leste da Austrália
- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- Oeste dos EUA
- Oeste dos EUA 2
- Norte da Europa
- Europa Ocidental
- Leste da Ásia
- Sudeste asiático

### <a name="time-taken"></a>Tempo levado

Toda a configuração do Azure FarmBeats, incluindo a preparação e instalação levará menos de uma hora.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará concluir as seguintes etapas antes de iniciar a instalação real do Azure FarmBeats:

### <a name="verify-permissions"></a>Verificar permissões

Você precisará das seguintes permissões no inquilino do Azure para instalar o Azure FarmBeats:

- Inquilino - Criador de aplicativos AAD
- Assinatura - Proprietário
- Grupo de recursos no qual farmbeats está sendo instalado - Proprietário

As duas primeiras permissões são necessárias para criar a etapa [do aplicativo AAD.](#create-an-aad-application) Se necessário, você pode obter alguém com as permissões apropriadas para criar o aplicativo AAD.

A pessoa que executa a instalação farmbeats a partir do marketplace precisa ser dona do Resource Group no qual o FarmBeats está sendo instalado. Para proprietários de assinaturas, isso acontece automaticamente quando o Resource Group é criado. Para outros, crie o Grupo de recursos e peça ao proprietário da Assinatura para torná-lo proprietário do Grupo de Recursos.

Você pode verificar suas permissões de acesso no portal Azure seguindo as instruções sobre [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Decida A Assinatura e região

Você precisará do ID de assinatura do Azure e da região onde deseja instalar o Azure FarmBeats. Escolha uma das regiões listadas na seção de suporte das [Regiões.](#regions-supported)

Anote o **ID de Assinatura do Azure** e a **Região Do Azure**.

### <a name="create-an-aad-application"></a>Crie um aplicativo AAD

O Azure FarmBeats exige a criação e registro de aplicativos do Azure Active Directory. Para executar com sucesso o script de criação AAD, são necessárias as seguintes permissões:

- Inquilino - Criador de aplicativos AAD
- Assinatura - Proprietário

Execute as etapas a seguir em uma instância Cloud Shell usando o ambiente PowerShell. Os usuários iniciantes serão solicitados a selecionar uma assinatura e criar uma conta de armazenamento. Complete a configuração como instruído.

1. Baixe o [script do gerador de aplicativos AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Por padrão, o arquivo é baixado para o diretório inicial. Navegue até o diretório.

    ```azurepowershell-interactive
        cd
    ```

3. Execute o script AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. O script pede as seguintes três entradas:

    - **Nome do site farmbeats**: Este é o prefixo de URL exclusivo para o seu aplicativo web FarmBeats. Caso o prefixo já esteja tomado, o script será errado. Uma vez instalado, sua implantação do\<FarmBeats estará acessível a partir de https:// nome do site FarmBeats\<>.azurewebsites.net e as APIs de swagger estarão em https:// farmbeats-website-name>-api.azurewebsites.net

    - **ID de login do Azure**: Forneça o ID de login do Azure para o usuário que você deseja ser adicionado como administrador do FarmBeats. Esse usuário pode, então, conceder acesso ao aplicativo web FarmBeats para outros usuários. O ID de login é john.doe@domain.comgeralmente do formulário . O Azure UPN também é suportado.

    - **ID de assinatura**: Este é o ID da assinatura em que você deseja instalar o Azure FarmBeats

5. O script AAD leva cerca de 2 minutos para executar e produzir valores na tela, bem como para um arquivo json no mesmo diretório. Se você tinha outra pessoa executar o script, peça-lhes para compartilhar esta saída com você.

### <a name="create-sentinel-account"></a>Criar conta sentinela

Sua configuração Azure FarmBeats permite que você obtenha imagens de satélite da missão de satélite [Sentinel-2](https://scihub.copernicus.eu/) da Agência Espacial Europeia para sua fazenda. Para configurar essa configuração, você precisa de uma conta do Sentinel.

Siga as etapas para criar uma conta gratuita com o Sentinel:

1. Acesse a página oficial [de inscrição.](https://aka.ms/SentinelRegistration)
2. Forneça os detalhes necessários (nome, sobrenome, nome de usuário, senha e ID de e-mail) e preencha o formulário.
3. Um link de verificação é enviado para o ID de e-mail registrado. Selecione o link fornecido no e-mail e complete a verificação.

Seu processo de inscrição está completo. Anote seu nome de **usuário sentinela** e **senha sentinela,** uma vez que a verificação também esteja concluída.

## <a name="install"></a>Instalar o

Agora você está pronto para instalar o FarmBeats. Siga as etapas abaixo para iniciar a instalação:

1. Entre no portal do Azure. Selecione sua conta no canto superior direito e mude para o inquilino Azure AD onde deseja instalar o Azure FarmBeats.

2. Acesse o Azure Marketplace dentro do portal e procure **o Azure FarmBeats** no Marketplace.

3. Uma nova janela com uma visão geral do Azure FarmBeats aparece. Selecione **Criar**.

4. Uma nova janela é exibida. Complete o processo de inscrição escolhendo a assinatura, o grupo de recursos e o local para o qual você deseja instalar o Azure FarmBeats.

5. Forneça o endereço de e-mail que deve receber quaisquer alertas de serviço relacionados ao Azure FarmBeats na seção **Alertas de Serviço FarmBeats.** Selecione **Next** na parte inferior da página para passar para a guia **Dependências.**

    ![Guia básico](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Copie as entradas individuais da saída do [script AAD](#create-an-aad-application) para as entradas na seção de aplicação AAD.

7. Digite o nome de usuário e a senha da [conta sentinela](#create-sentinel-account) na seção Conta sentinela. Selecione **Ao lado** para passar para a guia **'Revisar + Criar'.**

    ![Guia Dependências](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Uma vez que os detalhes inseridos sejam validados, selecione **OK**. A página Termos de uso é exibida. Revise os termos e selecione **Criar** para iniciar a instalação. Você será redirecionado para a página onde poderá acompanhar o progresso da instalação.

Uma vez que a instalação esteja concluída, você pode verificar a instalação e começar a\<usar o portal FarmBeats navegando até o nome do site que você forneceu durante a instalação: https:// nome do site farmbeats>.azurewebsites.net. Você deve ver a interface de usuário farmbeats com uma opção para criar Fazendas.

**O Datahub** pode\<ser encontrado em https:// nome do site FarmBeats>-api.azurewebsites.net/swagger. Aqui você verá os diferentes objetos da API FarmBeats e executará operações REST nas APIs.

## <a name="upgrade"></a>Atualizar

Para atualizar o FarmBeats para a versão mais recente, execute as etapas a seguir em uma instância cloud Shell usando o ambiente PowerShell. O usuário precisará ser o proprietário da assinatura na qual o FarmBeats está instalado.

Os usuários iniciantes serão solicitados a selecionar uma assinatura e criar uma conta de armazenamento. Complete a configuração como instruído.

1. Baixe o [script de atualização](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Por padrão, o arquivo é baixado para o diretório inicial. Navegue até o diretório.

    ```azurepowershell-interactive
        cd
    ```

3. Execute o script de atualização

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

O caminho para o arquivo input.json é opcional. Se não for especificado, o script solicitará todas as entradas necessárias. A atualização deve terminar em cerca de 30 minutos.

## <a name="uninstall"></a>Desinstalar

Para desinstalar o Azure FarmBeats Datahub ou o Accelerator, complete as seguintes etapas:

1. Faça login no portal Azure e **exclua os grupos de recursos** nos quais esses componentes estão instalados.

2. Vá ao Azure Active Directory & **exclua o aplicativo Azure AD** vinculado à instalação do Azure FarmBeats.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como instalar o Azure FarmBeats na sua assinatura do Azure. Agora, aprenda a [adicionar usuários](manage-users-in-azure-farmbeats.md#manage-users) à sua instância Azure FarmBeats.
