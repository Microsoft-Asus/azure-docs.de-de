---
title: Referenztabelle für alle Azure Security Center-Empfehlungen
description: In diesem Artikel sind die Azure Security Center-Sicherheitsempfehlungen aufgeführt, die Ihnen beim Schützen Ihrer Ressourcen helfen.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 01/12/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 11d4830908b4e86da12cd5e40cc26b1c1b1aecbd
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133044"
---
# <a name="security-recommendations---a-reference-guide"></a>Sicherheitsempfehlungen: Referenzhandbuch

In diesem Artikel sind die Empfehlungen aufgeführt, die in Azure Security Center angezeigt werden können. Welche Empfehlungen in Ihrer Umgebung angezeigt werden, hängt von den geschützten Ressourcen und Ihrer angepassten Konfiguration ab.

Security Center-Empfehlungen beruhen auf Best Practices. Zum Teil orientieren sie sich am **Azure-Sicherheitsvergleichstest**. Dabei handelt es sich um von Microsoft erstellte Azure-spezifische Richtlinien zu den Best Practices für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren.
[Weitere Informationen zum Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md)

Informationen zum Umgang mit diesen Empfehlungen finden Sie unter [Umsetzen von Empfehlungen in Azure Security Center](security-center-remediate-recommendations.md).

Ihre Sicherheitsbewertung basiert auf der Anzahl der Security Center-Empfehlungen, die Sie umgesetzt haben. Sehen Sie sich den Schweregrad jeder einzelnen Empfehlung und deren mögliche Auswirkung auf Ihre Sicherheitsbewertung an, um zu entscheiden, welche Empfehlungen zuerst umgesetzt werden sollten.

> [!TIP]
> Wenn es in der Beschreibung einer Empfehlung heißt: „Keine zugehörige Richtlinie“, so liegt das normalerweise daran, dass diese Empfehlung von einer anderen Empfehlung und _deren_ Richtlinie abhängig ist. Die Empfehlung „Endpoint Protection-Integritätsfehler sollten behoben werden...“ stützt sich beispielsweise auf die Empfehlung, die prüft, ob _überhaupt_ eine Endpoint Protection-Lösung installiert ist. („Endpoint Protection-Lösung sollte installiert werden...“). Die zugrunde liegende Empfehlung _verfügt_ über eine Richtlinie.
> Die Richtlinien nur auf die grundlegende Empfehlung zu beschränken, vereinfacht die Richtlinienverwaltung.

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Computeempfehlungen

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Datenempfehlungen

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess-Empfehlungen

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Netzwerkempfehlungen

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Veraltete Empfehlungen

|Empfehlung|Beschreibung und zugehörige Richtlinie|severity|Schnelle Behebung aktiviert?([Weitere Informationen](security-center-remediate-recommendations.md#quick-fix-remediation))|Ressourcentyp|
|----|----|----|----|----|
|**Der Zugriff auf App Services sollte eingeschränkt werden**|Schränken Sie den Zugriff auf Ihre App Services ein, indem Sie die Netzwerkkonfiguration ändern, um eingehenden Datenverkehr aus Bereichen abzulehnen, die zu weit gefasst sind.<br>(Zugehörige Richtlinie: [Vorschau]: Zugriff auf App Services muss eingeschränkt sein)|Hoch|N|App Service|
|**Die Regeln für Webanwendungen in IaaS-Netzwerksicherheitsgruppen sollten verstärkt werden**|Härten Sie die Netzwerksicherheitsgruppe (NSG) Ihrer virtuellen Computer, auf denen Webanwendungen ausgeführt werden, mit NSG-Regeln, die in Bezug auf Webanwendungsports zu tolerant sind.<br>(Zugehörige Richtlinie: Für Webanwendungen in IaaS müssen die NSG-Regeln verstärkt werden)|Hoch|N|Virtueller Computer|
|**Podsicherheitsrichtlinien sollten definiert werden, um den Angriffsvektor zu reduzieren, indem nicht erforderliche Anwendungsberechtigungen entfernt werden (Vorschau)**|Definieren Sie Podsicherheitsrichtlinien, um den Angriffsvektor zu reduzieren, indem nicht erforderliche Anwendungsberechtigungen entfernt werden. Wir empfehlen Ihnen die Konfiguration von Podsicherheitsrichtlinien durchzuführen, damit Pods nur auf Ressourcen zugreifen können, für die sie über eine Zugriffsberechtigung verfügen.<br>(Zugehörige Richtlinie: [Vorschau]: Podsicherheitsrichtlinien für Kubernetes Service definieren)|Medium|N|Computeressourcen (Container)|
|**Für bessere Einblicke in Ihre IoT-Geräte das Sicherheitsmodul „Azure Security Center für IoT“ installieren**|Installieren Sie für bessere Einblicke in Ihre IoT-Geräte das Sicherheitsmodul „Azure Security Center für IoT“.|Niedrig|N|IoT-Gerät|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Empfehlungen finden Sie in den folgenden Artikeln:

- [Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md)
- [Schützen Ihres Netzwerks in Azure Security Center](security-center-network-recommendations.md)
