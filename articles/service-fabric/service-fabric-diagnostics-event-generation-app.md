---
title: Überwachung auf Azure Service Fabric-Anwendungsebene
description: In diesem Artikel erfahren Sie mehr über Ereignisse und Protokolle auf Anwendungs- und Dienstebene, die zum Überwachen und Diagnostizieren von Azure Service Fabric-Clustern verwendet werden.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: e1871df962a26def8c12000f8b8bc0cf31bae9a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247658"
---
# <a name="application-logging"></a>Anwendungsprotokollierung

Die Instrumentierung Ihres Codes liefert nicht nur Erkenntnisse über Ihre Benutzer. Sie ist auch die einzige Möglichkeit, um Probleme in Ihrer Anwendung zu erkennen und zu diagnostizieren. Es ist zwar technisch möglich, einen Debugger mit einem Produktionsdienst zu verbinden, aber dies ist keine gängige Vorgehensweise. Es ist also wichtig, dass Sie über ausführliche Instrumentierungsdaten verfügen.

Bei einigen Produkten wird Ihr Code automatisch instrumentiert. Diese Lösungen können zwar auch funktionieren, eine manuelle Instrumentierung muss jedoch fast immer speziell auf Ihre Geschäftslogik zugeschnitten sein. Letztlich benötigen Sie ausreichend Informationen für das genaue Debuggen der Anwendung. Service Fabric-Anwendungen können mit einem beliebigen Protokollierungsframework instrumentiert werden. In diesem Dokument werden verschiedene Verfahren für die Instrumentierung Ihres Codes sowie geeignete Anwendungsfälle beschrieben. 

Beispiele zur Verwendung dieser Vorschläge finden Sie unter [Hinzufügen von Protokollierung zur Service Fabric-Anwendung](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Application Insights SDK

Die umfassende Integration von Application Insights in Service Fabric ist vorkonfiguriert. Benutzer können die KI-NuGet-Pakete von Service Fabric hinzufügen und Daten und Protokolle empfangen, die so erstellt und erfasst wurden, dass sie im Azure-Portal angezeigt werden können. Außerdem wird Benutzern das Hinzufügen ihrer eigenen Telemetriedaten empfohlen, um ihre Anwendungen zu diagnostizieren und zu debuggen, und um nachzuverfolgen, welche Dienste und Teile ihrer Anwendung am häufigsten verwendet werden. Die [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet)-Klasse im SDK bietet viele Methoden zum Nachverfolgen von Telemetriedaten in Ihren Anwendungen. Sehen Sie sich in unserem Tutorial [Überwachen und Diagnostizieren einer ASP.NET Core-Anwendung in Service Fabric](service-fabric-tutorial-monitoring-aspnet.md) ein Beispiel zum Instrumentieren und Hinzufügen von Application Insights zu Ihrer Anwendung an.

## <a name="eventsource"></a>EventSource

Wenn Sie eine Service Fabric-Lösung aus einer Vorlage in Visual Studio erstellen, wird eine von **EventSource** abgeleitete Klasse (**ServiceEventSource** oder **ActorEventSource**) generiert. Es wird eine Vorlage erstellt, in der Sie Ereignisse für Ihre Anwendung oder Ihren Dienst hinzufügen können. Der **EventSource**-Name **muss** eindeutig sein. Er sollte auf der Standardvorlage „MyCompany-&lt;Projektmappe&gt;-&lt;Projekt&gt;“ basieren und entsprechend umbenannt werden. Mehrere **EventSource**-Definitionen mit dem gleichen Namen führen zur Laufzeit zu Problemen. Jedes definierte Ereignis muss über einen eindeutigen Bezeichner verfügen. Wenn ein Bezeichner nicht eindeutig ist, tritt ein Laufzeitfehler auf. In einigen Organisationen werden Wertebereiche vorab Bezeichnern zugewiesen, um Konflikte zwischen separaten Entwicklungsteams zu vermeiden. Weitere Informationen finden Sie im [Blog von Vance](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) oder in der [MSDN-Dokumentation](/previous-versions/msp-n-p/dn774985(v=pandp.20)).

## <a name="aspnet-core-logging"></a>ASP.NET Core-Protokollierung

Es ist wichtig, dass Sie sorgfältig planen, wie Sie Ihren Code instrumentieren. Mit dem richtigen Instrumentierungsplan können Sie ggf. verhindern, dass Ihre Codebasis destabilisiert wird und eine erneute Instrumentierung des Codes erforderlich wird. Zur Reduzierung des Risikos können Sie eine Instrumentierungsbibliothek wie [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/) wählen, die Teil von Microsoft ASP.NET Core ist. ASP.NET Core verfügt über eine [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)-Schnittstelle, die Sie zusammen mit dem Anbieter Ihrer Wahl nutzen können, während gleichzeitig die Auswirkungen auf den vorhandenen Code minimiert werden. Sie können den Code in ASP.NET Core unter Windows und Linux und im vollständigen .NET Framework verwenden, damit Ihr Instrumentierungscode standardisiert ist.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie den Protokollanbieter zum Instrumentieren Ihrer Anwendungen und Dienste ausgewählt haben, müssen Ihre Protokolle und Ereignisse aggregiert werden, bevor sie an eine Analyseplattform gesendet werden können. Informieren Sie sich über [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) und [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md), um einige der empfohlenen Optionen für Azure Monitor besser zu verstehen.
