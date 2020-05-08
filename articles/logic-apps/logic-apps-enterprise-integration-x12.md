---
title: Enviar e receber mensagens X12 para B2B
description: Trocar mensagens X12 para cenários de integração corporativa B2B usando aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 8ec20e03544ba54b83130ae41244dcdb186252d0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612973"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens X12 para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com Enterprise Integration Pack

Para trabalhar com mensagens X12 em aplicativos lógicos do Azure, você pode usar o conector do X12, que fornece gatilhos e ações para gerenciar a comunicação do X12. Para obter informações sobre mensagens EDIFACT, consulte [Exchange EDIFACT messages](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico do qual você deseja usar o conector X12 e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. O conector do X12 fornece apenas ações, não gatilhos. Se você não estiver familiarizado com os Aplicativos Lógicos, examine [O que são Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início rápido: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua assinatura do Azure e vinculada ao aplicativo lógico no qual você planeja usar o conector do X12. O aplicativo lógico e a conta de integração devem existir no mesmo local ou na região do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já definiu em sua conta de integração usando o qualificador de identidade X12.

* Os [esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md) a serem usados para validação de XML que você já adicionou à sua conta de integração. Se você estiver trabalhando com esquemas HIPAA (Health Insurance Portability and Accountability Act), consulte esquemas da [HIPAA](#hipaa-schemas).

* Antes de usar o conector do X12, você deve criar um [contrato](../logic-apps/logic-apps-enterprise-integration-agreements.md) X12 entre seus parceiros comerciais e armazenar esse contrato em sua conta de integração. Se você estiver trabalhando com esquemas HIPAA (Health Insurance Portability and Accountability Act), precisará adicionar uma `schemaReferences` seção ao seu contrato. Para obter mais informações, consulte [esquemas da HIPAA](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Configurações de Recebimento

Depois de definir as propriedades do contrato, você pode configurar como este contrato identifica e manipula as mensagens de entrada recebidas do seu parceiro por meio deste contrato.

1. Em **Adicionar**, selecione **Configurações de Recebimento**.

1. Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. As **configurações de recebimento** são organizadas nessas seções:

   * [Identificadores](#inbound-identifiers)
   * [Confirmação](#inbound-acknowledgement)
   * [Esquemas](#inbound-schemas)
   * [Envelopes](#inbound-envelopes)
   * [Números de Controle](#inbound-control-numbers)
   * [Validações](#inbound-validations)
   * [Configurações Internas](#inbound-internal-settings)

   Para obter as descrições da propriedade, confira as tabelas nesta seção.

1. Quando terminar, certifique-se de salvar suas configurações selecionando **OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Configurações de recebimento-identificadores

![Propriedades do identificador para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Propriedade | Descrição |
|----------|-------------|
| **ISA1 (Qualificador de Autorização)** | O valor do qualificador de autorização que você deseja usar. O valor padrão é **00-nenhuma informação de autorização presente**. <p>**Observação**: se você selecionar outros valores, especifique um valor para a propriedade **ISA2** . |
| **ISA2** | O valor das informações de autorização a ser usado quando a propriedade **ISA1** não for **00-nenhuma informação de autorização presente**. Esse valor de propriedade deve ter um mínimo de um caractere alfanumérico e um máximo de 10. |
| **ISA3 (Qualificador de Segurança)** | O valor do qualificador de segurança que você deseja usar. O valor padrão é **00-nenhuma informação de segurança presente**. <p>**Observação**: se você selecionar outros valores, especifique um valor para a propriedade **ISA4** . |
| **ISA4** | O valor de informações de segurança a ser usado quando a propriedade **ISA3** não for **00-nenhuma informação de segurança presente**. Esse valor de propriedade deve ter um mínimo de um caractere alfanumérico e um máximo de 10. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Configurações de recebimento-confirmação

![Confirmação para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Propriedade | Descrição |
|----------|-------------|
| **TA1 esperado** | Retorna uma confirmação técnica (TA1) ao emissor de intercâmbio. |
| **FA esperado** | Retorna uma confirmação funcional (FA) ao emissor de intercâmbio. <p>Para a propriedade de **versão FA** , com base na versão do esquema, selecione as confirmações 997 ou 999. <p>Para habilitar a geração de loops AK2 em confirmações funcionais para conjuntos de transação aceitos, selecione **incluir loop AK2/IK2**. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Configurações de recebimento-esquemas

![Esquemas para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Para esta seção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) de sua [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) para cada tipo de transação (ST01) e aplicativo de remetente (GS02). O pipeline de recebimento de EDI desmonta a mensagem de entrada combinando os valores e o esquema que você definiu nesta seção com os valores de ST01 e GS02 na mensagem de entrada e com o esquema da mensagem de entrada. Depois de concluir cada linha, uma nova linha vazia será exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Versão** | A versão do X12 para o esquema |
| **Tipo de Transação (ST01)** | O tipo de transação |
| **Aplicativo do Remetente (GS02)** | O aplicativo do remetente |
| **Esquema** | O arquivo de esquema que você deseja usar |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Configurações de recebimento-envelopes

![Separadores a serem usados em conjuntos de transações para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Propriedade | Descrição |
|----------|-------------|
| **Uso de ISA11** | O separador a ser usado em um conjunto de transações: <p>- **Identificador padrão**: Use um ponto (.) para notação decimal, em vez da notação decimal do documento de entrada no pipeline de recebimento EDI. <p>- **Separador de repetição**: especifique o separador para ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetida. Por exemplo, geralmente o circunflexo (^) é usado como o separador de repetição. Para esquemas HIPAA, só é possível usar circunflexo (^). |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Configurações de recebimento-números de controle

![Tratamento de duplicatas de número de controle para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Propriedade | Descrição |
|----------|-------------|
| **Não permitir duplicatas de números de controle de intercâmbio** | Bloqueia o intercâmbio de duplicatas. Verifique o número de controle de intercâmbio (ISA13) para o número de controle de intercâmbio recebido. Se uma correspondência for detectada, o pipeline de recebimento EDI não processará o intercâmbio. <p><p>Para especificar o número de dias para executar a verificação, insira um valor para a propriedade **verificar se há ISA13 duplicados a cada (dias)** . |
| **Recusar duplicatas de Números de controle de grupo** | Bloquear intercâmbios com números de controle de grupo duplicados. |
| **Recusar duplicatas de Números de controle de Conjuntos de transações** | Bloquear intercâmbios com números de controle de conjunto de transação duplicados. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Configurações de recebimento – validações

![Validações para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

A linha **padrão** mostra as regras de validação que são usadas para um tipo de mensagem EDI. Se você quiser definir regras diferentes, selecione cada caixa em que você deseja que a regra seja definida como **true**. Depois de concluir cada linha, uma nova linha vazia será exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Tipo de mensagem** | O tipo de mensagem EDI |
| **Validação de EDI** | Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| **Validação Estendida** | Se o tipo de dados não for EDI, a validação estará no requisito de elemento de dados e na validação de comprimento de elemento de dados permitida (min ou Max). |
| **Permitir Zeros à Esquerda/Direita** | Mantenha qualquer zero ou caractere de espaço à esquerda ou à direita adicional. Não remova esses caracteres. |
| **Cortar Zeros à Esquerda/Direita** | Remova os zeros à esquerda ou à direita e os caracteres de espaço. |
| **Política de Separador à Direita** | Gera separadores à direita. <p>- **Não permitido**: proibir delimitadores e separadores à direita no intercâmbio de entrada. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio é declarado não válido. <p>- **Opcional**: aceite intercâmbios com ou sem delimitadores e separadores à direita. <p>- **Obrigatório**: o intercâmbio de entrada deve ter delimitadores e separadores à direita. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Configurações de recebimento-configurações internas

![Configurações internas para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Propriedade | Descrição |
|----------|-------------|
| **Converter o formato decimal implícito NN em um valor numérico de base 10** | Converta um número de EDI especificado com o formato "nn" em um valor numérico de base 10. |
| **Criar marcas XML vazias se forem permitidos separadores à direita** | Faça com que o emissor de intercâmbio inclua marcas XML vazias para separadores à direita. |
| **Dividir Intercâmbio como conjuntos de transação – suspender conjuntos de transação com erro** | Analise cada conjunto de transações que está em um intercâmbio em um documento XML separado aplicando o envelope apropriado ao conjunto de transações. Suspenda apenas as transações em que a validação falha. |
| **Dividir Intercâmbio como conjuntos de transação – suspender intercâmbio com erro** | Analise cada conjunto de transações que está em um intercâmbio em um documento XML separado aplicando o envelope apropriado. Suspende o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação. |
| **Preservar Intercâmbio – suspender conjuntos transação com erro** | Deixe o intercâmbio intacto e crie um documento XML para todo o intercâmbio em lote. Suspenda apenas os conjuntos de transações com falha na validação, mas continue processando todos os outros conjuntos de transações. |
| **Preservar Intercâmbio – suspender intercâmbio com erro** |Mantém o intercâmbio intacto, cria um documento XML para o intercâmbio em lote inteiro. Suspende o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Configurações de Envio

Depois de definir as propriedades do contrato, você pode configurar como este contrato identifica e manipula as mensagens de saída enviadas ao seu parceiro por meio deste contrato.

1. Em **Adicionar**, selecione **Configurações de Envio**.

1. Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. Para obter as descrições da propriedade, confira as tabelas nesta seção.

   As **configurações de envio** são organizadas nessas seções:

   * [Identificadores](#outbound-identifiers)
   * [Confirmação](#outbound-acknowledgement)
   * [Esquemas](#outbound-schemas)
   * [Envelopes](#outbound-envelopes)
   * [Número de versão de controle](#outbound-control-version-number)
   * [Números de Controle](#outbound-control-numbers)
   * [Conjuntos de Caracteres e Separadores](#outbound-character-sets-separators)
   * [Validação](#outbound-validation)

1. Quando terminar, certifique-se de salvar suas configurações selecionando **OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Configurações de envio-identificadores

![Propriedades do identificador para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Propriedade | Descrição |
|----------|-------------|
| **ISA1 (Qualificador de Autorização)** | O valor do qualificador de autorização que você deseja usar. O valor padrão é **00-nenhuma informação de autorização presente**. <p>**Observação**: se você selecionar outros valores, especifique um valor para a propriedade **ISA2** . |
| **ISA2** | O valor das informações de autorização a ser usado quando a propriedade **ISA1** não for **00-nenhuma informação de autorização presente**. Esse valor de propriedade deve ter um mínimo de um caractere alfanumérico e um máximo de 10. |
| **ISA3 (Qualificador de Segurança)** | O valor do qualificador de segurança que você deseja usar. O valor padrão é **00-nenhuma informação de segurança presente**. <p>**Observação**: se você selecionar outros valores, especifique um valor para a propriedade **ISA4** . |
| **ISA4** | O valor de informações de segurança a ser usado quando a propriedade **ISA3** não for **00-nenhuma informação de segurança presente**. Esse valor de propriedade deve ter um mínimo de um caractere alfanumérico e um máximo de 10. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Configurações de envio-confirmação

![Propriedades de confirmação para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Propriedade | Descrição |
|----------|-------------|
| **TA1 esperado** | Retorna uma confirmação técnica (TA1) ao emissor de intercâmbio. <p>Essa configuração especifica que o parceiro de host, que está enviando a mensagem, solicita uma confirmação do parceiro convidado no contrato. Essas confirmações são esperadas pelo parceiro host com base nas configurações de recebimento do contrato. |
| **FA esperado** | Retorna uma confirmação funcional (FA) ao emissor de intercâmbio. Para a propriedade de **versão FA** , com base na versão do esquema, selecione as confirmações 997 ou 999. <p>Essa configuração especifica que o parceiro de host, que está enviando a mensagem, solicita uma confirmação do parceiro convidado no contrato. Essas confirmações são esperadas pelo parceiro host com base nas configurações de recebimento do contrato. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Configurações de envio-esquemas

![Esquemas para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Para esta seção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) para cada tipo de transação (ST01). Depois de concluir cada linha, uma nova linha vazia será exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Versão** | A versão do X12 para o esquema |
| **Tipo de Transação (ST01)** | O tipo de transação para o esquema |
| **Esquema** | O arquivo de esquema que você deseja usar. Se você selecionar o esquema primeiro, a versão e o tipo de transação serão definidos automaticamente. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Configurações de envio-envelopes

![Separadores em um conjunto de transações a ser usado para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Propriedade | Descrição |
|----------|-------------|
| **Uso de ISA11** | O separador a ser usado em um conjunto de transações: <p>- **Identificador padrão**: Use um ponto (.) para notação decimal, em vez da notação decimal do documento de saída no pipeline de envio EDI. <p>- **Separador de repetição**: especifique o separador para ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetida. Por exemplo, geralmente o circunflexo (^) é usado como o separador de repetição. Para esquemas HIPAA, só é possível usar circunflexo (^). |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Configurações de envio – número de versão de controle

![Número de versão de controle para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Para esta seção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) para cada intercâmbio. Depois de concluir cada linha, uma nova linha vazia será exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Número de Versão de Controle (ISA12)** | A versão do padrão X12 |
| **Indicador de Uso (ISA15)** | O contexto de um intercâmbio, que é dados de **teste** , dados de **informações** ou dados de **produção** |
| **Esquema** | O esquema a ser usado para gerar os segmentos GS e ST para um intercâmbio X12 que é enviado para o pipeline de envio EDI. |
| **GS1** | Opcional, selecione o código funcional. |
| **GS2** | Opcional, especifique o remetente do aplicativo. |
| **GS3** | Opcional, especifique o receptor do aplicativo. |
| **GS4** | Opcional, selecione **CCYYMMDD** ou **YYMMDD**. |
| **GS5** | Opcional, selecione **hhmm**, **HHMMSS**ou **HHMMSSdd**. |
| **GS7** | Opcional, selecione um valor para a agência responsável. |
| **GS8** | Opcional, especifique a versão do documento de esquema. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Configurações de envio-números de controle

![Números de controle para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Propriedade | Descrição |
|----------|-------------|
| **Número de Controle de Intercâmbio (ISA13)** | O intervalo de valores para o número de controle de intercâmbio, que pode ter um mínimo de valor 1 e um valor máximo de 999999999 |
| **Número de Controle de Grupo (GS06)** | O intervalo de valores para o número de controle de grupo, que pode ter um valor mínimo de 1 e um valor máximo de 999999999 |
| **Número de Controle de Conjunto de Transações (ST02)** | O intervalo de valores para o número de controle do conjunto de transação, que pode ter um valor mínimo de 1 e um valor máximo de 999999999 <p>- **Prefixo**: opcional, um valor alfanumérico <br>- **Sufixo**: opcional, um valor alfanumérico |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Configurações de envio – conjuntos de caracteres e separadores

![Delimitadores para tipos de mensagem em mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

A linha **padrão** mostra o conjunto de caracteres usado como delimitadores para um esquema de mensagem. Se você não quiser usar o conjunto de caracteres **padrão** , poderá inserir um conjunto diferente de delimitadores para cada tipo de mensagem. Depois de concluir cada linha, uma nova linha vazia será exibida automaticamente.

> [!TIP]
> Para fornecer valores de caractere especial, edite o contrato como JSON e forneça o valor ASCII para o caractere especial.

| Propriedade | Descrição |
|----------|-------------|
| **Conjunto de Caracteres a ser usado** | O conjunto de caracteres X12, que é **Basic**, **Extended**ou **UTF8**. |
| **Esquema** | O esquema que você deseja usar. Depois de selecionar o esquema, selecione o conjunto de caracteres que você deseja usar, com base nas descrições do separador abaixo. |
| **Tipo de entrada** | O tipo de entrada para o conjunto de caracteres |
| **Separador de componente** | Um único caractere que separa elementos de dados compostos |
| **Separador de elemento de dados** | Um único caractere que separa elementos de dados simples em dados compostos |
| **separador de caracteres de substituição** | Um caractere de substituição que substitui todos os caracteres separadores nos dados de carga ao gerar a mensagem X12 de saída |
| **Terminador de segmento** | Um único caractere que indica o final de um segmento EDI |
| **Sufixo** | O caractere a ser usado com o identificador de segmento. Se você especificar um sufixo, o elemento de dados de terminador de segmento poderá ficar vazio. Se o terminador de segmento for deixado vazio, será necessário designar um sufixo. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Configurações de envio-validação

![Propriedades de validação para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

A linha **padrão** mostra as regras de validação que são usadas para um tipo de mensagem EDI. Se você quiser definir regras diferentes, selecione cada caixa em que você deseja que a regra seja definida como **true**. Depois de concluir cada linha, uma nova linha vazia será exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Tipo de mensagem** | O tipo de mensagem EDI |
| **Validação de EDI** | Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| **Validação Estendida** | Se o tipo de dados não for EDI, a validação estará no requisito de elemento de dados e na validação de comprimento de elemento de dados permitida (min ou Max). |
| **Permitir Zeros à Esquerda/Direita** | Mantenha qualquer zero ou caractere de espaço à esquerda ou à direita adicional. Não remova esses caracteres. |
| **Cortar Zeros à Esquerda/Direita** | Remova os zeros à esquerda ou à direita e os caracteres de espaço. |
| **Política de Separador à Direita** | Gera separadores à direita. <p>- **Não permitido**: proibir delimitadores e separadores à direita no intercâmbio de saída. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio é declarado não válido. <p>- **Opcional**: enviar intercâmbios com ou sem delimitadores e separadores à direita. <p>- **Obrigatório**: o intercâmbio de saída deve ter delimitadores e separadores à direita. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>Tipos de mensagens e esquemas da HIPAA

Ao trabalhar com esquemas HIPAA e os tipos de mensagem 277 ou 837, você precisa executar algumas etapas adicionais. Os [números de versão do documento (GS8)](#outbound-control-version-number) para esses tipos de mensagem têm mais de 9 caracteres, por exemplo, "005010X222A1". Além disso, alguns números de versão de documento são mapeados para tipos de mensagem Variant. Se você não fizer referência ao tipo de mensagem correto em seu esquema e em seu contrato, você receberá esta mensagem de erro:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Esta tabela lista os tipos de mensagem afetados, quaisquer variantes e os números de versão do documento que são mapeados para esses tipos de mensagem:

| Tipo de mensagem ou variante |  Descrição | Número de versão do documento (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Notificação de status de informações de assistência médica | 005010X212 |
| 837_I | Declaração de assistência médica dental | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Institucional da declaração de assistência médica | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Profissional de declaração de assistência médica | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Você também precisa desabilitar a validação EDI ao usar esses números de versão do documento, pois eles resultam em um erro de que o comprimento do caractere é inválido.

Para especificar esses números de versão de documento e tipos de mensagem, siga estas etapas:

1. No esquema do HIPAA, substitua o tipo de mensagem atual pelo tipo de mensagem variante do número de versão do documento que você deseja usar.

   Por exemplo, suponha que você deseja usar o número `005010X222A1` de versão do `837` documento com o tipo de mensagem. Em seu esquema, substitua cada `"X12_00501_837"` valor pelo `"X12_00501_837_P"` valor em vez disso.

   Para atualizar seu esquema, siga estas etapas:

   1. Na portal do Azure, acesse sua conta de integração. Localize e baixe seu esquema. Substitua o tipo de mensagem e renomeie o arquivo de esquema e carregue seu esquema revisado para sua conta de integração. Para obter mais informações, consulte [Editar esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. Nas configurações de mensagem do seu contrato, selecione o esquema revisado.

1. No objeto do `schemaReferences` seu contrato, adicione outra entrada que especifique o tipo de mensagem variante que corresponde ao número de versão do documento.

   Por exemplo, suponha que você queira usar o número `005010X222A1` de versão do `837` documento para o tipo de mensagem. Seu contrato tem uma `schemaReferences` seção com essas propriedades e valores:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   Nesta `schemaReferences` seção, adicione outra entrada que tenha estes valores:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Quando terminar, sua `schemaReferences` seção terá esta aparência:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. Nas configurações de mensagem do seu contrato, desabilite a validação EDI desmarcando a caixa de seleção **validação EDI** para cada tipo de mensagem ou para todos os tipos de mensagem se você estiver usando os valores **padrão** .

   ![Desabilitar a validação para todos os tipos de mensagem ou cada tipo de mensagem](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos adicionais sobre esse conector, como ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/x12/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem B2B para o ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores para aplicativos lógicos](../connectors/apis-list.md)