---
title: Autoridades de certificação permitidas para o Azure front door Standard/Premium (visualização)
description: Este artigo lista todas as autoridades de certificação permitidas quando você cria seu próprio certificado.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098543"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Autoridades de certificação permitidas para o Azure front door Standard/Premium (visualização)

Quando você habilita o recurso HTTPS usando seu próprio certificado para um domínio personalizado Standard/Premium da porta de front-end do Azure. Você precisa de uma autoridade de certificação (CA) permitida para criar seu certificado TLS/SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

As ACs a seguir são permitidas quando você criar seu próprio certificado:

- AddTrust External CA Root
- AC de raiz AlphaSSL
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
- Atos TrustedRoot 2011
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert Global Root G2
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 CA do Servidor de Validação Estendido
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- DST Raiz CA X3
- D-TRUST Classe de Raiz 3 CA 2 2009
- Em todos os lugares criptografia CA DE TLS DV
- Autoridade de certificação raiz da Entrust
- Autoridade de certificação raiz da Entrust - G2
- Autoridade de certificação raiz da Entrust (2048)
- GeoTrust Global CA
- GeoTrust Primary Certification Authority
- GeoTrust Primary Certification Authority – G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign Extended Validation CA – SHA256 – G2
- GlobalSign Organization Validation CA – G2
- GlobalSign Root CA
- Go Daddy Root Certificate Authority – G2
- Go Daddy Secure Certificate Authority - G2
- Vamos criptografar Authority X3
- Microssegundos Szigno CA raiz 2009
- Raiz QuoVadis CA2 G3
- RapidSSL RSA CA 2018
- RootCA1 de comunicação de segurança
- RootCA2 de comunicação de segurança
- RootCA3 de comunicação de segurança
- Symantec Class 3 EV SSL CA – G3
- Symantec Class 3 Secure Server CA – G4
- Symantec Enterprise Mobile Root for Microsoft
- Thawte Primary Root CA
- Thawte Primary Root CA – G2
- Thawte Primary Root CA – G3
- Thawte RSA CA 2018
- Thawte Timestamping CA
- TrustAsia TLS RSA CA
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority – G5
- VeriSign International Server CA – Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority
