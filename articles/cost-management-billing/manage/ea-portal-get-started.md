---
title: Introdução ao Azure Enterprise Portal
description: Este artigo explica como os clientes do Azure EA (Contrato Enterprise do Azure) usam o Azure Enterprise Portal.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 8997cf5e86eb79b527c0b37ef0b85556469aea98
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687630"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Introdução ao Azure Enterprise Portal

Este artigo ajuda os clientes diretos e indiretos do Azure EA (Contrato Enterprise do Azure) a começar a usar o [Azure Enterprise Portal](https://ea.azure.com). Obtenha informações básicas sobre:

- A estrutura do Azure Enterprise Portal.
- Funções usadas no Azure Enterprise Portal.
- Criação de assinatura.
- Análise de custos no Azure Enterprise Portal e no portal do Azure.

Veja este vídeo para assistir a uma sessão de integração completa do Azure Enterprise Portal:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Hierarquia do Azure Enterprise Portal

A hierarquia do Azure Enterprise Portal consiste em:

- **Azure Enterprise Portal** – um portal de gerenciamento online que ajuda você a gerenciar os custos dos serviços de EA do Azure. Você pode:

  - Criar uma hierarquia de EA do Azure com departamentos, contas e assinaturas.
  - Reconciliar os custos de seus serviços consumidos, baixar relatórios de uso e exibir listas de preços.
  - Criar chaves de API para seu registro.

- Os **departamentos** ajudam a segmentar os custos em agrupamentos lógicos. Os departamentos permitem que você defina um orçamento ou uma cota no nível do departamento.

- As **contas** são unidades organizacionais no Azure Enterprise Portal. Você pode usar as contas para gerenciar assinaturas e acessar relatórios.

- As **assinaturas** são a menor unidade no Azure Enterprise Portal. Elas são contêineres para os serviços do Azure gerenciados pelo administrador de serviços.

O diagrama a seguir ilustra as hierarquias simples do EA do Azure.

![Diagrama de hierarquias simples do EA do Azure](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Funções de usuário corporativo

As seguintes funções de usuário administrativas fazem parte do seu Registro Enterprise:

- Administrador corporativo
- Administrador de departamento
- Proprietário da conta
- Administrador de serviço
- Contato de notificação

As funções funcionam em dois portais diferentes para concluir as tarefas. Você usa o [Azure Enterprise Portal](https://ea.azure.com) para gerenciar a cobrança e os custos e o [portal do Azure](https://portal.azure.com) para gerenciar serviços do Azure.

As funções de usuário são associadas a uma conta de usuário. Para validar a autenticidade do usuário, cada usuário deve ter uma conta Microsoft, corporativa ou de estudante válida. Verifique se cada conta está associada a um endereço de email monitorado ativamente. As notificações de conta são enviadas para o endereço de email.

Ao configurar usuários, você pode atribuir várias contas à função Administrador corporativo. No entanto, apenas uma conta pode conter a função de proprietário da conta. Além disso, você pode atribuir as funções administrador corporativo e proprietário da conta a uma conta.

### <a name="enterprise-administrator"></a>Administrador corporativo

Os usuários com essa função têm o nível mais alto de acesso. Eles podem:

- Gerenciar contas e proprietários da conta.
- Gerenciar outros administradores corporativos.
- Gerenciar administradores de departamento.
- Gerenciar contatos de notificação.
- Exibir o uso em todas as contas.
- Exibir encargos não cobrados em todas as contas.

Você pode ter vários administradores corporativos em um Registro Enterprise. Você pode permitir acesso somente leitura a administradores corporativos. Todos eles herdam a função de administrador do departamento.

### <a name="department-administrator"></a>Administrador de departamento

Os usuários com essa função podem:

- Criar e gerenciar departamentos.
- Criar proprietários da conta.
- Exibir detalhes de uso dos departamentos que eles gerenciam.
- Exibir custos, se tiverem as permissões necessárias.

Você pode ter vários administradores de departamento para cada Registro Enterprise.

Você pode conceder acesso somente leitura aos administradores de departamento ao editar ou criar um administrador de departamento. Defina a opção somente leitura como **Sim**.

### <a name="account-owner"></a>Proprietário da conta

Os usuários com essa função podem:

- Criar e gerenciar assinaturas.
- Gerenciar administradores de serviços.
- Exibir o uso de assinaturas.

Cada conta requer uma conta Microsoft, corporativa ou de estudante, exclusiva. Para obter mais informações sobre as funções administrativas do Azure Enterprise Portal, confira [Entendendo as funções administrativas do Contrato Enterprise do Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrador de serviço

A função administrador de serviços tem permissões para gerenciar serviços no portal do Azure e atribuir usuários à função de coadministrador.

### <a name="notification-contact"></a>Contato de notificação

O contato de notificação pode receber notificações de uso relacionadas ao registro.

## <a name="activate-your-enrollment"></a>Ative seu registro

Para ativar o serviço, o administrador corporativo inicial abre o [Azure Enterprise Portal](https://ea.azure.com) e entra usando o endereço de email do email de convite.

Se você for configurado como o administrador corporativo, não precisará receber o email de ativação. Acesse o [Azure Enterprise Portal](https://ea.azure.com) e entre com o endereço de email e a senha da conta Microsoft, corporativa ou de estudante.

Se você tiver mais de um registro, escolha um para ativar. Por padrão, somente os registros ativos são mostrados. Para exibir o histórico de registros, desmarque a opção **Ativo** no canto superior direito do Azure Enterprise Portal.

Em **Registro**, o status mostra **Ativo**.

![Exemplo mostrando um registro ativo](./media/ea-portal-get-started/ea-enrollment-status.png)

Somente administradores corporativos do Azure existentes podem criar outros administradores corporativos.

### <a name="create-another-enterprise-administrator"></a>Criar outro administrador corporativo

Para adicionar outro administrador corporativo:

1. Entre no [Azure Enterprise Portal](https://ea.azure.com).
1. Acesse **Gerenciar** > **Detalhes do Registro**.
1. Selecione **+ Adicionar Administrador** na parte superior à direita.

Verifique se você tem o endereço de email do usuário e o método de autenticação preferencial, por exemplo, uma conta Microsoft, corporativa ou de estudante.

Se você não for o administrador corporativo, entre em contato com um administrador corporativo para solicitar que ele o adicione a um registro. Depois que for adicionado a um registro, você receberá um email de ativação.

Se o administrador corporativo não puder ajudá-lo, crie uma [solicitação de suporte no Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Forneça as seguintes informações:

- Número de registro
- Endereço de email para adicionar e tipo de autenticação (conta Microsoft, corporativa ou de estudante)
- Aprovação de email de um administrador corporativo existente
  - Se o administrador corporativo existente não estiver disponível, entre em contato com seu parceiro ou consultor de software para solicitar que ele altere os detalhes de contato por meio da ferramenta VLSC (Centro de Serviços de Licenciamento por Volume da Microsoft).

Para obter mais informações sobre as funções administrativas empresariais, confira [Entendendo as funções administrativas do Contrato Enterprise no Azure](understand-ea-roles.md).

## <a name="create-an-azure-enterprise-department"></a>Criar um departamento do Azure Enterprise

Administradores corporativos e administradores de departamento usam departamentos para organizar e relatar os serviços e o uso do Azure Enterprise por departamento e centro de custo. O administrador corporativo é capaz de:

- Adicionar ou remover departamentos.
- Associar uma conta a um departamento.
- Criar administradores de departamento.
- Permitir que administradores de departamento exibam preços e custos.

Um administrador de departamento pode adicionar novas contas aos próprios departamentos. Pode também remover contas desses departamentos, mas não do registro.

Para adicionar um departamento:

1. Entre no Azure Enterprise Portal.
1. No painel esquerdo, selecione **Gerenciar**.
1. Selecione a guia **Departamento** e, em seguida, selecione **+ Adicionar Departamento**.
1. Insira as informações.
   O nome do departamento é o único campo obrigatório. Ele precisa ter, pelo menos, três caracteres.
1. Quando concluir, selecione **Adicionar**.

## <a name="add-a-department-administrator"></a>Adicionar um administrador de departamento

Depois que um departamento é criado, o administrador corporativo pode adicionar administradores de departamento e associar cada um deles a um departamento. Os administradores de departamento podem executar as seguintes ações para seus departamentos:

- Criar outros administradores de departamento
- Exibir e editar propriedades do departamento, tais como o nome ou o centro de custo
- Adicionar contas
- Remover contas
- Baixar detalhes de uso
- Exibir o uso mensal e os encargos <sup>1</sup>

> <sup>1</sup> Um administrador corporativo deve conceder essas permissões. Se você tiver recebido permissão para exibir o uso mensal e os encargos do departamento mas não puder vê-los, entre em contato com seu parceiro.

### <a name="to-add-a-department-administrator"></a>Para adicionar um administrador de departamento

Como um administrador corporativo:

1. Entre no Azure Enterprise Portal.
1. No painel esquerdo, selecione **Gerenciar**.
1. Selecione a guia **Departamento** e, em seguida, selecione o departamento.
1. Clique em **+ Adicionar Administrador** e adicione as informações necessárias.
1. Para acesso somente leitura, defina a opção **Somente Leitura** como **Sim** e, em seguida, selecione **Adicionar**.

![Exemplo mostrando a caixa de diálogo Adicionar Administrador do Departamento](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Para definir o acesso somente leitura

Você pode permitir acesso somente leitura aos administradores de departamento.

- Ao criar um administrador de departamento, defina a opção somente leitura como **Sim**.

- Para editar um administrador do departamento existente:
   1. Selecione um departamento e, em seguida, selecione o símbolo de lápis ao lado do **Administrador do Departamento** que você deseja editar.
   1. Defina a opção somente leitura como **Sim** e, em seguida, selecione **Salvar**.

Os administradores corporativos obtêm permissões de administrador do departamento automaticamente.

## <a name="add-an-account"></a>Adicionar uma conta

A estrutura das contas e das assinaturas afeta o modo como elas são administradas e como elas aparecem em suas faturas e relatórios. Exemplos de estruturas organizacionais típicas incluem divisões de negócios, equipes funcionais e localizações geográficas.

Para adicionar uma conta:

1. No Azure Enterprise Portal, selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione a guia **Conta**. Na página **Conta**, selecione **+Adicionar Conta**.
1. Selecione um departamento ou deixe-o como não atribuído e, em seguida, selecione o tipo de autenticação desejado.
1. Digite um nome amigável para identificar a conta no relatório.
1. Insira o endereço de **Email do Proprietário da Conta** para associar à nova conta.
1. Confirme o endereço de email e, em seguida, selecione **Adicionar**.

![Exemplo mostrando a lista de contas e a opção + Adicionar Conta](./media/ea-portal-get-started/create-ea-add-an-account.png)

Para adicionar outra conta, selecione **Adicionar Outra Conta** ou **Adicionar** no canto inferior direito da barra de ferramentas à esquerda.

Para confirmar a propriedade da conta:

1. Entre no Azure Enterprise Portal.
1. Exiba o status.

   O status deve mudar de **Pendente** para **Data de Início/Término**. A Data de Início/Término é a data em que o usuário se conectou pela primeira vez e a data de término do contrato.
1. Quando a mensagem de **Aviso** aparecer, o proprietário de conta precisará selecionar **Continuar** para ativar a conta na primeira vez que entrar no Azure Enterprise Portal.

## <a name="change-account-owner"></a>Alterar proprietário da conta

Os administradores corporativos podem usar o Azure Enterprise Portal para transferir a propriedade da conta de assinatura em um registro. A ação move todas as assinaturas de uma conta de usuário de origem para uma conta de usuário de destino.

Esteja atento a essas informações importantes ao transferir contas:

- Você pode fazer essas transferências:
  - De uma conta corporativa ou de estudante para outra conta corporativa ou de estudante.
  - De uma conta Microsoft para uma conta corporativa ou de estudante.
  - De uma conta Microsoft para outra conta Microsoft.

    A conta de destino deve ser uma conta válida do Azure Commerce para ser um destino válido para transferências. Para novas contas, você será solicitado a criar uma conta do Azure Commerce ao entrar no Azure Enterprise Portal. Para contas existentes, você deve primeiro criar uma nova assinatura do Azure antes que a conta seja considerada qualificada.

- Você não pode fazer uma transferência de uma conta corporativa ou de estudante para uma conta Microsoft.

- Quando você conclui uma transferência de assinatura, a Microsoft atualiza o proprietário da conta.

Entenda estas políticas de RBAC (controle de acesso baseado em função):

- Quando você executa transferências de assinatura entre duas IDs organizacionais no mesmo locatário, as políticas de RBAC e as funções de administrador de serviços e de coadministrador existentes são preservadas.
- Outras transferências de assinatura resultam na perda de suas políticas de RBAC e das atribuições de função.
- Políticas e funções de administrador não são transferidas entre diretórios diferentes. Os administradores de serviços são atualizados para o proprietário da conta de destino.

Antes de alterar um proprietário da conta:

1. No Azure Enterprise Portal exiba a guia **Conta** e identifique a conta de origem. A conta de origem precisa estar ativa.
1. Identifique a conta de destino e verifique se ela está ativa.

Para transferir a propriedade da conta para todas as assinaturas:

1. Entre no Azure Enterprise Portal.
1. Na área de navegação esquerda, selecione **Gerenciar**.
1. Selecione a guia **Conta** e passe o mouse sobre uma conta.
1. Selecione o ícone de mudar o proprietário da conta à direita. O ícone se assemelha a uma pessoa.
1. Escolha uma conta qualificada e, em seguida, selecione **Avançar**.
1. Confirme a transferência e selecione **Enviar**.

![Imagem mostrando o símbolo Alterar Proprietário da Conta](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Para transferir a propriedade da conta para uma assinatura única:

1. Entre no Azure Enterprise Portal.
1. Na área de navegação esquerda, selecione **Gerenciar**.
1. Selecione a guia **Conta** e passe o mouse sobre uma conta.
1. Selecione o ícone de transferir assinaturas à direita. O ícone se assemelha a uma página.
1. Escolha uma assinatura qualificada e selecione **Avançar**.
1. Confirme a transferência e, em seguida, selecione **Enviar**.

![Imagem mostrando o símbolo Transferir Assinaturas](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Assista a este vídeo para ver o gerenciamento de usuários do Azure Enterprise Portal:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Criar uma assinatura

Os proprietários da conta podem exibir e gerenciar assinaturas. Você pode usar assinaturas para dar acesso a ambientes e projetos de desenvolvimento às equipes de sua organização. Por exemplo, teste, produção, desenvolvimento e preparo.

Ao criar assinaturas diferentes para cada ambiente de aplicativo, você ajuda a proteger cada ambiente.

- Você também pode atribuir uma conta de administrador de serviços diferente para cada assinatura.
- Você pode associar assinaturas a qualquer número de serviços.
- O proprietário da conta cria assinaturas e atribui uma conta de administrador de serviços a cada assinatura na conta dele.

### <a name="add-a-subscription"></a>Adicionar uma assinatura

Use as informações a seguir para adicionar uma assinatura.

Na primeira vez que você adicionar uma assinatura à conta, será solicitado que você aceite o MOSA (Contrato de Assinatura do Microsoft Online) e um plano de taxas. Embora eles não sejam aplicáveis a clientes do Contrato Enterprise, o MOSA e o plano de taxas são necessários para criar sua assinatura. O aditamento de registro do Contrato Enterprise do Microsoft Azure substitui os itens acima e a sua relação contratual não é alterada. Quando solicitado, selecione a caixa que indica que você aceita os termos.

_Microsoft Azure Enterprise_ é o nome padrão quando uma assinatura é criada. Você pode alterar o nome para diferenciá-lo das outras assinaturas em seu registro e garantir que seja reconhecido em relatórios no nível corporativo.

Para adicionar uma assinatura:

1. No Azure Enterprise Portal, entre na conta.
1. Selecione a guia **Admin** e, em seguida, selecione **Assinatura** na parte superior da página.
1. Verifique se você está conectado como o proprietário da conta em questão.
1. Selecione **+ Adicionar Assinatura** e, em seguida, selecione **Comprar**.

   Na primeira vez que você adicionar uma assinatura a uma conta, deverá fornecer suas informações de contato. Ao adicionar assinaturas adicionais, suas informações de contato serão adicionadas para você.

1. Selecione **Assinaturas** e, em seguida, selecione a assinatura que você criou.
1. Selecione **Editar Detalhes da Assinatura**.
1. Edite o **Nome da Assinatura** e o **Administrador de Serviços** e, em seguida, selecione a marca de seleção.

   O nome da assinatura aparece nos relatórios. É o nome do projeto associado à assinatura no portal de desenvolvimento.

Novas assinaturas podem levar até 24 horas para aparecer na lista de assinaturas. Depois de ter criado uma assinatura, você pode:

- [Editar detalhes da assinatura](https://account.azure.com/Subscriptions)
- [Gerenciar chaves de assinatura](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Transferir uma assinatura do Enterprise para uma assinatura Pagamento Conforme o Uso

Para transferir uma assinatura Enterprise para uma assinatura individual com as tarifas de Pagamento Conforme o Uso, você precisa criar uma solicitação de suporte no Azure Enterprise Portal. Para criar uma solicitação de suporte, selecione **+ Nova solicitação de suporte** na área **Ajuda e Suporte**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associar uma conta existente à sua assinatura de Pagamento Conforme o Uso

Se já tiver uma conta do Microsoft Azure existente no portal do Azure, insira a conta Microsoft, corporativa ou de estudante pertinente para associá-la ao seu registro do Contrato Enterprise.

### <a name="associate-an-existing-account"></a>Associar uma conta existente

1. No Azure Enterprise Portal, selecione **Gerenciar**.
1. Selecione a guia **Conta**.
1. Selecione **+Adicionar uma conta**.
1. Insira a conta corporativa ou de estudante ou a conta Microsoft associada à conta do Azure existente.
1. Confirme a conta associada à conta do Azure existente.
1. Forneça um nome que você gostaria de usar para identificar esta conta nos relatórios.
1. Selecione **Adicionar**.
1. Para adicionar outra conta, selecione novamente a opção **+Adicionar uma conta** ou retorne à página inicial escolhendo o botão **Admin**.
1. Se você exibir a página **Conta**, a conta recém-adicionada será exibida com o status **Pendente**.

### <a name="confirm-account-ownership"></a>Confirmar a propriedade da conta

1. Entre na conta de email associada à conta corporativa ou de estudante ou à conta Microsoft que você forneceu.
1. Abra a notificação de email intitulada _"Convite para ativar a conta no serviço do Microsoft Azure de licenciamento por volume da Microsoft"_ .
1. No convite, selecione o link **Entrar no Microsoft Azure Enterprise Portal**.
1. Selecione **Entrar**.
1. Insira a conta Microsoft, corporativa ou de estudante e a senha para entrar e confirmar a propriedade da conta.

### <a name="azure-marketplace"></a>Azure Marketplace

Embora a maioria das assinaturas possa converter do ambiente de Pagamento Conforme o Uso para o Contrato Enterprise do Azure, os serviços do Azure Marketplace não fazem isso. Para ter um modo de exibição único de todas as assinaturas e preços, recomendamos adicionar os serviços do Azure Marketplace ao Azure Enterprise Portal.

1. Entre no Azure Enterprise Portal.
1. Selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione a **EnrollmentTab**.
1. Exiba a seção **Detalhes do Registro**.
1. À direita do campo Azure Marketplace, selecione o ícone de lápis para habilitar. Clique em **Salvar**.

O proprietário da conta agora pode comprar serviços do Azure Marketplace que antes pertenciam à assinatura de Pagamento Conforme o Uso.

Depois que as novas assinaturas do Azure Marketplace forem ativadas no registro do EA do Azure, cancele os serviços do Azure Marketplace que foram criados no ambiente de Pagamento Conforme o Uso. Esta etapa é crítica para que suas assinaturas do Azure Marketplace não entrem em um estado inadequado quando seu meio de pagamento do Pagamento Conforme o Uso expirar.

### <a name="msdn"></a>MSDN

As assinaturas da MSDN são automaticamente convertidas em Desenvolvimento/Teste da MSDN e a oferta do EA do Azure perderá os créditos monetários existentes.

### <a name="azure-in-open"></a>Azure via Open

Se você associar uma assinatura do Azure via Open com um Contrato Enterprise, perderá os créditos não consumidos do Azure via Open. Portanto, recomendamos que você consuma todo o crédito da assinatura Azure via Open antes de adicionar a conta ao seu Contrato Enterprise.  

### <a name="accounts-with-support-subscriptions"></a>Contas com assinaturas de suporte

Se o Contrato Enterprise não tiver uma assinatura de suporte e você adicionar uma conta existente com uma assinatura de suporte no Azure Enterprise Portal, sua assinatura de suporte do MOSA não será transferida automaticamente. Você precisará comprar novamente uma assinatura de suporte no EA do Azure durante o período de carência: até o final do mês subsequente.

## <a name="view-usage-summary-and-download-reports"></a>Exibir resumo de uso e baixar relatórios

Os administradores corporativos podem exibir um resumo dos dados de uso, do pagamento antecipado do Azure consumido e das cobranças associadas ao uso adicional no Azure Enterprise Portal. As cobranças são apresentadas no nível do resumo em todas as contas e assinaturas.

Para exibir o uso detalhado de contas específicas, baixe o relatório de detalhes de uso:

1. Entre no Azure Enterprise Portal.
1. Selecione **Relatórios**.
1. Selecione a guia **Baixar Uso**.
1. Na lista de relatórios, selecione **Baixar** para o relatório mensal que deseja obter.

   > [!NOTE]
   > O relatório de detalhe de uso não inclui as tributos incidentes.
   >
   > Pode haver uma latência de até oito horas do momento em que o uso foi feito até ser refletido no relatório.

Para exibir os relatórios e os grafos de resumo de uso:

1. Entre no Azure Enterprise Portal.

1. Selecione uma condição de Pagamento antecipado.

   Para alterar o intervalo de datas do **Resumo de Uso**, você pode alternar de **M** (mensal) para **C** (personalizado) no canto superior direito da página e, em seguida, inserir as datas de início e término personalizadas.

   ![Criar e exibir o resumo de uso e baixar relatórios no modo de exibição personalizado](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Para exibir detalhes adicionais, selecione um período ou mês no gráfico.

   - O gráfico mostra o uso mês a mês com um detalhamento do uso utilizado, os encargos adicionais de serviço, os encargos cobrados separadamente e os encargos do Azure Marketplace.
   - Para o mês selecionado, use os campos abaixo do gráfico para filtrar por departamentos, contas e assinaturas.
   - Você pode alternar entre **Cobrar por Serviços** e **Cobrar por Hierarquia**.
   - Veja os detalhes de **Serviço do Azure**, dos **Encargos Cobrados Separadamente** e do **Azure Marketplace** expandindo as seções pertinentes.

Assista a este vídeo para ver como exibir o uso:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Baixar relatórios CSV

Os administradores corporativos usam a página de Download de Relatório Mensal para baixar vários relatórios como arquivos CSV:

- Saldo e preço
- Detalhes de uso
- Encargos do Azure Marketplace
- Tabela de preços

Para baixar relatórios:

1. No Azure Enterprise Portal, selecione **Relatórios**.
2. Selecione **Baixar Uso** na parte superior da página.
3. Selecione **Baixar** ao lado do relatório do mês.

   > [!NOTE]
   > Pode haver uma latência de até cinco dias entre a data em que o uso ocorreu e o momento em que ele é mostrado nos relatórios.
   >
   > Os usuários que baixarem arquivos CSV com o Safari para Excel poderão encontrar erros de formatação. Para evitar erros, abra o arquivo usando um editor de texto.

![Exemplo mostrando a página Baixar Uso](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Assista a este vídeo para ver como baixar informações de uso:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Download do relatório avançado

Você pode usar o download de relatório avançado para obter relatórios que abrangem intervalos de datas ou contas específicas. O arquivo de saída está no formato CSV para acomodar grandes conjuntos de registros.

1. No Azure Enterprise Portal, selecione **Download de Relatório Avançado**.
1. Selecione um intervalo de datas e as contas apropriadas.
1. Escolha **Solicitar dados de uso**.
1. Selecione o botão **Atualizar** até que o status do relatório seja atualizado para **Baixar**.
1. Baixe o relatório.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Baixar relatórios de uso e informações de cobrança de um registro anterior

Você pode baixar relatórios de uso e informações de cobrança para um registro anterior depois que uma transferência de registro tiver sido feita. Os relatórios históricos estão disponíveis no Azure Enterprise Portal e no gerenciamento de custos.

O Azure Enterprise Portal filtra os registros inativos, deixando-os fora da exibição. Você precisará desmarcar a caixa de **Ativo** para exibir registros transferidos inativos.  

![Desmarcar a caixa Ativo permite que o usuário veja registros inativos](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Glossário de termos do EA do Azure

- **Conta**: uma unidade organizacional no Azure Enterprise Portal. Ela é usada para administrar assinaturas e para relatórios.
- **Proprietário da conta**: a pessoa que gerencia assinaturas e administradores de serviço no Azure. Ela pode exibir dados de uso nessa conta e suas assinaturas associadas.
- **Assinatura de aditamento**: uma assinatura coincidente ou de um ano sob o aditamento do registro.
- **Pagamento antecipado**: Pagamento antecipado de um valor monetário anual de serviços do Azure a uma tarifa de Pagamento antecipado reduzida para uso em relação a esse pagamento antecipado.
- **Administrador de departamento**: a pessoa que gerencia departamentos, cria contas e proprietários de contas, exibe detalhes de uso dos departamentos que gerencia e pode exibir custos quando permissões forem concedidas.
- **Número de registro**: um identificador exclusivo fornecido pela Microsoft para identificar o registro específico associado a um Contrato Enterprise.
- **Administrador corporativo**: a pessoa que gerencia departamentos, proprietários de departamentos, contas e proprietários de contas no Azure. Ela tem a capacidade de gerenciar administradores corporativos e exibir dados de uso, quantidades cobradas e encargos não cobrados em todas as contas e assinaturas associadas ao registro corporativo.
- **Contrato Enterprise**: um contrato de licenciamento da Microsoft para clientes com compras centralizadas que desejam padronizar toda a organização com a tecnologia da Microsoft e manter uma infraestrutura de tecnologia da informação com um padrão de software da Microsoft.
- **Registro do contrato Enterprise**: um registro no programa de Contrato Enterprise que fornece produtos da Microsoft em volume com tarifas reduzidas.
- **Conta Microsoft**: um serviço online que permite que sites participantes autentiquem um usuário com um conjunto de credenciais.
- **Aditamento do Registro do Microsoft Azure Enterprise (aditamento do registro)** : um aditamento assinado por uma empresa que fornece acesso ao Azure como parte de seu Registro Enterprise.
- **Azure Enterprise Portal**: o portal usado por nossos clientes empresariais para gerenciar suas contas do Azure e as assinaturas relacionadas.
- **Quantidade de recursos consumidos**: a quantidade de um serviço individual do Azure utilizado em um mês.
- **Administrador de serviços**: a pessoa que acessa e gerencia assinaturas e projetos de desenvolvimento no Azure Enterprise Portal.
- **Assinatura**: representa uma assinatura do Azure Enterprise Portal e é um contêiner de serviços do Azure gerenciados pelo mesmo administrador de serviços.
- **Conta corporativa ou de estudante**: para organizações que configuraram o Active Directory com federação para a nuvem e todas as contas estão em um locatário.

### <a name="enrollment-statuses"></a>Status de registro

- **Novo**: Esse status é atribuído a um registro criado dentro de 24 horas e será atualizado para um status pendente dentro de 24 horas.
- **Pendente**: o administrador do registro precisa entrar no Azure Enterprise Portal. Depois de conectado, o registro mudará para o status Ativo.
- **Ativa**: o registro está Ativo e as contas e assinaturas podem ser criadas no Azure Enterprise Portal. O registro permanecerá ativo até a data de término do Contrato Enterprise.
- **Termo estendido indefinido**: o status de termo estendido indefinido ocorre após a data de término do Contrato Enterprise. Ele permite que os clientes do EA do Azure que optaram pelo período estendido continuem a usar os serviços do Azure indefinidamente no final do Contrato Enterprise.

   Antes que o registro do EA do Azure atinja a data de término do Contrato Enterprise, o administrador do registro deve decidir dentre as seguintes opções:

  - Renovar o registro adicionando outro Pagamento antecipado do Azure.
  - Transferir para um novo registro.
  - Migrar para o MOSP (Programa de Assinatura do Microsoft Online).
  - Confirmar a desabilitação de todos os serviços associados ao registro.
- **Expirado**: o cliente do EA do Azure é recusado do período estendido e o registro de EA do Azure atingiu a data de término do Contrato Enterprise. O registro expirará e todos os serviços associados serão desabilitados.
- **Transferidos**: registros em que todos os serviços e contas associados foram transferidos para um novo registro são exibidos com o status Transferidos.
  >[!NOTE]
  > Os registros não são transferidos automaticamente se um novo número de registro é gerado na renovação. Você deve incluir o número de registro anterior na sua documentação de renovação para facilitar uma transferência automática.

## <a name="get-started-on-azure-ea---faq"></a>Introdução às perguntas frequentes do EA do Azure

Esta seção fornece detalhes sobre as perguntas típicas feitas pelos clientes durante o processo de integração.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Posso associar minha conta existente do Azure ao registro do EA do Azure?

Sim. Todas as assinaturas do Azure das quais você é o proprietário da conta serão convertidas para o Contrato Enterprise. Estão incluídas assinaturas que usam crédito mensal, como Visual Studio, AzurePass, MPN, BizSpark e muito mais. Você perderá o crédito mensal ao converter essas assinaturas.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Associei acidentalmente minha conta do Azure existente ao registro EA do Azure. Como resultado, perdi meu crédito mensal. Posso ter meu crédito mensal de volta?

Se você entrar como um proprietário da conta do EA do Azure com as mesmas credenciais da sua Assinatura do Visual Studio, poderá recuperar seu benefício individual do Azure da Assinatura do Visual Studio executando uma das seguintes ações:

- Exclua o proprietário da conta do Azure Enterprise Portal, depois de remover ou mover as assinaturas do Azure associadas. Em seguida, inscreva-se novamente para os benefícios individuais do Azure para o Visual Studio.
- Exclua o assinante do Visual Studio do site de administração no VLSC e atribua novamente a assinatura a uma conta com credenciais diferentes desta vez. Em seguida, inscreva-se novamente para os benefícios individuais do Azure para o Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Que tipo de assinatura devo criar?

O Azure Enterprise Portal oferece dois tipos de assinaturas para clientes empresariais:

- Microsoft Azure Enterprise, que é ideal para:
  - Utilização em qualquer tipo de produção
  - Melhores preços com base no gasto da infraestrutura

  Para obter mais informações, [entre em contato com o departamento de vendas do Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Desenvolvimento/Teste Enterprise, que é ideal para:
  - Todas as cargas de trabalho de desenvolvimento/teste da equipe
  - Cargas de trabalho de desenvolvimento/teste individual, de média a pesada
  - Acesso a imagens especiais do MSDN e taxas de serviço preferenciais

  Para obter mais informações, confira [oferta Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>É possível transferir a propriedade de assinatura para outra conta?

Sim, você pode transferir a propriedade de assinatura para uma conta diferente. Por exemplo, se uma Conta A tiver três assinaturas, o administrador corporativo poderá transferir uma assinatura para a Conta B, uma para a Conta C e outra para a Conta D ou pode transferir todas as assinaturas para a Conta E.

Para transferir assinaturas:

1. No Azure Enterprise Portal, selecione **Gerenciar** > **Conta**.
1. Passe o mouse sobre **Conta** na extrema direita para ver as opções **Transferir Propriedade** (ícone de pessoa) e **Transferir Assinatura** (ícone de lista). Essas opções só ficam visíveis para contas ativas.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>O nome da minha assinatura é o mesmo que o nome da oferta. Devo alterar o nome da assinatura para algo significativo para minha organização?

Quando você cria uma assinatura, o nome usa como padrão o tipo de oferta que você escolhe. Recomendamos alterar o nome da assinatura para algo que facilite o rastreamento da assinatura.

Para alterar o nome:

1. Entre em [https://account.windowsazure.com](https://account.windowsazure.com).
1. Selecione a lista de assinaturas.
1. Selecione a assinatura que deseja editar.
1. Selecione o ícone **Gerenciar Assinatura**.
1. Edite os detalhes da assinatura.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Como faço para acompanhar os custos incorridos por um centro de custo?

Para acompanhar o custo por centro de custo, você precisa definir o centro de custo em um dos seguintes níveis:

- department
- Conta
- Subscription

Dependendo daquilo que precisa, você poderá usar o mesmo centro de custo para acompanhar o uso e os custos associados a um centro de custo específico.

Por exemplo, para acompanhar os custos de um projeto especial em que vários departamentos estão envolvidos, pode ser interessante definir o centro de custo no nível de uma assinatura para controlar o uso e os custos.

Você não pode definir um centro de custo no nível de serviço. Caso você queira controlar o uso no nível de serviço, você pode usar o recurso _Marca_ disponível no nível de serviço.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Como faço para acompanhar o uso e o gasto pelos diversos departamentos da organização?

Você pode criar quantos departamentos forem necessários em seu registro do EA do Azure. Para acompanhar o uso corretamente, não compartilhe assinaturas entre departamentos.

Depois de criar departamentos e assinaturas, você poderá ver os dados no relatório de uso. Essas informações podem ajudá-lo a acompanhar o uso e gerenciar custos e gastos no nível do departamento.

Você também pode acessar os dados de uso por meio da API de relatórios. Para obter informações detalhadas e o código de exemplo, confira as [APIs REST do Azure Enterprise](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Posso definir uma cota de gastos e receber alertas à medida que me aproximar do meu limite?

Você pode definir uma cota de gastos no nível do departamento e o sistema o notificará automaticamente quando seus limites de gastos atingirem 50%, 75%, 90% e 100% da cota definida.

Para definir sua cota de gastos, selecione um departamento e, em seguida, selecione o ícone Editar. Depois de editar os detalhes do limite de gastos, selecione **Salvar**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Usei grupos de recursos para implementar o RBAC e acompanhar o uso. Como faço para exibir os detalhes de uso associados?

Se você usar _grupos de recursos_ e _marcas_, essas informações serão rastreadas no nível de serviço e você poderá acessá-las no arquivo detalhado de uso (CSV) para download. Confira a opção [baixar o relatório de uso](https://ea.azure.com/report/downloadusage) no Azure Enterprise Portal.

Você também pode acessar o uso por meio da API. Para obter informações detalhadas e o código de exemplo, confira as [APIs REST do Azure Enterprise](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

> [!NOTE]
> Você só pode aplicar marcas em recursos com suporte a operações do Azure Resource Manager. Se você tiver criado uma máquina virtual, uma rede virtual ou um armazenamento por meio do modelo de implantação clássica (como por meio do portal clássico), não poderá aplicar uma marcação a esse recurso. Para dar suporte à marcação, implante esses recursos novamente por meio do Resource Manager. Todos os outros recursos oferecem suporte à marcação.

### <a name="can-i-perform-analyses-using-power-bi"></a>Posso executar análises usando o Power BI?

Sim. Com o pacote de conteúdo do Microsoft Azure Enterprise para Power BI, você pode:

- Importar e analisar rapidamente o consumo do Azure para seu Registro Enterprise.
- Descobrir qual departamento, conta ou assinatura consumiu a maior parte do uso.
- Saber qual serviço sua organização usou mais.
- Acompanhar as tendências de gastos e uso.

Para usar o Power BI:

1. Acesse o site do Power BI.
1. Entre com uma conta corporativa ou de estudante válida.

   A conta corporativa ou de estudante pode ser a mesma ou uma diferente da usada para acessar o registro pelo Azure Enterprise Portal.
1. No painel de serviços, selecione o bloco Microsoft Azure Enterprise e selecione **Conectar**.
1. Na tela **Conectar-se ao Azure Enterprise**, insira:
    - URL do ambiente do Azure: [https://ea.azure.com](https://ea.azure.com)
    - Número de meses: entre 1 e 36
    - Número de registro: o número de seu registro
1. Selecione **Avançar**.
1. Insira a Chave de API na caixa da **Chave de Conta**.

   Você pode encontrar a chave de API no Azure Enterprise Portal. Procure na guia **Baixar Uso** e, em seguida, selecione **Chave de acesso da API**. Copie e cole a chave na caixa **Chave de Conta** no Power BI.

Dependendo do tamanho do conjunto de dados, pode levar entre cinco e 30 minutos para que os dados sejam carregados no Power BI.

O relatório do Power BI está disponível para clientes diretos, indiretos e parceiros do EA do Azure que podem exibir informações de cobrança.

## <a name="next-steps"></a>Próximas etapas

- Os administradores do Azure Enterprise Portal devem ler [Administração do Azure Enterprise Portal](ea-portal-administration.md) para aprender sobre tarefas administrativas comuns.
- Se você precisar de ajuda com a solução de problemas do Azure Enterprise Portal, confira [Solucionar problemas de acesso ao Azure Enterprise Portal](ea-portal-troubleshoot.md).
- Para obter um guia de integração do EA do Azure, confira [Guia de integração do EA do Azure (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
