---
title: Registrar uma fonte de Teradata e configurar exames (versão prévia)
description: Este artigo descreve como registrar uma fonte de dados de Teradata no Azure Purview e configurar um exame.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841610"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registrar e examinar fonte de Teradata (versão prévia)

Este artigo descreve como registrar uma fonte de dados de Teradata no Purview e configurar um exame.

> [!IMPORTANT]
> Essa fonte de dados está na versão prévia. Você pode experimentá-lo e oferecer comentários.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A fonte de Teradata dá suporte ao **Exame completo** para extrair metadados de um banco de dados Teradata e busca a **Linhagem** entre os ativos de dados.

## <a name="prerequisites"></a>Pré-requisitos

- O conector dá suporte ao armazenamento de dados localizado somente em uma rede local, uma rede virtual do Azure ou na Amazon Virtual Private Cloud. Sendo assim, você precisa configurar um [runtime de integração auto-hospedada](manage-integration-runtimes.md) para se conectar a ele.

- Verifique se o JRE (Java Runtime Environment) está instalado na máquina virtual em que o runtime de integração auto-hospedada está instalado.
 
- Verifique se o "Pacote Redistribuível do Visual C++ 2012, Atualização 4" está instalado no computador do runtime de integração auto-hospedada. Se ele ainda não estiver instalado, baixe-o [aqui](https://www.microsoft.com/download/details.aspx?id=30679).

- Você precisará instalar manualmente um driver chamado Teradata JDBC Driver na máquina virtual local. O arquivo JAR executável pode ser baixado do [Site do Teradata](https://downloads.teradata.com/).

    > [!Note] 
    > O driver deve estar acessível para todas as contas na VM. Não instale em uma conta de usuário.

- As versões do banco de dados Teradata com suporte são **12.x a 16.x**. Certifique-se de ter Acesso de leitura à fonte de Teradata que está sendo examinada.

### <a name="feature-flag"></a>Sinalizador de recurso

O registro e o exame de uma fonte de Teradata estão disponíveis por trás de um sinalizador de recurso. Acrescente o seguinte à sua URL: *?feature.ext.datasource=%7b"teradata":"true"%7d* 

Por exemplo, a URL completa [https://web.purview.azure.com/?feature.ext.datasource=%7b"metadata":"true"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação
A única maneira com suporte para configurar a autenticação para uma fonte de Teradata é a autenticação de **Banco de dados básica**

## <a name="register-a-teradata-source"></a>Registrar uma fonte de Teradata

Para registrar uma nova fonte de Teradata no catálogo de dados, faça o seguinte:

1. Acesse sua conta do Purview
2. Selecionar **Fontes** no painel de navegação à esquerda
3. Escolha **Registrar**
4. Em **Registrar fontes**, selecione **Teradata**
5. Selecione **Continuar**

Na tela **Registrar fontes (Teradata)** , faça o seguinte:

1. Insira um **Nome** com a qual a fonte de dados será listada no Catálogo.
2. Insira o nome do **Host** para se conectar a uma fonte de Teradata. Ele também pode ser um endereço IP ou uma cadeia de conexão totalmente qualificada para o servidor.
3. Selecionar ou criar uma coleção (opcional)
4. **Conclua** a etapa para registrar a fonte de dados.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="opções de registro de fontes" border="true":::

## <a name="creating-and-running-a-scan"></a>Criando e executando uma verificação

Para criar e executar uma nova verificação, faça o seguinte:
1. No Centro de Gerenciamento, clique em *Runtimes de integração*. Verifique se um runtime de integração auto-hospedada está configurado. Se ele não estiver configurado, use as etapas mencionadas [aqui](manage-integration-runtimes.md) para criar um runtime de integração auto-hospedada para exame em uma VM do Azure ou local com acesso à rede local.

2. Em seguida, navegue até **Fontes**

3. Selecione a fonte de Teradata registrada.

4. Selecione + Nova verificação
 
5. Forneça os detalhes abaixo:

    - Nome: O nome da verificação

    - Conectar-se por meio do runtime de integração: Selecione o runtime de integração auto-hospedada

    - Método de autenticação: A autenticação do banco de dados é a única opção com suporte por enquanto. Ela será selecionada por padrão

    - Nome de usuário: Um nome de usuário para se conectar ao servidor de banco de dados. Esse nome de usuário deve ter acesso de leitura ao servidor

    - Senha: a senha de usuário usada para se conectar ao servidor de banco de dados

    - Esquema: liste o subconjunto de esquemas a serem importados expressos como uma lista separada por ponto e vírgula. por exemplo, esquema1; esquema2. Todos os esquemas de usuário serão importados se essa lista estiver vazia. Todos os esquemas (por exemplo, SysAdmin) e objetos do sistema são ignorados por padrão.

        Padrões de nome de esquema aceitáveis usando a sintaxe de expressões SQL LIKE incluem o uso de %, por exemplo, A%; %B; %C%; D
        - começar com A ou    
        - terminar com B ou    
        - conter C ou    
        - igual a D

        O uso de NOT e de caracteres especiais não é aceitável

    - Localização do driver: caminho completo para a localização do driver Teradata na VM do cliente. O nome do driver JDBC do Teradata deve ser: com.teradata.jdbc.TeraDriver

    - Memória máxima disponível: Memória máxima (em GB) disponível na VM do cliente a ser usada pelos processos de verificação. Isso depende do tamanho da fonte de Teradata a ser examinada.

    > [!Note] 
    > Como regra geral, forneça 2 GB de memória para cada 1000 tabelas

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="configuração do exame" border="true":::

6. Clique em *Continuar*.

7. Escolha o gatilho da verificação. Você pode configurar uma agenda ou executar o exame uma vez.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="gatilho de exame" border="true":::

8. Analise o exame e clique em *Salvar e executar*.

## <a name="viewing-your-scans-and-scan-runs"></a>Exibindo as verificações e as execuções de verificação

1. Navegue até o centro de gerenciamento. Selecione **Fontes de dados** na seção **Fontes e verificação**.

2. Selecione a fonte de dados desejada. Você verá uma lista de verificações existentes nessa fonte de dados.

3. Selecione a verificação cujos resultados você está interessado em exibir.

4. Esta página mostrará que todas as verificações anteriores são executadas junto com as métricas e o status de cada execução de verificação. Ele também exibirá se a verificação foi agendada ou manual, quantos ativos tiveram classificações aplicadas, quantos ativos totais foram descobertos, a hora de início e de término da verificação e a duração total da verificação.

## <a name="manage-your-scans"></a>Gerenciar os exames

Para gerenciar ou excluir uma verificação, faça o seguinte:

1. Navegue até o centro de gerenciamento. Selecione **Fontes de dados** na seção **Fontes e verificação** e selecione a fonte de dados desejada.

2. Selecione a verificação que você deseja gerenciar. Você pode editar a verificação selecionando **Editar**.

3. Você pode excluir sua verificação selecionando **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)
