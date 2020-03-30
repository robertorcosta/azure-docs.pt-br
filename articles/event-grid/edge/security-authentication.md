---
title: Segurança e autenticação - Azure Event Grid IoT Edge | Microsoft Docs
description: Segurança e autenticação em Event Grid em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844506"
---
# <a name="security-and-authentication"></a>Segurança e autenticação

Segurança e autenticação é um conceito avançado e requer familiaridade com o básico do Event Grid primeiro. Comece [aqui](concepts.md) se você for novo no Event Grid em IoT Edge. O módulo Event Grid baseia-se na infra-estrutura de segurança existente no IoT Edge. Consulte [esta documentação](../../iot-edge/security.md) para obter detalhes e configuração.

As seções a seguir descrevem detalhadamente como essas configurações são protegidas e autenticadas:

* Configuração TLS
* Autenticação de cliente de entrada
* Autenticação do servidor de saída
* Autenticação do cliente de saída

>[!IMPORTANT]
>A alavancagem de segurança e autenticação do módulo Event Grid é a infra-estrutura existente disponível no IoT Edge. A suposição é que o subsistema IoT Edge é seguro.

>[!IMPORTANT]
>A configuração do Event Grid é **segura por padrão**. As subseções a seguir explicam todas as opções e possíveis valores que você pode usar para substituir aspectos da autenticação. Entenda o impacto antes de fazer qualquer alteração. Para que quaisquer alterações surtam efeito, o módulo Event Grid precisará ser reimplantado.

## <a name="tls-configuration-aka-server-authentication"></a>Configuração TLS (também conhecida como autenticação de servidor)

O módulo Event Grid hospeda pontos finais HTTP e HTTPS. Cada módulo IoT Edge é atribuído um certificado de servidor pelo daemon de segurança do IoT Edge. Usamos o certificado do servidor para garantir o ponto final. No vencimento, o módulo é atualizado automaticamente com um novo certificado do daemon de segurança IoT Edge.

Por padrão, somente a comunicação HTTPS é permitida. Você pode substituir esse comportamento através **de inbound__serverAuth__tlsPolicy** configuração. A tabela a seguir captura os possíveis valores desta propriedade.

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| Rigoroso | Padrão. Habilita somente HTTPS
| habilitado | Habilita tanto http quanto HTTPS
| Desabilitado | Habilita somente HTTP

## <a name="inbound-client-authentication"></a>Autenticação de cliente de entrada

Os clientes são entidades que fazem operações de gestão e/ou tempo de execução. Os clientes podem ser outros módulos de IoT Edge, aplicativos não IoT.

O módulo Event Grid suporta dois tipos de autenticação do cliente:

* Baseado em chave SAS (SAS) baseado em chave de acesso compartilhado
* com base em certificados

Por padrão, o módulo Event Grid é configurado para aceitar apenas autenticação baseada em certificado. Na inicialização, o módulo Event Grid recupera o "TrustBundle" do daemon de segurança do IoT Edge e o usa para validar qualquer certificado do cliente. Os certificados do cliente que não se `UnAuthorized`resolverem para esta cadeia serão rejeitados com .

### <a name="certificate-based-client-authentication"></a>Autenticação de cliente baseada em certificados

A autenticação baseada em certificados está ativada por padrão. Você pode optar por desativar a autenticação baseada em certificados através do **inbound__clientAuth__clientCert__enabled**de propriedade . A tabela a seguir captura possíveis valores.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Padrão. Requer todas as solicitações no módulo Event Grid para apresentar um certificado de cliente. Além disso, você precisará configurar **inbound__clientAuth__clientCert__source**.
| false | Não force um cliente a apresentar o certificado.

A tabela a seguir captura possíveis valores para **inbound__clientAuth__clientCert__source**

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| IoT Edge | Padrão. Usa o Pacote de Confiança do IoT Edge para validar todos os certificados do cliente.

Se um cliente apresentar um auto-assinado, por padrão, o módulo Event Grid rejeitará tais solicitações. Você pode optar por permitir certificados de cliente auto-assinados através **de inbound__clientAuth__clientCert__allowUnknownCA** propriedade. A tabela a seguir captura possíveis valores.

| Valores possíveis | Descrição |
| ----------------  | ------------|
| true | Padrão. Permite que certificados auto-assinados sejam apresentados com sucesso.
| false | Falhará as solicitações se os certificados auto-assinados forem apresentados.

>[!IMPORTANT]
>Em cenários de produção, você pode querer definir **inbound__clientAuth__clientCert__allowUnknownCA** para **falso**.

### <a name="sas-key-based-client-authentication"></a>Autenticação de cliente baseada em chaves SAS

Além da autenticação baseada em certificados, o módulo Event Grid também pode fazer autenticação baseada em chaves SAS. A chave SAS é como um segredo configurado no módulo Event Grid que ele deve usar para validar todas as chamadas recebidas. Os clientes precisam especificar o segredo no cabeçalho HTTP 'aeg-sas-key'. O pedido será `UnAuthorized` rejeitado se não corresponder.

A configuração para controlar a autenticação baseada em chaves SAS é **inbound__clientAuth__sasKeys__enabled**.

| Valores possíveis | Descrição  |
| ----------------  | ------------ |
| true | Permite a autenticação baseada em chaves SAS. Requer **inbound__clientAuth__sasKeys__key1** ou **inbound__clientAuth__sasKeys__key2**
| false | Padrão. A autenticação baseada em sas-key está desativada.

 **inbound__clientAuth__sasKeys__key1** e **inbound__clientAuth__sasKeys__key2** são as chaves que você configura o módulo Event Grid para verificar contra as solicitações recebidas. Pelo menos uma das chaves precisa ser configurada. O cliente que fazer a solicitação precisará apresentar a chave como parte do cabeçalho de solicitação '**aeg-sas-key**'. Se ambas as teclas estiverem configuradas, o cliente pode apresentar uma das chaves.

> [!NOTE]
>Você pode configurar ambos os métodos de autenticação. Nesse caso, a chave SAS é verificada primeiro e somente se isso falhar, a autenticação baseada em certificado é realizada. Para que um pedido seja bem sucedido, apenas um dos métodos de autenticação precisa ser bem sucedido.

## <a name="outbound-client-authentication"></a>Autenticação do cliente de saída

Cliente em contexto de saída refere-se ao módulo Event Grid. A operação que está sendo feita é a entrega de eventos aos assinantes. Os módulos de assinatura são considerados como o servidor.

Todos os módulos IoT Edge são atribuídos um Certificado de Identidade pelo daemon de segurança do IoT Edge. Usamos o certificado de identidade para chamadas de saída. No vencimento, o módulo é atualizado automaticamente com um novo certificado do daemon de segurança IoT Edge.

A configuração para controlar a autenticação do cliente de saída é **outbound__clientAuth__clientCert__enabled**.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Padrão. Requer todas as solicitações de saída do módulo Event Grid para apresentar um certificado. Precisa configurar **outbound__clientAuth__clientCert__source**.
| false | Não exija que o módulo Event Grid apresente seu certificado.

A configuração que controla a fonte do certificado é **outbound__clientAuth__clientCert__source**.

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| IoT Edge | Padrão. Usa o certificado de identidade do módulo configurado pelo daemon de segurança IoT Edge.

### <a name="outbound-server-authentication"></a>Autenticação do servidor de saída

Um dos tipos de destino para um assinante da Event Grid é o "Webhook". Por padrão, apenas os pontos finais HTTPS são aceitos para esses assinantes.

A configuração para controlar a política de destino do webhook **outbound__webhook__httpsOnly**.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Padrão. Permite apenas assinantes com ponto final HTTPS.
| false | Permite assinantes com ponto final HTTP ou HTTPS.

Por padrão, o módulo Event Grid validará o certificado de servidor do assinante. Você pode pular a validação substituindo **outbound__webhook__skipServerCertValidation**. Os valores possíveis são:

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Não valide o certificado de servidor do assinante.
| false | Padrão. Valide o certificado de servidor do assinante.

Se o certificado do assinante for auto-assinado, então, por padrão, o módulo Event Grid rejeitará tais assinantes. Para permitir o certificado auto-assinado, você pode substituir **outbound__webhook__allowUnknownCA**. A tabela a seguir captura os possíveis valores.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Padrão. Permite que certificados auto-assinados sejam apresentados com sucesso.
| false | Falhará as solicitações se os certificados auto-assinados forem apresentados.

>[!IMPORTANT]
>Em cenários de produção, você vai querer definir **outbound__webhook__allowUnknownCA** para **falso**.

> [!NOTE]
>O ambiente IoT Edge gera certificados auto-assinados. A recomendação é gerar certificados emitidos por CAs autorizados para cargas de trabalho de produção e definir **permitir propriedadeUnknownCA** tanto na entrada quanto na saída como **falsa**.

## <a name="summary"></a>Resumo

Um módulo Event Grid é **seguro por padrão**. Recomendamos manter esses padrões para suas implantações de produção.

A seguir estão os princípios orientadores a serem usados durante a configuração:

* Permitir apenas solicitações HTTPS no módulo.
* Permitir apenas a autenticação de cliente baseada em certificados. Permita apenas os certificados que são emitidos por CAs conhecidos. Não permita certificados auto-assinados.
* Despermitir a autenticação do cliente baseada no SASKey.
* Sempre apresente o certificado de identidade do módulo Event Grid em chamadas de saída.
* Permita apenas assinantes HTTPS para tipos de destino webhook.
* Valide sempre o certificado de servidor do assinante para os tipos de destino do Webhook. Permitir apenas certificados emitidos por CAs conhecidos. Não permita certificados auto-assinados.

Por padrão, o módulo Event Grid é implantado com a seguinte configuração:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
