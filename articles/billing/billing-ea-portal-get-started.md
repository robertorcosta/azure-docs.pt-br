---
title: Introdução ao Portal do EA do Azure
description: Este artigo explica como os clientes de EA do Azure usam o Portal do EA do Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/07/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: d5fe93f1d3297fbdfb879a515c502025b49eab1e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223986"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Introdução ao Portal do EA do Azure

Este artigo ajuda os clientes diretos e indiretos do EA do Azure a começar a usar o [Portal do EA do Azure](https://ea.azure.com) com informações básicas sobre:

- Como o Portal do EA do Azure é estruturado.
- Funções usadas no Portal do EA do Azure.
- Como começar a criar assinaturas.
- Análise dos custos no Portal do EA do Azure e no portal do Azure.

Veja um vídeo que mostra uma sessão de integração completa do Portal do EA do Azure:

[Vídeo de integração do Portal do EA do Azure](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Hierarquia do Portal do EA do Azure

A hierarquia do Portal do EA do Azure consiste em:

**Portal do EA do Microsoft Azure** – o Portal do EA do Azure é um portal de gerenciamento online que ajuda você a gerenciar custos para seus serviços de EA do Azure. Você o usa para criar uma hierarquia de EA do Azure, incluindo departamentos, contas e assinaturas. Você também pode usá-lo para reconciliar os custos de seus serviços consumidos, baixar relatórios de uso e exibir listas de preços. E você cria chaves de API para seu registro.

**Departamentos** – você cria departamentos para ajudar a segmentar os custos em agrupamentos lógicos e, em seguida, definir um orçamento ou uma cota no nível do departamento.

**Contas** – são unidades organizacionais no Portal do EA do Azure e são usadas para gerenciar assinaturas. Contas também usadas para relatórios.

**Assinaturas** – são a menor unidade no Portal do EA do Azure. Elas são contêineres para os serviços do Azure gerenciados pelo administrador de serviços.

O diagrama a seguir ilustra as hierarquias simples do EA do Azure.

![Diagrama de hierarquias simples do EA do Azure](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Funções de usuário corporativo

Para administrar os Serviços do Azure em seu registro, há quatro funções administrativas de usuário corporativo distintas:

- Administrador corporativo
- Administrador de departamento
- Proprietário da conta
- Administrador de serviço

As funções são usadas para concluir tarefas em dois portais do Microsoft Azure diferentes. O Portal do EA do Azure (https://ea.azure.com) é usado para ajudá-lo a gerenciar a cobrança e os custos. O portal do Azure (https://portal.azure.com) é usado para gerenciar os Serviços do Azure.

As funções de usuário são associadas a uma conta de usuário. Para validar a autenticidade do usuário, cada usuário deve ter uma conta Microsoft, corporativa ou de estudante válida. Verifique se cada conta está associada a um endereço de email monitorado ativamente. As notificações de conta são enviadas para o endereço de email.

Ao configurar usuários, você pode atribuir várias contas corporativas, de estudante ou da Microsoft à função de administrador corporativo. No entanto, você pode atribuir apenas uma conta Microsoft, corporativa ou de estudante à função de proprietário da conta. Além disso, uma única conta Microsoft, corporativa ou de estudante pode ter as funções de administrador corporativo e proprietário da conta aplicadas a ela.

### <a name="enterprise-administrator"></a>Administrador corporativo

A função de administrador corporativo tem o nível de acesso mais alto. Os usuários com essa função podem:

- Gerenciar contas e proprietários de conta
- Gerenciar outros administradores corporativos
- Gerenciar administradores de departamento
- Gerenciar contatos de notificação
- Exibir o uso em todas as contas
- Exibir encargos não cobrados em todas as contas

Você pode ter vários administradores corporativos em um Registro Enterprise. Você pode permitir acesso somente leitura a administradores corporativos. Todos eles herdam a função de administrador do departamento.

### <a name="department-administrator"></a>Administrador de departamento

Os usuários com essa função podem:

- Criar e gerenciar departamentos
- Criar novos proprietários de conta
- Exibir detalhes de uso dos departamentos que eles gerenciam
- Exibir custos, se as permissões necessárias forem concedidas

Você pode ter vários administradores de departamento para cada Registro Enterprise.

Você pode permitir acesso somente leitura aos administradores de departamento. Para permitir acesso somente leitura, edite ou crie um novo administrador de departamento e defina a opção somente leitura como **Sim**.

### <a name="account-owner"></a>Proprietário da conta

Os usuários com essa função podem:

- Criar e gerenciar assinaturas
- Gerenciar administradores de serviço
- Exibir o uso de assinaturas

Cada conta requer uma conta Microsoft, corporativa ou de estudante exclusiva. Para obter mais informações sobre as funções administrativas do Portal do EA do Azure, confira [Entendendo as funções administrativas do Contrato Enterprise no Azure](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Administrador de serviço

O administrador de serviços tem permissões para gerenciar serviços no portal do Azure e atribuir usuários à função de coadministrador.

## <a name="activate-your-enrollment"></a>Ative seu registro

Para ativar o serviço, o administrador corporativo inicial abre o Portal do EA do Azure em [https://ea.azure.com](https://ea.azure.com) e entra usando o endereço de email do email de convite.

Se você tiver mais de um registro, escolha um para ativar. Por padrão, somente os registros ativos são mostrados. Para exibir o histórico de registros, desmarque a opção **Ativo** no canto superior direito do Portal do EA do Azure.

Em Registro, o status mostra **Ativo**.

![Exemplo mostrando um registro ativo](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

Somente administradores corporativos do Azure existentes podem criar outros admins corporativos.

### <a name="create-another-enterprise-admin"></a>Criar outro admin corporativo

- Entre no [Portal do EA do Azure](https://ea.azure.com) e navegue até **Gerenciar** > **Detalhes de Registro** e clique em **+ Adicionar Administrador** no canto superior direito da página.

Verifique se você tem os endereços de email do usuário e o método de autenticação preferencial, por exemplo, com conta Microsoft, corporativa ou de estudante. Você precisa das informações para adicionar um usuário.

Se você não for o administrador do EA, entre em contato com um administrador do EA para solicitar que ele o adicione a um registro. Depois que for adicionado a um registro, você receberá um email de ativação.

Se o administrador do EA não puder ajudá-lo, crie uma [solicitação de suporte do Portal do EA do Azure](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Forneça as seguintes informações:

- Número de registro
- Endereço de email para adicionar e tipo de autenticação (conta Microsoft, corporativa ou de estudante)
- Aprovação de email de um administrador de EA existente
  - Se o administrador de EA existente não estiver disponível, entre em contato com seu parceiro ou consultor de software para solicitar que ele altere os detalhes de contato por meio da ferramenta VLSC.

Para obter mais informações sobre as funções administrativas empresariais, confira [Entendendo as funções administrativas do Contrato Enterprise no Azure](billing-understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Criar um departamento de EA do Azure

Administradores corporativos e administradores de departamento usam departamentos para organizar e relatar os serviços e o uso do Azure Enterprise por departamento e centro de custo. O administrador corporativo é capaz de:

- Adicionar ou remover departamentos
- Associar uma conta a um departamento
- Criar administradores de departamento
- Permitir que administradores de departamento exibam preços e custos

Um administrador de departamento pode adicionar novas contas aos próprios departamentos. Pode também remover contas desses departamentos, mas não do registro.

Para adicionar um departamento:

1. Na área de navegação à esquerda, clique em **Gerenciar**.
2. Clique na guia **Departamento**, clique em **+ Adicionar Departamento** e, em seguida, insira as informações necessárias.
3. O nome do departamento é o único campo obrigatório. Ele precisa ter, pelo menos, três caracteres.
4. Ao concluir, clique em **Adicionar**.

## <a name="add-a-department-admin"></a>Adicionar um administrador de departamento

Depois que um departamento é criado, o administrador corporativo do Azure pode adicionar administradores de departamento e associar cada um deles a um departamento. O administrador do departamento é capaz de:

- Criar outros administradores de departamento
- Exibir e editar propriedades do departamento, tais como o nome ou o centro de custo
- Adicionar uma conta aos próprios departamentos
- Remover uma conta dos próprios departamentos
- Baixar detalhes de uso dos próprios departamentos
- Exibir o uso mensal e os encargos do próprio departamento se um administrador corporativo tiver concedido a ele permissão para isso <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Para adicionar um administrador de departamento

Como um administrador corporativo:

1. Na área de navegação à esquerda, clique em **Gerenciar**.
2. Clique na guia **Departamento** e, em seguida, clique no departamento.
3. Clique em **+ Adicionar Administrador** e adicione as informações necessárias.
4. Para acesso somente leitura, defina a opção **Somente Leitura** como **Sim** e clique em **Adicionar**.

![Exemplo mostrando a caixa de diálogo Adicionar Administrador do Departamento](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Para definir o acesso somente leitura

Você pode permitir acesso somente leitura aos administradores de departamento. Ao criar um administrador do departamento:

- Defina a opção somente leitura como **Sim**.

Para editar um administrador do departamento existente:

1. Selecione um departamento e, em seguida, clique no símbolo de lápis ao lado do **Administrador do Departamento** que você deseja editar.
2. Defina a abertura somente leitura como **Sim**. Em seguida, clique em **Salvar**.

Os usuários com a função de administrador corporativo obtêm permissões de administrador do departamento automaticamente.

<sup>1</sup> Se você tiver recebido permissão para exibir o uso mensal e os encargos do departamento mas não puder vê-los, entre em contato com seu parceiro.

## <a name="add-an-account"></a>Adicionar uma conta

A estrutura da conta e da assinatura afeta o modo como elas são administradas e como elas aparecem em suas faturas e relatórios. Exemplos de organização típica incluem estruturar por divisões empresariais, equipes funcionais e locais geográficos.

Para adicionar uma conta:

1. No Portal do EA do Azure, clique em **Gerenciar** na área de navegação à esquerda.
2. Clique na guia **Conta** e depois, na página **Conta**, clique em **+Adicionar Conta**.
3. Selecione um departamento ou deixe-o como não atribuído e selecione o tipo de autenticação desejado.
4. Digite um nome amigável a ser usado para identificar a conta no relatório.
5. Digite o endereço de **Email do Proprietário da Conta** para associar à nova conta.
6. Confirme o endereço de email e clique em **Adicionar**.

![Exemplo mostrando a lista de contas e a opção + Adicionar Conta](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

Você pode adicionar outra conta clicando em **Adicionar Outra Conta** ou pode clicar em **Adicionar** no canto inferior direito da barra de ferramentas à esquerda.

Para confirmar a propriedade da conta:

1. Entre no Portal do EA do Azure.
1. Confirme a propriedade da conta exibindo o status. O status deve mudar de **Pendente** para **Data de Início/Término**. A Data de Início/Término é a data em que o usuário se conectou pela primeira vez e a data de término do contrato.
1. Quando a mensagem 'Aviso' aparecer, um proprietário de conta precisará clicar em **Continuar** para ativar a conta na primeira vez que entrar no Portal do EA do Azure.


## <a name="change-account-owner"></a>Alterar proprietário da conta

Os administradores corporativos podem usar o Portal do EA do Azure para transferir a propriedade da conta de assinatura em um registro. A ação move todas as assinaturas de uma conta de usuário de origem para uma conta de usuário de destino.

Pontos importantes sobre a transferência de informações de conta de usuário:

- A transferência de uma conta corporativa ou de estudante para outra é uma ação compatível.
- A transferência de uma conta Microsoft para uma conta corporativa ou de estudante é uma ação compatível.
- A transferência de uma conta corporativa ou de estudante para uma conta Microsoft não é uma ação compatível.
- A transferência de uma conta Microsoft para outra é uma ação compatível. A conta de destino deve ser uma conta válida do Azure Commerce para ser um destino válido para transferências. Para novas contas, você será solicitado a criar uma conta do Azure Commerce ao entrar no Portal do EA do Azure. Para contas existentes, você deve primeiro criar uma nova assinatura do Azure antes que a conta seja considerada qualificada.
- Quando você conclui uma transferência de assinatura, a Microsoft atualiza o proprietário da conta.

Políticas de controle de acesso baseado em função:

- Somente as transferências de assinatura do Azure entre duas IDs organizacionais no mesmo locatário preservam as políticas de RBAC (controle de acesso baseado em função) do Azure, atribuições de função de administrador de serviços e atribuições de função de coadministrador. Outras transferências de assinatura resultam na perda de suas políticas de RBAC e das atribuições de função de coadministrador de administrador de serviços. Políticas e funções de administrador não são transferidas entre diretórios diferentes. Os administradores de serviços são atualizados para o proprietário da conta de destino.
- Quando você executa transferências de assinatura entre duas IDs organizacionais no mesmo locatário, as políticas de RBAC e as funções de administrador de serviços e de coadministrador existentes são preservadas.

Antes de alterar um proprietário da conta:

1. Exiba a guia **Conta** e identifique a conta de origem. A conta de origem precisa estar ativa.
2. Identifique a conta de destino. Ela precisa estar ativa.

Para transferir a propriedade da conta para todas as assinaturas:

1. Na área de navegação à esquerda, clique em **Gerenciar**.
2. Clique na guia **Conta** e passe o mouse sobre uma conta.
3. Clique no símbolo de alterar proprietário da conta à direita. O símbolo se assemelha a uma pessoa.
4. Selecione uma conta qualificada e clique em **Avançar**.
5. Confirme a transferência e clique em **Enviar**.

![Imagem mostrando o símbolo Alterar Proprietário da Conta](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Para transferir a propriedade da conta para uma assinatura única:

1. Na área de navegação à esquerda, clique em **Gerenciar**.
2. Clique na guia **Conta** e passe o mouse sobre uma conta.
3. Clique no símbolo transferir assinaturas à direita. O símbolo se assemelha a uma página.
4. Selecione uma assinatura qualificada e clique em **Avançar**.
5. Confirme a transferência e clique em **Enviar**.

![Imagem mostrando o símbolo Transferir Assinaturas](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

Veja um vídeo que mostra o gerenciamento de usuários do Portal do EA do Azure:

[Vídeo de gerenciamento de usuários do Portal do EA do Azure](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Criar uma assinatura

Os proprietários da conta podem exibir e gerenciar assinaturas. Você pode usar assinaturas para dar acesso a ambientes e projetos de desenvolvimento às equipes de sua organização. Por exemplo, teste, produção, desenvolvimento e preparo. Ao criar assinaturas diferentes para cada ambiente de aplicativo, você ajuda a proteger cada ambiente. Você também pode atribuir uma conta de administrador de serviços diferente para cada assinatura. Você pode associar assinaturas a qualquer número de serviços. O proprietário da conta cria assinaturas e atribui uma conta de administrador de serviços a cada assinatura na conta dele.

### <a name="add-a-subscription"></a>Adicionar uma assinatura

Use as informações a seguir para adicionar uma assinatura.

Na primeira vez que você adicionar uma assinatura à conta, será solicitado que você aceite o contrato MOSA e um plano de taxas. Embora eles não sejam aplicáveis a clientes de Contrato Enterprise, eles são necessários para criar sua assinatura. O aditamento de registro do Contrato Enterprise do Microsoft Azure substitui os itens acima e a sua relação contratual não é alterada. Quando solicitado, selecione a caixa indicando que você aceita os termos.

Todas as novas assinaturas são criadas com o nome de assinatura padrão de _Microsoft Azure Enterprise_. Você pode atualizar o nome da assinatura para diferenciá-la das outras assinaturas no registro. Isso é feito também para garantir que ela seja reconhecida em relatórios no nível empresarial.

Para adicionar uma assinatura:

1. No Portal do EA do Azure, entre na conta.
2. Clique na guia **Admin** e, em seguida, clique em **Assinatura** na parte superior da página.
2. Verifique se você está conectado como o proprietário da conta em questão.
3. Clique em **+ Adicionar Assinatura** e, em seguida, clique em **Comprar**.
  Na primeira vez que você adicionar uma assinatura a uma conta, deverá fornecer suas informações de contato. Ao adicionar assinaturas adicionais, suas informações de contato serão adicionadas para você.
4. Clique em **Assinaturas**, selecione a assinatura que você criou e clique em **Editar Detalhes da Assinatura**.
5. Atualize o **Nome da Assinatura** e o **Administrador de Serviços** e selecione a marca de seleção.
  O nome da assinatura aparece em relatórios e é o nome do projeto associado à assinatura no portal de desenvolvimento.

Novas assinaturas podem levar até 24 horas para aparecer na lista de assinaturas. Depois de ter criado uma assinatura, você pode:

- [Editar detalhes da assinatura](https://account.azure.com/Subscriptions)
- [Gerenciar chaves de assinatura](https://portal.azure.com/#home)

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>Transferir uma assinatura do EA para paga conforme o uso

Para transferir uma assinatura do EA para uma assinatura individual com as tarifas pré-pagas, você precisa criar uma nova solicitação de suporte no portal do EA do Azure. Para criar uma solicitação de suporte, clique em **+ Nova solicitação de suporte** na área Ajuda e Suporte.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associar uma conta existente à sua assinatura de pagamento conforme o uso

Se você já tiver uma conta Microsoft Azure existente no portal do Microsoft Azure, insira o conta Microsoft associada ou a conta corporativa ou de estudante para associá-la ao seu registro do Enterprise Agreement.

### <a name="associate-an-existing-account"></a>Associar uma conta existente

1. No Enterprise Portal, clique em **Gerenciar**.
1. Clique na guia **Conta**.
1. Clique em **+Adicionar uma conta**.
1. Insira a conta Microsoft ou a conta corporativa ou de estudante associada à conta existente.
1. Confirme a conta Microsoft ou a conta corporativa ou de estudante associada à conta existente.
1. Forneça um nome que você gostaria de usar para identificar esta conta nos relatórios.
1. Clique em **Adicionar**.
1. Você pode adicionar outra conta marcando novamente a opção **+Adicionar uma Conta** ou pode retornar à página inicial escolhendo o botão **Administrador**.
1. Se você clicar para exibir a página **Conta**, a conta recém-adicionada será exibida com o status **Pendente**.

### <a name="confirm-account-ownership"></a>Confirmar a propriedade da conta

1. Faça logon na conta de email associada à conta Microsoft ou à conta corporativa ou de estudante que você forneceu.
1. Abra a notificação de email intitulada _"Convite para ativar a conta no serviço do Microsoft Azure de licenciamento por volume da Microsoft"_ .
1. No convite, clique no link **Entrar no Microsoft Azure Enterprise Portal**.
1. Clique em **Entrar**.
1. Insira seu conta Microsoft, ou a conta corporativa ou de estudante, e senha para entrar e confirmar a propriedade da conta.

### <a name="azure-marketplace"></a>Azure Marketplace

Embora a maioria das assinaturas converta do ambiente Pré-pago para o Azure Enterprise, os serviços do Azure Marketplace não fazem isso. Para ter um modo de exibição único de todas as assinaturas e preços, recomendamos adicionar os serviços do Azure Marketplace ao Enterprise Portal:

1. Clique em **Gerenciar** na área de navegação à esquerda.
1. Clique em **EnrollmentTab**.
1. Exiba a seção Detalhes do Registro.
1. À direita do campo Azure Marketplace, clique no ícone de lápis para habilitar e pressione **Salvar**.

O proprietário da conta agora pode comprar assinaturas do Azure Marketplace que antes pertenciam ao ambiente pré-pago.

Depois que as novas assinaturas do Azure Marketplace forem ativadas no registro, cancele as assinaturas do Marketplace criadas no ambiente pré-pago. Esta etapa é essencial para que suas assinaturas do Marketplace não entrem em um estado inadequado quando seu meio de pagamento do ambiente pré-pago expirar.

### <a name="msdn"></a>MSDN

As assinaturas do MSDN são automaticamente convertidas em Desenvolvimento/Teste do MSDN e a oferta do EA perderá os créditos monetários existentes.

### <a name="azure-in-open"></a>Azure via Open

A associação de uma assinatura do Azure via Open com um EA fará com que qualquer crédito não consumido do Azure via Open seja perdido. Para evitar qualquer perda potencial de crédito, recomendamos que os clientes consumam todo o crédito em uma assinatura do Azure via Open antes de adicionar a conta ao EA.  

### <a name="accounts-with-support-subscriptions"></a>Contas com assinaturas de suporte

Quando adicionar contas existentes ao Enterprise Portal que têm uma assinatura de suporte (mas ainda não têm uma assinatura de suporte do EA), observe que a assinatura de suporte do MOSA não transfere o suporte automaticamente e ele precisará ser readquirido no EA. Um período de carência para a cobertura do suporte será fornecido até o final do mês subsequente para dar tempo para a reordenação do suporte.

## <a name="view-usage-summary-and-download-reports"></a>Exibir resumo de uso e baixar relatórios

Os administradores corporativos podem exibir um resumo dos dados de uso, do compromisso monetário consumido e das cobranças associadas com o uso adicional no portal do Azure EA. As cobranças são apresentadas no nível do resumo em todas as contas e assinaturas.

Para exibir o uso detalhado de contas específicas:

Baixe o relatório de detalhes de uso. Clique em **Relatórios** e, em seguida, clique na guia **Uso de Download**. Na lista de relatórios, clique em **Baixar** para o relatório mensal que deseja obter.

O relatório não inclui as taxas aplicáveis. Talvez haja uma latência de até oito horas do momento em que o uso foi incorrido até ser refletido no relatório.

Para exibir os relatórios e os grafos de resumo de uso:

1. No Portal do EA do Azure, na área de navegação à esquerda, clique em **Relatórios** e exiba a guia **Resumo de Uso**.  
  ![Criar e exibir o resumo de uso e baixar relatórios](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Selecione um período de compromisso.
3. Alterne entre **M** (Mensal) e **P** (Personalizado) no canto superior direito da página para exibir o **Resumo de Uso** com datas de início e de término personalizadas.  
  ![Criar e exibir o resumo de uso e baixar relatórios no modo de exibição personalizado](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Selecione um período ou mês no grafo para exibir detalhes adicionais.
5. O grafo mostra o uso mês a mês com um detalhamento do uso utilizado, os encargos adicionais de serviço, os encargos cobrados separadamente e os encargos do Marketplace.
6. Para o mês selecionado, filtre por departamentos, contas e assinaturas abaixo do grafo.
7. Alterne entre **Cobrar por Serviços** e **Cobrar por Hierarquia**.
8. Expanda e recolha entre o **Serviço do Azure**, os **Encargos Cobrados Separadamente** e o **Azure Marketplace** para exibir detalhes.

Veja um vídeo que mostra como exibir o uso:

[Vídeo de uso do Portal do EA do Azure](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Baixar relatórios CSV

Os administradores corporativos usam a página de Download de Relatório Mensal para baixar vários relatórios como arquivos CSV. Elas incluem:

- Saldo e encargos
- Detalhes de uso
- Cobranças do Marketplace
- Tabela de preços

Para baixar relatórios:


1. No Portal do EA do Azure, clique em **Relatórios**.
2. Clique em **Baixar Uso** na parte superior da página.
3. Selecione **Baixar** ao lado do relatório do mês.

Pode haver uma latência de até cinco dias entre a data em que o uso ocorreu e o momento em que ele é mostrado nos relatórios.

Os usuários que baixarem arquivos CSV com o Safari para Excel poderão encontrar erros de formatação. Para evitar erros, abra o arquivo usando um editor de texto.

![Exemplo mostrando a página Baixar Uso](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Aqui está um vídeo que mostra como baixar informações de uso:

[Vídeo de uso do Portal do EA do Azure](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Download do relatório avançado

Para relatórios sobre contas ou intervalos de datas específicos, é possível usar o download do relatório avançado. Desde 30 de agosto de 2016, o formato do arquivo de saída foi alterado de .xlsx para .csv para acomodar conjuntos de registros maiores.

1. Escolha **Download do relatório avançado**.
1. Escolha **Intervalo de datas apropriado**.
1. Escolha **Contas apropriadas**.
1. Escolha **Solicitar dados de uso**.
1. Escolha o botão **Atualizar** até que o status do relatório seja atualizado para **Download**.
1. Baixe o relatório.

## <a name="ea-term-glossary"></a>Glossário de termos do EA

- **Conta**: uma unidade organizacional no Portal do EA do Azure que é usada para administrar assinaturas e para fazer relatórios.
- **Proprietário da conta**: a pessoa identificada para gerenciar assinaturas e administradores de serviço no Microsoft Azure. Ela pode exibir dados de uso nessa conta e suas assinaturas associadas.
- **Assinatura de aditamento**: um único ano ou uma assinatura coincidente sob o aditamento do registro.
- **Compromisso**: compromisso de um valor monetário anual para serviços do Microsoft Azure a uma tarifa de compromisso reduzida para uso em relação a esse pagamento antecipado.
- **Administrador de departamento**: a pessoa identificada para gerenciar departamentos, criar novas contas e proprietários de contas, exibir detalhes de uso dos departamentos que ela gerencia e exibir custos quando permissões forem concedidas.
- **Número de registro**: um identificador exclusivo fornecido pela Microsoft para identificar o registro específico associado a um contrato Enterprise.
- **Administrador corporativo**: a pessoa identificada para gerenciar departamentos e proprietários de departamentos, além de contas e proprietários de contas no Microsoft Azure. Ela tem a capacidade de gerenciar administradores corporativos e exibir dados de uso, quantidades cobradas e encargos não cobrados em todas as contas e assinaturas associadas ao registro corporativo.
- **Contrato Enterprise**: um contrato de licenciamento da Microsoft para clientes com compras centralizadas que desejam padronizar toda a organização com a tecnologia da Microsoft e manter uma infraestrutura de tecnologia da informação com um padrão de software da Microsoft.
- **Registro do contrato Enterprise**: um registro no programa de contrato Enterprise que fornece produtos da Microsoft em volume com tarifas reduzidas.
- **Conta Microsoft**: um serviço baseado na Web que permite que sites participantes autentiquem um usuário com um único conjunto de credenciais.
- **Aditamento do registro do Enterprise do Microsoft Azure (aditamento do registro)** : um aditamento assinado por uma empresa que fornece acesso ao Microsoft Azure como parte de seu registro Enterprise.
- **Portal do EA do Azure**: o portal usado por nossos clientes empresariais para gerenciar suas contas do Microsoft Azure e assinaturas relacionadas.
- **Quantidade de recursos consumidos**: a quantidade de um serviço individual do Microsoft Azure utilizado em um mês.
- **Administrador de serviços**: a pessoa identificada para acessar e gerenciar assinaturas e projetos de desenvolvimento no Portal do EA do Azure.
- **Assinatura**: representa uma assinatura do Portal do EA do Azure e é um contêiner de serviços gerenciados do Microsoft Azure pelo mesmo administrador de serviços.
- **Conta corporativa ou de estudante**: para organizações que configuraram o Active Directory com Federação para a nuvem e todas as contas estão em um único locatário.

### <a name="enrollment-statuses"></a>Status do registro:

- **Pendente**: o administrador do registro precisa entrar no Portal do EA do Azure. Depois de conectado, o registro mudará para o status Ativo.
- **Ativo**: o registro está Ativo e as contas e assinaturas podem ser criadas no Portal do EA do Azure. O registro permanecerá ativo até a data de término do contrato Enterprise.
- **Termo estendido indefinido**: o status de termo estendido indefinido ocorre após a data de término do contrato Enterprise. Ele permite que os clientes do EA que optaram pelo período estendido continuem a usar o Azure indefinidamente no final do contrato Enterprise. Antes que o registro do EA atinja a data de término do contrato corporativo, o administrador do registro deve decidir se renovará o registro adicionando mais compromisso monetário, o transferirá para um novo registro, migrará para o Microsoft Online Subscription Program (MOSP) ou confirmará a desativação de todos serviços associados ao registro.
- **Expirado**: o cliente do EA é recusado do prazo estendido e o registro do EA atingiu a data de término do contrato corporativo, a inscrição expirará e todos os serviços associados serão desativados.
- **Transferidos**: registros em que todos os serviços e contas associados foram transferidos para um novo registro serão exibidos com o status Transferidos. Observe que os registros não são transferidos automaticamente se um novo número de registro é gerado durante a renovação. O número de registro anterior deve ser incluído na documentação de renovação do cliente para facilitar uma transferência automática.

## <a name="get-started-on-azure-ea-faq"></a>Introdução às perguntas frequentes do EA do Azure

Este documento fornece detalhes sobre as perguntas típicas feitas pelos clientes durante o processo de integração.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Posso associar minha Conta do Azure existente ao Registro Enterprise?

Sim, pode. É importante observar que todas as assinaturas do Azure das quais você é o proprietário da conta serão convertidas para o Registro Enterprise. Isso inclui assinaturas que utilizam crédito mensal (por exemplo, Visual Studio, AzurePass, MPN, BizSpark, etc.) e isso significa que você perderá o crédito mensal fazendo isso.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>Associei acidentalmente minha conta do Azure existente ao registro Enterprise. Como resultado, perdi meu crédito mensal. É possível ter meu crédito mensal de volta?

Para recuperar seu benefício individual do Azure da assinatura do Visual Studio depois de autenticar como um proprietário da conta do EA, depois de ter usado o mesmo logon para o EA como sua assinatura do Visual Studio, você deve:
1. excluir esse proprietário de conta do Portal do EA, depois de remover ou mover as assinaturas do Azure que ele possui, e fazer com que ele se inscreva nos benefícios individuais do Visual Studio Azure.
 OU
1. excluir o assinante do Visual Studio no site de administração no VLSC e reatribuir a assinatura, fazendo com que ele use um logon diferente. Posteriormente, ele pode se inscrever nos benefícios individuais do Visual Studio Azure.

### <a name="what-type-of-subscription-should-i-create"></a>Que tipo de assinatura devo criar?

O Portal do EA oferece dois tipos de assinaturas para clientes empresariais:

- Microsoft Azure Enterprise – ideal para:
  - Utilização em qualquer tipo de produção
  - Melhores preços com base no gasto da infraestrutura
  - Encontre mais detalhes no endereço https://azure.microsoft.com/pricing/enterprise-agreement/
- Desenvolvimento/Teste Enterprise – ideal para:
  - Todas as cargas de trabalho de desenvolvimento/teste da equipe
  - Cargas de trabalho de desenvolvimento/teste individual, de média a pesada
  - Acesso a imagens especiais do MSDN e taxas de serviço preferenciais
  - Encontre mais detalhes no endereço https://azure.microsoft.com/offers/ms-azr-0148p/

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>É possível transferir a propriedade de assinatura para outra conta?

Sim, é possível transferir a propriedade de assinatura para uma conta diferente. Por exemplo, se uma Conta A tiver três assinaturas, o administrador corporativo poderá transferir uma assinatura para a Conta B, uma para a Conta C e outra para a Conta D ou todas para a Conta E.

Você pode acessar o EA e clicar em Gerenciar > Conta, passar o cursor do mouse sobre **Conta** (extremidade direita) e verá a opção Transferir Propriedade (ícone de foto) e Transferir Assinatura (ícone de lista).

Esta opção só estará visível para contas ativas.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Eu vejo os padrões de nome de assinatura como nome da oferta, devo alterar o nome da assinatura para algo significativo para minha organização?

Qualquer assinatura criada será padronizada com o tipo de oferta que você escolher. Recomendamos alterar o nome da assinatura para algo que facilite o rastreamento da assinatura.

**Para alterar o nome:**
1. Entre em [https://account.windowsazure.com](https://account.windowsazure.com).
1. Clique na lista Assinatura.
1. Escolha Assinatura.
1. Clique no ícone **Gerenciar assinatura**.
1. Edite os detalhes da assinatura.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Como faço para acompanhar os custos incorridos pelo Centro de Custo?

Para acompanhar o custo pelo Centro de custo, você precisa definir o Centro de custo em qualquer um dos níveis a seguir:
- department
- Conta
- Subscription

Dependendo daquilo que precisa, você poderá usar o mesmo Centro de Custo para acompanhar o uso e o custo associados a um Centro de Custo específico.

Por exemplo, para acompanhar o custo de um projeto especial em que vários departamentos estão envolvidos, talvez você queira usar o Centro de Custo no nível de uma assinatura para controlar o uso e o custo.

Não é possível definir o Centro de Custo no nível de serviço e, caso você queira controlar o uso no nível de serviço, você pode usar o recurso "Marcação" disponível no nível de serviço.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Como faço para acompanhar o uso e o gasto pelos diversos departamentos da organização?

Você pode criar quantos departamentos forem necessários em seu registro do EA. Para acompanhar o uso corretamente, você precisa garantir que as assinaturas não sejam compartilhadas entre os departamentos.

Depois de criar o departamento e a assinatura, você poderá ver as informações que fluem no relatório de uso e que ajudarão a controlar o uso e o gerenciamento de custos/gastos no nível do departamento.

Você também pode acessar o uso por meio da API e de informações detalhadas. O código de exemplo está disponível em [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Posso definir a cota de gastos e receber alertas à medida que me aproximar do meu limite?

Você pode definir a cota de gastos no nível do departamento e o sistema o notificará automaticamente quando seus limites de gastos atingirem 50%, 75%, 90% e 100% da cota definida.

Para definir sua cota de gastos, clique no departamento ao qual você deseja adicionar um limite de gastos e clique no ícone de edição. Clique em **Salvar** para salvar os detalhes.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Usei GRs (Grupos de Recursos) para implementar o RBAC e acompanhar o uso, como posso exibir os detalhes de uso associados?

Se informações como "Grupos de Recursos" e "Marcação"forem usadas, elas serão rastreadas no nível de serviço e as informações estarão disponíveis no arquivo de download (CSV) de uso detalhado, que pode ser baixado no Portal do EA do Azure [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage).

Você também pode acessar o uso por meio da API e de informações detalhadas. O código de exemplo está disponível em [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

Observe que você só pode aplicar marcações a recursos que dão suporte às operações do Resource Manager. Se você tiver criado uma máquina virtual, uma rede virtual ou um armazenamento por meio do modelo de implantação clássica (como por meio do portal clássico), não poderá aplicar uma marcação a esse recurso. Para dar suporte à marcação, implante esses recursos novamente por meio do Resource Manager. Todos os outros recursos oferecem suporte à marcação.

### <a name="can-i-perform-analyses-using-power-bi"></a>Posso executar análises usando o Power BI?

Sim. Com o pacote de conteúdo do Microsoft Azure Enterprise para Power BI, você pode importar e analisar rapidamente o consumo do Azure em seu registro Enterprise. Descubra qual departamento, conta ou assinatura consumiu mais uso, que serviço foi mais usado pela organização, ou rastreie tendências de gasto e uso.

**Navegue até o site do Power BI:**

 1. Entre com uma conta corporativa ou de estudante válida.
    - A conta corporativa ou de estudante pode ser a mesma ou uma diferente da usada para acessar o registro pelo Portal do EA do Azure.
 1. No painel de serviços, escolha:
    - Bloco do Microsoft Azure Enterprise.
    - Clique em **Conectar**.
 1. Na tela "Conectar-se ao Azure Enterprise", escolha:
    - URL do ambiente do Azure: [https://ea.azure.com](https://ea.azure.com).
    - Número de meses: escolha entre 1 e 36.
    - Número do registro: insira o número do registro.
    - Clique em **Próximo**.
 1. Insira a Chave de API na caixa da Chave de Autenticação. Acesse a chave de API no portal do EA do Azure na guia "uso do download", clique em **Chave de Acesso à API**.
    - Copie e cole a chave na caixa "Chave de Conta".
    - Os dados levarão cerca de 5-30 minutos para serem carregados no Power BI, dependendo do tamanho do conjunto de dados.

O Power BI Reporting está disponível para clientes diretos, indiretos e parceiros do EA que podem exibir informações de cobrança.

## <a name="next-steps"></a>Próximas etapas

- Os administradores do Portal do EA do Azure devem ler [Administração do Portal do EA do Azure](billing-ea-portal-administration.md) para aprender sobre tarefas administrativas comuns.
- Se você precisar de ajuda para solucionar problemas do Portal do EA do Azure, confira [Solucionar problemas de acesso ao Portal do EA do Azure](billing-ea-portal-troubleshoot.md).
- Para obter um guia de integração do EA do Azure, confira [Guia de integração do EA do Azure](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
