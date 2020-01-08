---
title: Contratos e aditamentos de EA do Azure
description: Este artigo explica como os contratos e aditamentos de EA do Azure afetam o uso do Portal do EA do Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 1f421abf1c9cff545c77a9fcbbb4acfd76d1e1d0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644402"
---
# <a name="azure-ea-agreements-and-amendments"></a>Contratos e aditamentos de EA do Azure

O artigo explica como os contratos e aditamentos do Azure EA podem afetar seu acesso, uso e pagamento pelos serviços do Azure.

## <a name="amendment-status"></a>Status do aditamento

Qualquer aditamento para os clientes do Azure precisa passar pelo parceiro ou equipe de conta Microsoft desses clientes e ser processado pelo centro de operações regionais. Se acreditar que um aditamento não foi processado, consulte seu parceiro, consultor de software ou equipe de contas Microsoft.

## <a name="enrollment-provisioning-status"></a>Status de provisionamento do registro

A data de início de um novo compromisso monetário é definida pela data em que o centro de operações regionais o processou. Como as ordens de compromisso monetário realizadas por meio do portal do Azure EA são processadas no fuso horário UTC, você poderá enfrentar algum atraso se sua ordem de compra de compromisso monetário tiver sido processada em uma região diferente. A data de início da cobertura na ordem de compra em https://www.explore.ms mostra o início do compromisso monetário. A data de início da cobertura é quando o compromisso monetário aparece no Portal do EA do Azure.

## <a name="special-pricing-and-azure-commitment-discount-amendments"></a>Preços especiais e aditamentos de desconto do compromisso do Azure

Se você necessitar de aditamentos de ACD (Desconto de Compromisso com o Azure), precisará discuti-los com sua equipe de contas. A equipe do Azure Ops poderá aplicar o ACD depois que um aditamento tiver sido processado pela Business Desk e solicitado para a equipe do Azure Ops. O desconto de ACD é aplicado somente ao mês atual. Todos os meses anteriores são compensados por crédito retroativo. 

## <a name="support-offer-not-provisioned"></a>Oferta de suporte não provisionada

Você pode solicitar suporte Standard ou Pro-Direct comprando SKUs específicos a um suporte. O pedido é semelhante a uma ordem de compra do SKU monetário do Azure do EA. Por exemplo, 6QK-00001. Os números de SKU de suporte são W6T-00002 (Pro-Direct) e W6T-00003 (Standard). Verifique as ofertas de suporte atuais antes de comprar SKUs de suporte.

O registro precisa ter pelo menos uma conta ativa para que a oferta de suporte seja provisionada.

 A [Oferta do plano de suporte do Contrato Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support/) do Azure está disponível para alguns clientes.

## <a name="enrollment-status"></a>Status do registro

Um registro tem um dos valores de status a seguir. Cada valor determina como você pode usar e acessar um registro. O status de registro determina em qual fase seu registro está. Ele informa se o registro precisa ser ativado antes que possa ser usado. Ou se o período inicial expirou e você está recebendo cobrança pelo uso excedente.

**Pendente** –o administrador do registro precisa entrar no Portal do EA do Azure. Depois de conectado, o registro muda para o status **Ativo**.

**Ativo** – o registro está acessível e pode ser usado. Você pode criar contas e assinaturas no Portal do EA do Azure. O registro permanece ativo até a data de término do Contrato Enterprise.

**Termo Estendido Indefinido** – o status de termo estendido indefinido ocorre depois que a data de término do Contrato Enterprise é atingida. Antes que o registro do EA atinja a data de término do Contrato Enterprise, o administrador do registro deve decidir:

- Renovar o registro adicionando um compromisso monetário adicional
- Transferir o registro existente para um novo registro
- Migrar para o MOSP (Programa de Assinatura do Microsoft Online)
- Confirmar a desabilitação de todos os serviços associados ao registro

**Expirado**: o registro do EA expira quando atinge a data de término do Contrato Enterprise. O cliente EA é desvinculado do termo estendido e todos os serviços dele são desabilitados.

Desde 1º de agosto de 2019, novos formulários de recusa não são aceitos para clientes comerciais do Azure. Em vez disso, todos os registros entram em um termo estendido indefinido. Se você quiser parar de usar os serviços do Azure, encerre sua assinatura no [portal do Azure EA](https://portal.azure.com). Ou, seu parceiro pode enviar uma solicitação de encerramento https://www.explore.ms. Não há alteração para clientes com tipos de contrato governamentais.

**Transferido** – o status transferido é aplicado a registros que têm os respectivos serviços e contas associados transferidos para um novo registro. Os registros não são transferidos automaticamente se um novo número de registro é gerado durante a renovação. O número de registro anterior deve ser incluído na solicitação de renovação do cliente para uma transferência automática.

## <a name="partner-markup"></a>Marcação de parceiro

No Portal do EA do Azure, a marcação de preço do parceiro ajuda a habilitar relatórios de custo melhores para os clientes. O Portal do EA do Azure mostra o uso e os preços configurados por parceiros para os respectivos clientes.

A marcação permite que os administradores de parceiros adicionem uma marcação percentual aos seus contratos empresariais indiretos. A marcação percentual aplica-se a todas as informações de serviço internas da Microsoft no portal do Azure EA, como taxas de medição, compromissos monetários e pedidos. Após a marcação ser publicada pelo parceiro, o cliente vê os custos do Azure no portal do Azure EA. Por exemplo, resumo de uso, listas de preços e relatórios de uso baixados.

Desde setembro de 2019, os parceiros podem aplicar marcação a qualquer momento durante um termo. Eles não precisam aguardar até o próximo aniversário do termo para aplicarem a marcação.

A Microsoft não acessará nem utilizará a marcação e os preços associados fornecidos para qualquer finalidade, a menos que seja explicitamente autorizado pelo parceiro do canal.

### <a name="how-the-calculation-works"></a>Como funciona o cálculo

O LSP fornece um número percentual único no portal de EA.  Todas as informações comerciais no portal serão elevadas pelo percentual fornecido pelo LSP. Exemplo:

- O cliente assina um EA com compromisso monetário de US$ 100.000,00.
- A taxa de medição para o Serviço A é de US$ 10/hora.
- O LSP define a porcentagem de marcação de 10% no portal de EA.
- O exemplo a seguir mostra como o cliente verá as informações comerciais:
    - Saldo monetário: US$ 110.000,00.
    - Taxa de medição para o Serviço A: US$ 11/hora.
    - Informações de uso/hospedagem para o Serviço A, quando usado por 100 horas: US$ 1.100,00.
    - Saldo Monetário disponível para o cliente após a dedução do consumo do Serviço A: US$ 108.900,00.

### <a name="when-to-use-a-markup"></a>Quando usar uma marcação

Use o recurso se você definir a mesma porcentagem de marcação em TODAS as transações comerciais no EA. Ou seja, se você marcar as informações de compromisso monetário, as taxas de medição, as informações do pedido etc.

Não use o recurso de marcação se:
- Você usar taxas diferentes entre o compromisso monetário e as taxas de medição.
- Você usar taxas diferentes para medições diferentes.

Se você usa taxas diferentes para medições diferentes, recomendamos que desenvolva uma solução personalizada com base na Chave de API, que pode ser fornecida pelo cliente para efetuar pull de dados de consumo e fornecer relatórios.

### <a name="other-important-information"></a>Outras informações importantes

Esse recurso serve para fornecer uma estimativa do custo do Azure para o cliente final. O LSP é responsável por todas as transações financeiras com o cliente sob o EA.

Revise as informações comerciais, como informação do saldo monetário, lista de preços etc., antes de publicar os preços marcados para o cliente final.

### <a name="how-to-add-a-price-markup"></a>Como adicionar uma marcação de preço

**Etapa 1: adicionar marcação de preço**

1. No Enterprise Portal, escolha **Relatórios** na área de navegação à esquerda.
1. Em _Resumo do uso_, clique na palavra **Marcação** em azul.
1. Insira a porcentagem de marcação (entre -100 e 100) e clique **Visualizar**.


**Etapa 2: revisar e validar**

Revise o preço de marcação em _Resumo do uso_ no termo de compromisso na exibição de cliente. O preço da Microsoft ainda estará disponível na exibição de parceiro. É possível alternar as exibições usando a opção "pessoas" da marcação de parceiro na parte superior direita.

1. Reveja os preços na tabela de preços.
1. É possível fazer alterações antes da publicação selecionando **Editar** na guia _Exibir resumo de uso > Exibição de cliente_.  
  Os preços de serviço e os saldos de compromisso serão marcados com as mesmas porcentagens. Se você tem porcentagens diferentes para o saldo monetário e taxas de medição ou percentuais diferentes para serviços diferentes, não use esse recurso.

**Etapa 3: publicar**

Após a revisão e validação do preço, clique em **Publicar**.
  
Os preços com marcação ficarão disponíveis para os administradores corporativos imediatamente após a publicação. Não é possível fazer edições na marcação. Você deve desabilitar a marcação e começar da Etapa 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>Quais registros têm uma marcação habilitada?

Para verificar se um registro tem uma marcação publicada, clique em **Gerenciar** no painel de navegação à esquerda e clique na guia **Registro**. Marque a caixa de registro e veja o status da marcação em _Detalhes do registro_. Lá será exibido o status atual do recurso de marcação para esse EA, que poderá ser Desabilitado, Versão Prévia ou Publicado.

### <a name="how-can-the-customer-download-usage-estimates"></a>Como o cliente pode baixar estimativas de uso?

Após a publicação da marcação de parceiro, o cliente indireto terá acesso ao saldo, arquivos mensais de cobrança em .csv e arquivos de uso detalhado em .csv. Os arquivos de uso detalhado incluirão a taxa de recursos e o custo estendido.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Como eu posso, no papel de parceiro, aplicar a marcação a clientes de EA existentes que anteriormente estavam com outro parceiro?
Os parceiros podem usar o recurso de marcação (no Azure EA) após o processamento de uma Alteração do Parceiro de Canal; não é necessário aguardar o próximo vencimento.


## <a name="resource-commitment-and-requesting-quota-increases"></a>Confirmação de recursos e solicitação de aumento de cota

**O sistema impõe as seguintes cotas padrão por assinatura:**

| **Recurso** | **Cota padrão** | **Comentários** |
| --- | --- | --- |
| Instâncias de computação do Microsoft Azure | Vinte instâncias de computação pequenas simultâneas ou equivalentes nos outros tamanhos de instância de computação. | A tabela a seguir instrui como calcular o número equivalente de instâncias pequenas:<ul><li> Extra pequena: uma instância pequena equivalente </li><li> Pequena: uma instância pequena equivalente </li><li> Média: duas instâncias pequenas equivalentes </li><li> Grande: quatro instâncias pequenas equivalentes </li><li> Extragrande: oito instâncias pequenas equivalentes </li> </ul>|
| VMs v2 de instâncias de computação do Microsoft Azure | EA: 350 núcleos | VMs v2 do GA IaaS:<ul><li> Família A0\_A7: 350 núcleos </li><li> Família B\_A0\_A4: 350 núcleos </li><li> Família A8\_A9: 350 núcleos </li><li> Família DF: 350 núcleos</li><li> GF: 350 núcleos </li></ul>|
| Serviços Hospedados do Microsoft Azure | Seis serviços hospedados | Esse limite de serviços hospedados não pode ser aumentado além de seis para uma assinatura individual. Se precisar de serviços hospedados adicionais, incorpore outras assinaturas. |
| Armazenamento do Microsoft Azure | Cinco contas de armazenamento, cada uma com um tamanho máximo de 100 TB. | Você pode aumentar o número de contas de armazenamento para até 20 por assinatura. Se precisar de contas de armazenamento adicionais, incorpore outras assinaturas. |
| SQL Azure | 149 bancos de dados de qualquer tipo (ou seja, Web Edition ou Business Edition). |   |
| Controle de acesso | 50 namespaces por conta. 100 milhões de transações de Controle de Acesso por mês |   |
| Barramento de Serviço | 50 namespaces por conta. 40 conexões de Barramento de Serviço | Os clientes que adquirem conexões do Barramento de Serviço por meio de pacotes de conexão terão cotas iguais ao ponto médio entre o pacote de conexão adquirido e o próximo valor mais alto do pacote de conexão. Os clientes que escolherem um Pacote de 500 terão uma cota de 750. |

## <a name="resource-commitment"></a>Confirmação de recurso

A Microsoft fornecerá serviços para você pelo menos até o nível do uso associado incluído no compromisso mensal que você comprou (o Compromisso de Serviço), mas todos os outros aumentos nos níveis de utilização de recursos do serviço (por exemplo, aumento do número de instâncias de computação em execução ou aumento da quantidade de armazenamento em uso) estão sujeitos à disponibilidade desses recursos de serviço.

Qualquer cota descrita acima não é um Compromisso de Serviço. Para fins de determinação do número de pequenas instâncias de computação simultâneas (ou equivalente) que a Microsoft fornecerá como parte de um Compromisso de Serviço, isso é determinado pela divisão do número de horas de instância pequena de computação adquirido em um mês pelo número de horas do mês mais curto do ano (ou seja, fevereiro com 672 horas).

## <a name="requesting-a-quota-increase"></a>Solicitar um aumento de cota

Você pode solicitar um aumento de cota a qualquer momento enviando uma [solicitação online](https://g.microsoftonline.com/0WAEP00en/6). Para processar sua solicitação, forneça as seguintes informações:

- A conta Microsoft ou a conta corporativa ou de estudante associada ao proprietário da conta da sua assinatura. Esse é o endereço de email utilizado para entrar no portal do Microsoft Azure para gerenciar suas assinaturas. Além disso, identifique que essa conta está associada a um registro de EA.
- Os recursos e os valores para os quais você quer um aumento de cota.
- A ID da Assinatura do Portal do Desenvolvedor do Azure associada ao seu serviço.
  - Para saber mais sobre como obter sua ID de assinatura, [entre em contato com o suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="azure-compute-pre-purchase-plan-cpp"></a>Plano de pré-aquisição de computação (CPP) do Azure

O Plano de pré-aquisição de computação do Azure (CPP, anteriormente conhecido como Promoção de computação do Azure) é uma oferta que fornece aos clientes com cargas de trabalho estáveis e previsíveis a oportunidade de obter a computação por um desconto considerável ao comprar previamente a computação do Azure por 12 meses. Ele está disponível para o EA e visível no portal de EA.

Se você solicitou um SKU de CPP, será para um tipo específico de computador em um data center específico. A situação atual desse recurso não permite que forneçamos uma lista de todas as compras de CPP feitas por um cliente. Portanto, não há uma lista de créditos em espera. No entanto, ao configurar os tipos de computadores comprados no data center em que foram adquiridos, você verá a alocação mensal no relatório de Resumo do uso no portal do EA.

O valor da pré-aquisição será exibido como um crédito na coluna "Unidades Incluídas", e o uso não resultará em cobranças até que as "Unidades Incluídas" sejam consumidas. Após o consumo, o uso adicional estará em taxas negociadas na tabela de preços normal do EA do cliente.

A maneira de compra dos créditos têm base no número de computadores de uma classe por mês. Por exemplo: se você comprou 70 VMs A2 por ano no Leste dos EUA 2, não veria isso no portal do EA da mesma maneira que comprou.

As máquinas mensais são calculadas como em execução 744 horas por mês. A conversão disso em horas daria 70 vezes 744 ou 52.080 horas adquiridas de VMs A2 por mês. Como no Resumo do uso do portal do EA, as VMs A2 são indicadas em incrementos de 100 horas, você veria um crédito de 520,8 (52.080/100) "Unidades Incluídas" por mês e veria as unidades consumidas até então no mês. Os encargos de uso devem aparecer como zero até que as unidades mensais consumidas excedam as unidades mensais incluídas.

Tipos diferentes de computador usam unidades diferentes de medida. Por exemplo, as VMs D3 e D4 usam uma unidade de medida de 10 horas em nosso relatório Resumo do uso, de modo que uma compra de 70 delas mostraria 5208 unidades incluídas por mês (52.080/10).

Em geral, a fórmula abaixo fornecerá o número de VMs adquiridas com base nas unidades de medida:

<center><b> ("Quantidade Incluída"*"Unidades de Medida")/744 </b></center>

## <a name="plan-skus"></a>SKUs do plano

As SKUs do plano oferecem a capacidade de comprar um pacote de serviços integrados por uma taxa com desconto. As SKUs do plano foram projetadas para se complementarem por meio de ofertas integradas adicionais e proporcionarem maior economia de custos.

Um exemplo seria a assinatura do OMS (Operations Management Suite). O OMS oferece uma maneira simples de acessar um conjunto completo de recursos de gerenciamento baseado em nuvem, incluindo análise, configuração, automação, segurança, backup e recuperação de desastres. As assinaturas do OMS incluem direitos aos componentes do System Center para fornecer uma solução completa para ambientes de nuvem híbrida.

Os Administradores Corporativos podem atribuir Proprietários de Conta para provisionar SKUs de plano adquiridas anteriormente no Enterprise Portal executando estas etapas:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Exibir a tabela de preços para verificar a quantidade incluída

1. Entre como um Administrador Corporativo.
1. Clique em **Relatórios** na área de navegação à esquerda.
1. Clique na guia **Tabela de preços**.
1. Clique no ícone "Download" no canto superior direito.
1. Localize os números de parte da SKU do Plano correspondente com filtro na coluna "Quantidade Incluída" e selecione valores maiores do que "0".

### <a name="provision-the-plan-skus"></a>Provisionar as SKUs do plano

Os administradores corporativos podem adicionar novos proprietários de conta, associar um proprietário de conta existente ou solicitar que os proprietários de conta existentes entrem no portal do Azure EA para provisionar SKUs de plano adquiridos anteriormente no portal do Azure EA executando as etapas abaixo.  

**Adicionar um novo Proprietário de Conta (feito pelo administrador do EA):**

1. No Portal do Azure EA, escolha **Gerenciar** na área de navegação à esquerda.
1. Clique na guia **Conta**.
1. Na página Conta, clique em **+Adicionar Conta**.
1. Selecione um departamento ou deixe como não atribuído.
1. Selecione o tipo de autenticação pretendido.
1. Forneça um nome amigável que você gostaria de usar para identificar esta conta no relatório.
1. Insira o endereço de email do proprietário da conta que você quer associar à nova conta.
1. Confirme o endereço de email que você deseja associar à nova conta.
1. Clique em **Adicionar**.
1. Você pode adicionar outra conta clicando em **Adicionar outra conta** ou pode clicar no botão **Adicionar** no canto inferior direito da barra de ferramentas à esquerda.
1. Agora, o proprietário da conta pode entrar e adicionar as assinaturas relevantes para provisionar as SKUs do plano

**Associar um proprietário de uma conta existente:**

1. No Enterprise Portal, clique em **Gerenciar**.
1. Clique na guia **Conta**.
1. Clique em **+Adicionar uma conta**. Insira a conta Microsoft ou a conta corporativa ou de estudante associada à conta existente.
1. Confirme a conta Microsoft ou a conta corporativa ou de estudante associada à conta existente.
1. Forneça um nome que você gostaria de usar para identificar esta conta nos relatórios.
1. Clique em **Adicionar**.
1. Você pode adicionar outra conta selecionando novamente a opção **+Adicionar uma conta** ou pode retornar à página inicial escolhendo o botão **Administrador**.  
1. Se você clicar para exibir a página Conta, a conta recém-adicionada será exibida com o status "Pendente". Ela será alterado para o status "Ativo" depois que o Proprietário da Conta tiver feito logon no portal do EA pela primeira vez.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Proprietários de conta novos/existentes para criar novas assinaturas

**Etapa 1: entre na conta**
1. No portal do Azure EA, selecione a guia **Gerenciar** e navegue até **Assinatura** no menu superior.
1. Verifique se você está conectado como o proprietário da conta.
1. Clique em **+Adicionar assinatura**.
1. Clique em **Comprar**.

Na primeira vez que você adicionar uma assinatura a uma conta, deverá fornecer suas informações de contato. Ao adicionar outras assinaturas, suas informações de contato serão preenchidas para você.

Na primeira vez que você adicionar uma assinatura à conta, receberá a solicitação para aceitar o contrato MOSA e um Plano de Taxas. Essas seções NÃO são aplicáveis a Clientes com Enterprise Agreement, mas atualmente são necessárias para provisionar sua assinatura. O aditamento de registro do Contrato Enterprise do Microsoft Azure substitui os itens acima e a sua relação contratual não será alterada. Marque a caixa indicando que você aceita os termos.

**Etapa 2: atualizar o nome da assinatura**

Todas as novas assinaturas serão adicionadas com o nome de assinatura padrão "Microsoft Azure Enterprise". É importante atualizar o nome da assinatura para diferenciá-lo das outras assinaturas em seu Registro Corporativo e garantir que seja reconhecido em relatórios no nível corporativo.

Clique em **Assinaturas**, clique na assinatura que você criou e clique em **Editar detalhes da assinatura**.

Atualize o nome da assinatura e o administrador de serviços e clique na marca de seleção. O nome da assinatura aparecerá em relatórios e também será o nome do projeto associado à assinatura no portal de desenvolvimento.
Novas assinaturas podem demorar até 24 horas para aparecer na lista de assinaturas.

Apenas os proprietários da conta podem exibir e gerenciar assinaturas.

### <a name="troubleshooting"></a>solução de problemas

**Proprietário da conta aparecendo no status pendente**

Quando novos Proprietários de Conta (AO) são adicionados a um registro pela primeira vez, o status deles aparece sempre como "pendente". Após receber o email de ativação de boas-vindas, o AO poderá entrar para ativar a própria conta. Essa ativação atualizará o status da conta de "pendente" para "ativo".

**Usos cobrados após a compra de SKUs do plano**

Esse cenário ocorre quando o cliente implantou serviços sob o número de registro errado ou selecionou os serviços incorretos.

Para validar se você está implantando sob o registro correto, verifique as informações das unidades incluídas na tabela de preços. Entre como um Administrador Corporativo e clique em **Relatórios** no painel de navegação à esquerda e selecione a guia **Tabela de preços**. Clique no ícone de Download no canto superior direito e localize os números de parte do SKU do Plano correspondente com filtro na coluna "Quantidade Incluída" e selecione valores maiores do que "0".

Verifique se o seu plano do OMS está aparecendo na tabela de preços sob as unidades incluídas. Se não houver unidades incluídas para o plano do OMS em seu registro, seu plano do OMS poderá estar em outro registro. Entre em contato com o Suporte do Azure Enterprise Portal em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Se as unidades incluídas para os serviços na tabela de preços não corresponderem ao que você implantou, por exemplo, Dados Premium Analisados de Insights Operacionais versus Dados Padrão Analisados de Insights Operacionais, significa que você pode ter implantado serviços que não têm cobertura do plano. Entre em contato com o Suporte do Azure Enterprise Portal em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) para obter ajuda.

**Serviços da SKU de plano provisionado no registro incorreto**

Se você tem vários registros e implantou serviços sob o número de registro errado, que não tem um plano do OMS, entre em contato com o suporte do Azure Enterprise Portal em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o Portal do EA do Azure, confira [Introdução ao Portal do EA do Azure](billing-ea-portal-get-started.md).
- Os administradores do Portal do EA do Azure devem ler [Administração do Portal do EA do Azure](billing-ea-portal-administration.md) para aprender sobre tarefas administrativas comuns.
