---
title: Problemen met VM-certificering voor Azure Marketplace oplossen
description: Dit artikel bevat informatie over het oplossen van problemen met het testen en certificeren van VM-installatie kopieën voor Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: f065b1bc98eab86542ecff73e1471e4d90cd4182
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339530"
---
# <a name="vm-certification-troubleshooting"></a>Problemen oplossen met VM-certificering

Wanneer u de installatie kopie van de virtuele machine (VM) publiceert naar Azure Marketplace, valideert het Azure-team deze om te zorgen voor de opstart baarheid, beveiliging en Azure-compatibiliteit. Als een van de tests van hoge kwaliteit mislukt, mislukt de publicatie en wordt er een fout bericht weer gegeven waarin het probleem wordt beschreven.

In dit artikel worden veelvoorkomende fout berichten voor het publiceren van VM-installatie kopieën en gerelateerde oplossingen uitgelegd.

> [!NOTE]
> Als u vragen of feedback voor verbetering hebt, neemt u contact op met de [ondersteuning](https://aka.ms/marketplacepublishersupport)van partner Center.

## <a name="approved-base-image"></a>Goedgekeurde basis installatie kopie

Wanneer u een aanvraag indient om uw installatie kopie opnieuw te publiceren met updates, kan de test voor de verificatie van het onderdeel nummer mislukken. Als dit mislukt, wordt uw installatie kopie niet goedgekeurd.

Deze fout treedt op wanneer u een basis installatie kopie gebruikt die deel uitmaakt van een andere uitgever en u de installatie kopie hebt bijgewerkt. In deze situatie is het niet toegestaan om uw installatie kopie te publiceren.

U kunt dit probleem oplossen door de installatie kopie op te halen uit Azure Marketplace en er wijzigingen in aan te brengen. Raadpleeg voor meer informatie de volgende artikelen:

- [Linux-installatie kopieën](../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-installatie kopieën](azure-vm-create-using-approved-base.md)

> [!Note]
> Als u een Linux-basis installatie kopie gebruikt die niet afkomstig is van Azure Marketplace, kunt u de eerste partitie met 2048 KB verrekenen. Hierdoor kan de niet-opgemaakte ruimte worden gebruikt voor het toevoegen van nieuwe facturerings gegevens. Hierdoor kan Azure verdergaan met het publiceren van uw VM naar Azure Marketplace.  

> [!Note]
> Als u een Linux-basis installatie kopie gebruikt die niet is gemaakt op Marketplace, kunt u de eerste partitie met 2048 KB verrekenen. Op deze manier kan de niet-opgemaakte ruimte worden gebruikt voor het toevoegen van nieuwe facturerings gegevens en kan Azure door gaan met het publiceren van uw VM naar Marketplace.  

## <a name="vm-extension-failure"></a>VM-extensie fout

Controleer of de installatie kopie VM-extensies ondersteunt.

Ga als volgt te werk om VM-extensies in te scha kelen:

1. Selecteer uw virtuele Linux-machine.
1. Ga naar **instellingen voor diagnostische gegevens**.
1. Schakel basis matrices in door het **opslag account** bij te werken.
1. Selecteer **Opslaan**.

   ![Bewaking op gastniveau inschakelen](./media/create-vm/vm-certification-issues-solutions-1.png)

Ga als volgt te werk om te controleren of de VM-extensies correct zijn geactiveerd:

1. Selecteer op de VM het tabblad **VM-extensies** en controleer de status van de **Linux Diagnostics-extensie**.
1. 
    * Als de status is *ingericht* , wordt de test case door gegeven.  
    * Als de status van de *inrichting mislukt* is, is het mogelijk dat de uitbrei dingen van de aanvraag zijn mislukt en dat u de gereduceerde vlag moet instellen.

      ![Scherm opname die laat zien dat het inrichten is geslaagd](./media/create-vm/vm-certification-issues-solutions-2.png)

      Als de extensie van de virtuele machine mislukt, raadpleegt u de [Diagnostische Linux-extensie gebruiken om metrische gegevens en logboeken te controleren](../virtual-machines/extensions/diagnostics-linux.md) . Als u niet wilt dat de VM-extensie wordt ingeschakeld, neemt u contact op met het ondersteunings team en vraagt u deze uit te scha kelen.

## <a name="vm-provisioning-issue"></a>Probleem met het inrichten van de VM

Controleer of u het VM-inrichtings proces zorgvuldig hebt gevolgd voordat u uw aanbieding verzendt. Zie [een installatie kopie van een virtuele machine testen](azure-vm-image-test.md)om de JSON-indeling voor het inrichten van de VM weer te geven.

Het inrichtings probleem kan de volgende fout scenario's omvatten:

|Scenario|Fout|Reden|Oplossing|
|---|---|---|---|
|1|Ongeldige virtuele harde schijf (VHD)|Als de opgegeven cookie waarde in de voet tekst van de VHD onjuist is, wordt de VHD als ongeldig beschouwd.|Maak de installatie kopie opnieuw en verzend de aanvraag.|
|2|Ongeldig BLOB-type|Het inrichten van de VM is mislukt omdat het gebruikte blok een BLOB-type in plaats van een pagina Type is.|Maak de installatie kopie opnieuw en verzend de aanvraag.|
|3|Time-out voor inrichten of niet juist gegeneraliseerd|Er is een probleem met VM-generalisatie.|Maak de installatie kopie opnieuw met generalisatie en verzend de aanvraag.|
|

> [!NOTE]
> Zie voor meer informatie over VM-generalisatie:
> - [Linux-documentatie](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows-documentatie](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>VHD-specificaties

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix cookie en andere VHD-specificaties
De teken reeks ' conectix ' maakt deel uit van de VHD-specificatie en gedefinieerd als de 8-bytes ' cookie ' in de onderstaande VHD-voet tekst die de maker van het bestand aangeeft. Alle VHD-bestanden die door micro soft zijn gemaakt, hebben deze cookie. 

Een blob met VHD-indeling moet een 512 byte-voet tekst hebben. Dit is de indeling voor de VHD-voet tekst:

|Vaste-schijf voet tekst velden|Grootte (bytes)|
|---|---|
Cookies|8
Functies|4
Bestands indelings versie|4
Gegevens offset|8
Tijdstempel|4
Maker-toepassing|4
Creator-versie|4
Creator host-besturings systeem|4
Oorspronkelijke grootte|8
Huidige grootte|8
Schijf geometrie|4
Schijftype|4
Controlesom|4
Unieke id|16
Opgeslagen status|1
Gereserveerd|427


### <a name="vhd-specifications"></a>VHD-specificaties
Zorg ervoor dat **de VHD voldoet aan de volgende criteria** om te zorgen voor een naadloze publicatie:
* De cookie moet de teken reeks ' conectix ' bevatten
* Het schijf type moet worden hersteld
* De virtuele grootte van de VHD is ten minste 20 MB
* De VHD is uitgelijnd (dat wil zeggen: de virtuele grootte moet een meervoud van 1 MB zijn)
* De VHD-BLOB-lengte = virtuele grootte + VHD-voet tekst lengte (512)

U kunt de VHD-specificatie [hier downloaden.](https://www.microsoft.com/download/details.aspx?id=23850)


## <a name="software-compliance-for-windows"></a>Software compatibiliteit voor Windows

Als uw Windows-installatie kopie wordt geweigerd vanwege een probleem met de software-naleving, hebt u mogelijk een Windows-installatie kopie gemaakt met het geïnstalleerde SQL Server-exemplaar in plaats van de relevante SQL-versie basis installatie kopie van Azure Marketplace.

Maak geen eigen Windows-installatie kopie waarop SQL Server is geïnstalleerd. Gebruik in plaats daarvan de goedgekeurde SQL-basis installatie kopieën (Enter prise/Standard/web) van Azure Marketplace.

Neem contact op met het ondersteunings team voor een eerdere goed keuring als u probeert Visual Studio of een product met Office-licenties te installeren.

Zie [een virtuele machine maken van een goedgekeurde basis](azure-vm-create-using-approved-base.md)voor meer informatie over het selecteren van een goedgekeurde basis.

## <a name="tool-kit-test-case-execution-failed"></a>Uitvoering van test case van Tool Kit is mislukt

Met de micro soft-certificerings Toolkit kunt u test cases uitvoeren en controleren of uw VHD of installatie kopie compatibel is met de Azure-omgeving.

Down load de [micro soft-certificerings Toolkit](azure-vm-image-test.md).

## <a name="linux-test-cases"></a>Linux-test cases

De volgende tabel geeft een lijst van de Linux-test cases die door de Toolkit worden uitgevoerd. Test validatie wordt vermeld in de beschrijving.

|Scenario|Testcase|Beschrijving|
|---|---|---|
|1|Bash-geschiedenis|Bash-geschiedenis bestanden moeten worden gewist voordat u de VM-installatie kopie maakt.|
|2|Versie van Linux-agent|Azure Linux Agent 2.2.41 of hoger moet worden geïnstalleerd.|
|3|Vereiste kernel-para meters|Verifieert of de volgende kernel-para meters zijn ingesteld: <br>console = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Partitie wisselen op besturingssysteem schijf|Hiermee wordt gecontroleerd of wisselende partities niet zijn gemaakt op de besturingssysteem schijf.|
|5|Basis partitie op besturingssysteem schijf|Maak een enkele hoofd partitie voor de besturingssysteem schijf.|
|6|OpenSSL-versie|De OpenSSL-versie moet v 0.9.8 of hoger zijn.|
|7|Python-versie|Python-versie 2,6 of hoger wordt sterk aanbevolen.|
|8|Interval van client Alive|Stel ' ClientAliveInterval in op 180. Op basis van de toepassing kan deze worden ingesteld op 30 tot 235. Als u de SSH inschakelt voor uw eind gebruikers, moet deze waarde worden ingesteld zoals wordt uitgelegd.|
|9|Architectuur van besturingssysteem|Alleen 64-bits besturingssystemen worden ondersteund.|
|10|Automatisch bijwerken|Hiermee wordt aangegeven of automatisch bijwerken van de Linux-agent is ingeschakeld.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Veelvoorkomende fouten tijdens het uitvoeren van de vorige test cases

De volgende tabel bevat algemene fouten die zijn gevonden tijdens het uitvoeren van eerdere test cases:
 
|Scenario|Testcase|Fout|Oplossing|
|---|---|---|---|
|1|Test case voor Linux-agent versie|De minimale versie van de Linux-agent is 2.2.41 of hoger. Deze vereiste is verplicht sinds 1 mei 2020.|Werk de versie van de Linux-agent bij en deze moet 2,241 of hoger zijn. Ga voor meer informatie naar de [Update pagina van de Linux-agent versie](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Test case voor bash geschiedenis|U ziet een fout als de grootte van de bash-geschiedenis in uw verzonden afbeelding meer dan 1 kilo byte (KB) is. De grootte is beperkt tot 1 KB om ervoor te zorgen dat mogelijk gevoelige gegevens niet worden vastgelegd in uw bash-geschiedenis bestand.|Om dit probleem op te lossen, koppelt u de VHD aan een andere werkende VM en brengt u de gewenste wijzigingen aan (u kunt bijvoorbeeld de *bash* -geschiedenis bestanden verwijderen) om de grootte te verkleinen tot minder dan of gelijk aan 1 KB.|
|3|Vereiste test case voor de kernel-para meter|U ontvangt deze fout melding wanneer de waarde voor de **console** niet is ingesteld op **ttyS0**. Controleer door de volgende opdracht uit te voeren:<br>`cat /proc/cmdline`|Stel de waarde voor de **console** in op **ttyS0** en verzend de aanvraag opnieuw.|
|4|Test case ClientAlive-interval|Als het resultaat van de Toolkit een mislukt resultaat voor deze test case geeft, is er een onjuiste waarde voor **' ClientAliveInterval**.|Stel de waarde voor **' ClientAliveInterval** in op kleiner dan of gelijk aan 235 en verzend de aanvraag vervolgens opnieuw.|

### <a name="windows-test-cases"></a>Test cases van Windows

De volgende tabel geeft een lijst van de Windows-test cases die de Toolkit moet uitvoeren, samen met een beschrijving van de test validatie:

|Scenario |Testcases|Beschrijving|
|---|---|---|---|
|1|Architectuur van besturingssysteem|Azure ondersteunt alleen 64-bits besturings systemen.|
|2|Afhankelijkheid van gebruikers account|Uitvoering van toepassing mag niet afhankelijk zijn van het Administrator-account.|
|3|Failovercluster|De Windows Server failover clustering-functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|4|IPCONFIGURATION|IPv6 wordt nog niet ondersteund in de Azure-omgeving. De toepassing mag niet afhankelijk zijn van deze functie.|
|5|DHCP|Dynamic Host Configuration Protocol server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|6|Hyper-V|De Hyper-V-Server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|7|Externe toegang|De serverrol voor externe toegang (Direct Access) wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|8|Rights Management Services|Rights Management Services. De server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|9|Windows Deployment Services|Windows Deployment Services. De server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|10|BitLocker-stationsversleuteling|BitLocker-stationsversleuteling wordt niet ondersteund op de vaste schijf van het besturings systeem, maar kan worden gebruikt op gegevens schijven.|
|11|Naam server voor Internet opslag|De functie Internet Storage Name Server wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|12|Multipath I/O|Multipath I/O. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|13|Netwerktaakverdeling|Netwerk taakverdeling. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|14|Peer Name Resolution Protocol|Peer Name Resolution Protocol. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|15|SNMP-services|De functie Services van Simple Network Management Protocol (SNMP) wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|16|Windows Internet Name Service|Windows Internet Name Service. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|17|WLAN-service (Wireless LAN)|Draadloze LAN-service. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|

Als u problemen ondervindt met de voor gaande test cases, raadpleegt u de kolom **Beschrijving** in de tabel voor de oplossing. Als u meer informatie nodig hebt, neemt u contact op met het ondersteunings team. 

## <a name="data-disk-size-verification"></a>Verificatie van de grootte van de gegevens schijf

Als de grootte van een aanvraag die wordt verzonden met de gegevens schijf groter is dan 1023 gigabytes (GB), wordt de aanvraag niet goedgekeurd. Deze regel is van toepassing op Linux en Windows.

Verzend de aanvraag opnieuw met een grootte die kleiner is dan of gelijk is aan 1023 GB.

## <a name="os-disk-size-validation"></a>Validatie van schijf grootte van besturings systeem

Raadpleeg de volgende regels voor beperkingen op de schijf grootte van het besturings systeem. Wanneer u een aanvraag indient, controleert u of de grootte van de besturingssysteem schijf binnen de limiet voor Linux of Windows valt.

|Besturingssysteem|Aanbevolen grootte voor VHD|
|---|---|
|Linux|30 GB tot 1023 GB|
|Windows|30 GB tot 250 GB|
|

Als Vm's toegang bieden tot het onderliggende besturings systeem, moet u ervoor zorgen dat de VHD groot genoeg is voor de VHD. Omdat schijven niet kunnen worden uitgevouwen zonder uitval tijd, gebruikt u een schijf grootte van 30 GB tot 50 GB.

|VHD-grootte|Werkelijke grootte|Oplossing|
|---|---|---|
|>500 tebibytes (TiB)|n.v.t.|Neem contact op met het ondersteunings team voor een uitzonderings goedkeuring.|
|250-500 TiB|>200 gibibytes (GiB) verschilt van de grootte van de BLOB|Neem contact op met het ondersteunings team voor een uitzonderings goedkeuring.|
|

> [!NOTE]
> Grotere schijf grootten leiden tot hogere kosten en zullen een vertraging oplopen tijdens de installatie en het replicatie proces. Als gevolg van deze vertraging en kosten kan het ondersteunings team een reden voor de goed keuring van uitzonde ringen zoeken.

## <a name="wannacry-patch-verification-test-for-windows"></a>Verificatie test voor de WannaCry-patch voor Windows

Als u een mogelijke aanval met betrekking tot het WannaCry-virus wilt voor komen, moet u ervoor zorgen dat alle Windows-installatie kopie aanvragen worden bijgewerkt met de nieuwste patch.

Raadpleeg de volgende tabel om de patch versie van Windows Server te controleren voor de details van het besturings systeem en de minimale versie die wordt ondersteund: 

De versie van het installatie kopie bestand kan worden gecontroleerd vanuit `C:\windows\system32\drivers\srv.sys` of `srv2.sys` .

> [!NOTE]
> Windows Server 2019 heeft geen verplichte versie vereisten.

|Besturingssysteem|Versie|
|---|---|
|Windows met 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|
|

## <a name="sack-vulnerability-patch-verification"></a>Verificatie van beveiligings patch voor de Opzakken

Wanneer u een Linux-installatie kopie verzendt, wordt uw aanvraag mogelijk afgewezen vanwege problemen met de kernel-versie.

Werk de kernel bij met een goedgekeurde versie en verzend de aanvraag opnieuw. U kunt de goedgekeurde versie van de kernel vinden in de volgende tabel. Het versie nummer moet gelijk zijn aan of groter zijn dan het nummer dat hier wordt weer gegeven.

Als uw installatie kopie niet is geïnstalleerd met een van de volgende kernel-versies, werkt u deze bij met de juiste patches. De benodigde goed keuring van het ondersteunings team aanvragen nadat de installatie kopie is bijgewerkt met de volgende vereiste patches:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|BESTURINGSSYSTEEM familie|Versie|Kernel|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0-151| 
||14,04 LTS|4.15.0-1049-*-Azure|
||16,04 LTS|4.15.0-1049|
||18,04 LTS|4.18.0-1023|
||18,04 LTS|5.0.0-1025|
||18,10|4.18.0-1023|
||19,04|5.0.0-1010|
||19,04|5.3.0-1004|
|RHEL en Cent OS|6.10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7,7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL-SAP 7,4|NOG TE BEPALEN|
||RHEL-SAP 7,5|NOG TE BEPALEN|
|SLES|SLES11SP4 (inclusief SAP)|3.0.101-108.95.2|
||SLES12SP1 voor SAP|3.12.74-60.64.115.1|
||SLES12SP2 voor SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-Azure)|
||SLES12SP3 voor SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-Azure)|
||SLES12SP4 voor SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-Azure)|
||SLES15 voor SAP|4.12.14-5.30.1 (kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-Azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK volgt RHEL hierboven|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS stabiele 2079.6.0|4.19.43*|
||Bèta 2135.3.1|4.19.50*|
||Alpha-2163.2.1|4.19.50*|
|Debian|Jessie (beveiliging)|3.16.68-2|
||Jessie backports|4.9.168-1 + deb9u3|
||uitrekken (beveiliging)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, sid (stretch backports)|4.19.37-5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>De grootte van de installatie kopie moet in meerdere mega bytes staan

Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op veelvouden van 1 Mega byte (MB). Als uw VHD niet aan de aanbevolen virtuele grootte voldoet, wordt uw aanvraag mogelijk geweigerd.

Volg de richt lijnen wanneer u van een onbewerkte schijf naar VHD converteert en zorg ervoor dat de onbewerkte schijf grootte een meervoud van 1 MB is. Zie [informatie over niet-goedgekeurde distributies](../virtual-machines/linux/create-upload-generic.md)voor meer informatie.

## <a name="vm-access-denied"></a>VM-toegang geweigerd

Als u tijdens het uitvoeren van de test cases op de VM problemen ondervindt bij het weigeren van de toegang, kan dit worden veroorzaakt door onvoldoende bevoegdheden om de test cases uit te voeren.

Controleer of de juiste toegang is ingeschakeld voor het account waarop de self-test cases worden uitgevoerd. Als de toegang niet is ingeschakeld, schakelt u deze in om de test cases uit te voeren. Als u geen toegang wilt inschakelen, kunt u de test resultaten met het ondersteunings team delen.

Als u uw aanvraag met SSH-uitgeschakelde installatie kopie wilt verzenden voor het certificerings proces, volgt u de onderstaande stappen

1. Voer de Azure-Toolkit uit op uw installatie kopie. (Down load de [nieuwste Toolkit](https://aka.ms/AzureCertificationTestTool)

2. Verhoog een [ondersteunings ticket](https://aka.ms/marketplacepublishersupport), voeg het Toolkit rapport toe en geef de details van de aanbieding, naam van de uitgever, plan-id/SKU en versie op.

3. Verzend uw certificerings aanvraag opnieuw.


## <a name="download-failure"></a>Fout bij het downloaden
    
Raadpleeg de volgende tabel voor eventuele problemen bij het downloaden van de VM-installatie kopie met behulp van een SAS-URL (Shared Access Signature).

|Scenario|Fout|Reden|Oplossing|
|---|---|---|---|
|1|De blob is niet gevonden|De VHD kan worden verwijderd of verplaatst van de opgegeven locatie.|| 
|2|BLOB in gebruik|De VHD wordt gebruikt door een ander intern proces.|De VHD moet een gebruikte status hebben wanneer u deze downloadt met behulp van een SAS-URL.|
|3|Ongeldige SAS-URL|De bijbehorende SAS-URL voor de VHD is onjuist.|Haal de juiste SAS-URL op.|
|4|Ongeldige hand tekening|De bijbehorende SAS-URL voor de VHD is onjuist.|Haal de juiste SAS-URL op.|
|6|Voorwaardelijke HTTP-header|De SAS-URL is ongeldig.|Haal de juiste SAS-URL op.|
|7|Ongeldige naam voor VHD|Controleer of er speciale tekens, zoals een procent teken (%), worden weer geven of aanhalings tekens ("), bestaan in de naam van de VHD.|Wijzig de naam van het VHD-bestand door de speciale tekens te verwijderen.|
|

## <a name="first-mb-2048-kb-partition-only-for-linux"></a>Eerste MB (2048 KB)-partitie (alleen voor Linux)

Wanneer u de VHD verzendt, moet u ervoor zorgen dat de eerste 2048 KB van de VHD leeg is. Als dat niet het geval is, mislukt de aanvraag *.

>[!NOTE]
>* Voor bepaalde speciale installatie kopieën, zoals die zijn gebouwd op basis van Azure Windows Base-installatie kopieën die zijn gemaakt met Azure Marketplace, wordt gecontroleerd op een facturerings code en wordt de MB-partitie genegeerd als de facturerings code aanwezig is en overeenkomt met onze interne beschik bare waarden.


## <a name="steps-for-creating-first-mb-2048-kb-partition-only-for-linux-on-an-empty-vhd"></a>Stappen voor het maken van de eerste MB (2048 KB)-partitie (alleen voor Linux) op een lege VHD

Stap 1: Maak een wille keurig type VM (bijvoorbeeld: Ubuntu, Cent OS, enzovoort). Vul de vereiste velden in en klik op volgende: schijven> \
![Volgende: schijven, opdracht](./media/create-vm/vm-certification-issues-solutions-15.png)

Stap 2: Maak een niet-beheerde schijf voor de bovenstaande VM.
![Een niet-beheerde schijf maken](./media/create-vm/vm-certification-issues-solutions-16.png)

Houd er rekening mee dat u met standaard waarden kunt gaan of een wille keurige waarde voor velden als NIC, NSG en openbaar IP-adres op te geven.

Stap 3: nadat u de virtuele machine hebt gemaakt, klikt u op schijven aan de linkerkant, zoals hieronder wordt weer gegeven, klikt u op ![ schijven](./media/create-vm/vm-certification-issues-solutions-17.png)

Stap 4: koppel uw VHD als gegevens schijf aan de bovenstaande VM voor het maken van een partitie tabel zoals hieronder.
![Uw VHD koppelen](./media/create-vm/vm-certification-issues-solutions-18.png)

Klik op DataDisk toevoegen-> bestaande BLOB-> Blader door uw VHD Storage-account-> container-> Selecteer VHD-> Klik op OK als hieronder \
![VHD selecteren](./media/create-vm/vm-certification-issues-solutions-19.png)

Uw VHD wordt toegevoegd als Data Disk LUN 0 en start de virtuele machine opnieuw op nadat u de schijf hebt toegevoegd.

Stap 5: Meld u na het opnieuw starten van de virtuele machine aan bij de VM met behulp van putty (of een andere client) en voer de opdracht ' sudo-i ' uit om toegang tot het hoofd te krijgen.

![Aanmelden bij de virtuele machine](./media/create-vm/vm-certification-issues-solutions-20.png)

Stap 6: Volg de onderstaande stappen om een partitie te maken op uw VHD.

a) type fdisk/dev/sdb-opdracht

b) voor het weer geven van de bestaande partitie lijst van uw VHD, typt u p

c) type d voor het verwijderen van alle bestaande partities die beschikbaar zijn op uw VHD (u kunt deze stap overs Laan als dit niet vereist is) ![ Verwijder alle bestaande partitie](./media/create-vm/vm-certification-issues-solutions-21.png)

d) Typ n als u een nieuwe partitie wilt maken en selecteer p voor (primaire partitie).

e) Geef 2048 op als de waarde ' First sector ' en u kunt ' laatste sector ' verlaten, omdat de standaard waarde wordt genoteerd. Houd er rekening mee dat alle gegevens worden gewist tot 2048 KB.
           
>[!NOTE]
>* Houd er rekening mee dat door het maken van de partitie, net als bij alle bestaande gegevens, de data base van 2048 KB wordt gewist. Daarom wordt het aanbevolen om een back-up van de VHD te maken voordat u de bovenstaande opdracht uitvoert.

Ga naar de onderstaande scherm afbeelding voor uw referentie.
![Gegevens gewist](./media/create-vm/vm-certification-issues-solutions-22.png)

f) Typ w om te bevestigen dat de partitie is gemaakt. 

![Partitie maken](./media/create-vm/vm-certification-issues-solutions-23.png)

g) u kunt de partitie tabel controleren door de opdracht n fdisk/dev/sdb uit te voeren en p te typen. vervolgens ziet u dat de partitie wordt gemaakt met een offset waarde van 2048. 

 ![2048 offset](./media/create-vm/vm-certification-issues-solutions-24.png)

Stap 7: Maak de VHD los van de virtuele machine en verwijder de virtuele machine.

         
## <a name="steps-for-creating-first-mb-2048-kb-partition-only-for-linux-by-moving-the-existing-data-on-vhd"></a>Stappen voor het maken van de eerste MB (2048 KB)-partitie (alleen voor Linux) door de bestaande gegevens op de VHD te verplaatsen

Stap 1: Maak een wille keurig type VM (bijvoorbeeld: Ubuntu, Cent OS, enzovoort). Vul de vereiste velden in en klik op volgende: schijven> \
![Klik op volgende: schijven>](./media/create-vm/vm-certification-issues-solutions-15.png)

Stap 2: Maak een niet-beheerde schijf voor de bovenstaande VM.
![Een niet-beheerde schijf maken](./media/create-vm/vm-certification-issues-solutions-16.png)

Houd er rekening mee dat u met standaard waarden kunt gaan of een wille keurige waarde voor velden als NIC, NSG en openbaar IP-adres op te geven.

Stap 3: nadat u de virtuele machine hebt gemaakt, klikt u op schijven aan de linkerkant, zoals hieronder wordt weer gegeven, klikt u op ![ schijven](./media/create-vm/vm-certification-issues-solutions-17.png)

Stap 4: koppel uw VHD als gegevens schijf aan de bovenstaande VM voor het maken van een partitie tabel zoals hieronder.
![Partitie tabel](./media/create-vm/vm-certification-issues-solutions-18.png)

Klik op DataDisk toevoegen-> bestaande BLOB-> Blader door uw VHD Storage-account-> container-> Selecteer VHD-> Klik op OK als hieronder \
![VHD selecteren](./media/create-vm/vm-certification-issues-solutions-19.png)

Uw VHD wordt toegevoegd als Data Disk LUN 0 en start de virtuele machine opnieuw op nadat u de schijf hebt toegevoegd.

Stap 5: Meld u na het opnieuw starten van de virtuele machine aan bij de VM met behulp van putty en voer de opdracht ' sudo-i ' uit om toegang tot het hoofd te krijgen. \
![Aanmelden na opnieuw opstarten](./media/create-vm/vm-certification-issues-solutions-20.png)

Stap 6: excute de opdracht echo ' + 1M ' | sfdisk--move-data/dev/SDC-N 1 ![ opdracht uitvoeren](./media/create-vm/vm-certification-issues-solutions-25.png)

>[!NOTE]
>* De bovenstaande opdracht kan meer tijd in beslag nemen, omdat deze afhankelijk is van de grootte van de schijf

Stap 7: Maak de VHD los van de virtuele machine en verwijder de virtuele machine.


## <a name="default-credentials"></a>Standaard referenties

Zorg er altijd voor dat er geen standaard referenties worden verzonden met de verzonden VHD. Het toevoegen van standaard referenties maakt de VHD kwetsbaarder voor beveiligings Risico's. Maak in plaats daarvan uw eigen referenties wanneer u de VHD verzendt.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk onjuist toegewezen

Wanneer een aanvraag wordt ingediend met meerdere gegevens schijven, maar de volg orde ervan niet bestaat, wordt dit beschouwd als een toewijzings probleem. Als er bijvoorbeeld drie gegevens schijven zijn, moet de Nummerings volgorde *0, 1, 2* zijn. Alle andere orders worden behandeld als een toewijzings probleem.

Verzend de aanvraag opnieuw met de juiste volg orde van de gegevens schijven.

## <a name="incorrect-os-mapping"></a>Onjuiste besturingssysteem toewijzing

Wanneer een installatie kopie wordt gemaakt, kan deze worden toegewezen aan of worden toegewezen aan het verkeerde besturingssysteem label. Wanneer u bijvoorbeeld **Windows** als onderdeel van de naam van het besturings systeem selecteert tijdens het maken van de installatie kopie, moet de besturingssysteem schijf alleen met Windows worden geïnstalleerd. Dezelfde vereiste is van toepassing op Linux.

## <a name="vm-not-generalized"></a>De VM is niet gegeneraliseerd

Als alle installatie kopieën die vanuit Azure Marketplace worden gemaakt, opnieuw moeten worden gebruikt, moet de virtuele harde schijf van het besturings systeem worden gegeneraliseerd.

* Voor **Linux** wordt met het volgende proces een virtuele Linux-machine gegeneraliseerd en opnieuw geïmplementeerd als een afzonderlijke VM.

  Voer in het SSH-venster de volgende opdracht in: `sudo waagent -deprovision+user`

* Voor **Windows** kunt u Windows-installatie kopieën generaliseren met behulp van `sysreptool` .

Zie [overzicht van systeem voorbereiding (Sysprep)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor meer informatie over dit hulp programma.

## <a name="datadisk-errors"></a>DataDisk-fouten

Gebruik de volgende tabel voor oplossingen voor fouten die betrekking hebben op de gegevens schijf:

|Fout|Reden|Oplossing|
|---|---|---|
|`DataDisk- InvalidUrl:`|Deze fout kan optreden vanwege een ongeldig aantal dat is opgegeven voor de Logical Unit Number (LUN) wanneer de aanbieding wordt verzonden.|Controleer of de LUN-nummer reeks voor de gegevens schijf zich in het partner centrum bevindt.|
|`DataDisk- NotFound:`|Deze fout kan optreden als er geen gegevens schijf is gevonden op een opgegeven SAS-URL.|Controleer of de gegevens schijf zich bevindt op de SAS-URL die in de aanvraag is opgegeven.|
|

## <a name="remote-access-issue"></a>Probleem met externe toegang

Als de optie Remote Desktop Protocol (RDP) niet is ingeschakeld voor de Windows-installatie kopie, wordt deze fout weer gegeven. 

Schakel RDP-toegang voor Windows-installatie kopieën in voordat u deze verzendt.

## <a name="bash-history-failed"></a>Bash-geschiedenis is mislukt

U ziet deze fout als de grootte van de bash-geschiedenis in uw verzonden afbeelding meer dan 1 kilo byte (KB) is. De grootte is beperkt tot 1 KB om ervoor te zorgen dat mogelijk gevoelige gegevens niet worden vastgelegd in uw bash-geschiedenis bestand.

Hieronder vindt u de stappen voor het verwijderen van de ' bash-geschiedenis '.

Stap 1. Implementeer de virtuele machine en klik op de optie opdracht uitvoeren op Azure Portal.
![Opdracht uitvoeren op Azure Portal](./media/create-vm/vm-certification-issues-solutions-3.png)

Stap 2. Selecteer eerste optie ' RunShellScript ' en voer de onderstaande opdracht uit.

Opdracht: "kat/dev/null > ~/.bash_history && geschiedenis-c" ![ bash-geschiedenis opdracht op Azure Portal](./media/create-vm/vm-certification-issues-solutions-4.png)

Stap 3. Nadat de opdracht is uitgevoerd, start u de VM opnieuw op.

Stap 4. Generaliseer de virtuele machine, maak de VHD met installatie kopieën en stop de virtuele machine.

Stap 5. Re-Submit de gegeneraliseerde installatie kopie.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Uitzonde ringen aanvragen (aangepaste sjablonen) op VM-installatie kopieën voor selectieve tests

Uitgevers kunnen uitzonde ringen aanvragen voor weinig testen die tijdens de VM-certificering worden uitgevoerd. Uitzonde ringen zijn in extreem zeldzame gevallen beschikbaar wanneer Publisher bewijs levert ter ondersteuning van de aanvraag. Het certificerings team behoudt zich het recht voor om uitzonde ringen op elk gewenst moment te weigeren of goed te keuren.

In de volgende secties wordt gecommuniceerd over belang rijke scenario's waarin uitzonde ringen worden aangevraagd en hoe u er een kunt aanvragen.

### <a name="scenarios-for-exception"></a>Scenario's voor uitzonde ring

Er zijn in het algemeen drie scenario's/gevallen waarin uitgevers uitzonde ringen aanvragen.

- **Uitzonde ring voor een of meer test cases** : uitgevers die contact opnemen met het partner centrum [ondersteunen](https://aka.ms/marketplacepublishersupport) om uitzonde ringen voor test cases aan te vragen.

- **Vergrendelde vm's/geen hoofd toegang** : voor sommige uitgevers zijn er Scenario's waarbij vm's moeten worden vergrendeld omdat ze software hebben, zoals firewalls die op de virtuele machine zijn geïnstalleerd. In dit geval kunnen uitgevers het [gecertificeerde test programma](https://aka.ms/AzureCertificationTestTool) downloaden en het rapport verzenden bij Partner Center- [ondersteuning](https://aka.ms/marketplacepublishersupport).

- **Aangepaste sjablonen** : sommige UITGEVERS publiceren VM-installatie kopieën waarvoor een aangepaste arm-sjabloon vereist is om de virtuele machines te implementeren. In dit geval moeten uitgevers de aangepaste sjablonen indienen bij Partner Center- [ondersteuning](https://aka.ms/marketplacepublishersupport) , zodat hetzelfde kan worden gebruikt door het certificerings team voor validatie.

### <a name="information-to-provide-for-exception-scenarios"></a>Informatie over uitzonderings scenario's

Uitgevers moeten contact opnemen met de [ondersteuning](https://aka.ms/marketplacepublishersupport) van partner Center voor het aanvragen van uitzonde ringen voor het bovenstaande scenario met de volgende informatie:

   1. Uitgevers-ID: de uitgevers-ID in het partner centrum-Portal
   2. Aanbiedings-ID/naam: de aanbiedings-ID/naam waarvoor een uitzonde ring is aangevraagd
   3. SKU/plan-ID: de plan-ID/SKU van de VM-aanbieding waarvoor een uitzonde ring is aangevraagd
   4. Versie: de versie van de VM-aanbieding waarvoor een uitzonde ring is aangevraagd
   5. Uitzonderings type: tests, vergrendelde virtuele machine, aangepaste sjablonen
   6. Reden van aanvraag: reden voor deze uitzonde ring en informatie over de tests die moeten worden uitgesloten
   7. Tijd lijn: de datum waarop deze uitzonde ring is aangevraagd
   8. Bijlage: Voeg documenten met een belang rijk bewijs toe. Voor vergrendelde Vm's koppelt u het test rapport en aangepaste sjablonen, geeft u de aangepaste ARM-sjabloon op als bijlage. Fout bij het koppelen van het rapport voor de vergrendelde Vm's en de aangepaste ARM-sjabloon voor aangepaste sjablonen, resulteert in een weigering van de aanvraag

## <a name="address-a-vulnerability-or-exploit-in-a-vm-offer"></a>Een beveiligingslek aanpakken of misbruik maken in een VM-aanbieding

In deze sectie wordt beschreven hoe u een nieuwe VM-installatie kopie kunt opgeven wanneer er een beveiligings probleem of misbruik wordt gedetecteerd met een van uw VM-installatie kopieën. Dit geldt alleen voor virtuele Azure-machines die zijn gepubliceerd op de Azure Marketplace.

> [!NOTE]
> U kunt de laatste VM-installatie kopie niet uit een plan verwijderen of het laatste abonnement voor een aanbieding stoppen.

Voer een van de volgende handelingen uit:

- Zie [een vaste VM-installatie kopie opgeven](#provide-a-fixed-vm-image) hieronder als u een nieuwe VM-installatie kopie hebt om de KWETS bare VM-installatie kopie te vervangen.
- Als u geen nieuwe VM-installatie kopie hebt om de enige VM-installatie kopie in een abonnement te vervangen of als u klaar bent met het abonnement, kunt u [het abonnement niet meer verkopen](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Als u niet van plan bent om de enige VM-installatie kopie in de aanbieding te vervangen, raden we u aan om te [stoppen met het verkopen van de aanbieding](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Een vaste VM-installatie kopie opgeven

Ga als volgt te werk om een installatie kopie van een virtuele machine te vervangen die een beveiligings probleem of misbruik heeft:

1. Geef een nieuwe VM-installatie kopie op om het beveiligings probleem op te lossen of misbruik te maken.
2. Verwijder de VM-installatie kopie met het beveiligings probleem of gebruik.
3. Publiceer de aanbieding opnieuw.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Een nieuwe VM-installatie kopie opgeven om het beveiligings probleem op te lossen of misbruik te maken

Als u deze stappen wilt uitvoeren, moet u de technische assets voorbereiden voor de VM-installatie kopie die u wilt toevoegen. Zie [een virtuele machine maken met behulp van een goedgekeurde basis](azure-vm-create-using-approved-base.md) of [een virtuele machine maken met uw eigen installatie kopie](azure-vm-create-using-own-image.md)en [een SAS-URI voor uw VM-installatie kopie genereren](azure-vm-get-sas-uri.md)voor meer informatie.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het navigatie menu de optie **commerciële Marketplace** -  >  **overzicht**.
3. Selecteer de aanbieding in de kolom **aanbiedings alias** .
4. Selecteer op het tabblad **plan overzicht** , in de kolom **naam** , het plan waaraan u de virtuele machine wilt toevoegen.
5. Selecteer op het tabblad **technische configuratie** onder **VM-installatie kopieën** **+ installatie kopie van virtuele machine toevoegen**.

> [!NOTE]
> U kunt slechts één VM-installatie kopie toevoegen aan één abonnement per keer. Als u meerdere VM-installatie kopieën wilt toevoegen, publiceert u de eerste Live voordat u de volgende VM-installatie kopie toevoegt.

6. Geef in de weer gegeven vakken een nieuwe schijf versie en de installatie kopie van de virtuele machine op.
7. Selecteer **Concept opslaan**.

Ga verder met de volgende sectie hieronder om de VM-installatie kopie te verwijderen met het beveiligings probleem.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>De VM-installatie kopie verwijderen met het beveiligings probleem of de misbruik

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het navigatie menu de optie **commerciële Marketplace** -  >  **overzicht**.
3. Selecteer de aanbieding in de kolom **aanbiedings alias** .
4. Selecteer op het tabblad **plan overzicht** , in de kolom **naam** , het plan met de virtuele machine die u wilt verwijderen.
5. Op het tabblad **technische configuratie** onder **VM-installatie kopieën** naast de VM-installatie kopie die u wilt verwijderen, selecteert u VM- **installatie kopie verwijderen**.
6. Selecteer **door gaan** in het dialoog venster dat wordt weer gegeven.
7. Selecteer **Concept opslaan**.

Ga verder met de volgende sectie hieronder om de aanbieding opnieuw te publiceren.

#### <a name="republish-the-offer"></a>De aanbieding opnieuw publiceren

1. Selecteer **controleren en publiceren**.
2. Als u informatie moet verstrekken aan het certificerings team, voegt u deze toe aan het vak **notities voor certificering** .
3. Selecteer **Publiceren**.

Zie [aanbiedingen bekijken en publiceren](review-publish-offer.md)om het publicatie proces te volt ooien.

## <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van een VM-aanbieding configureren](azure-vm-create-properties.md)
- [Beloonde actieve Marketplace](partner-center-portal/marketplace-rewards.md)
- Als u vragen of feedback over verbeteringen hebt, neemt u contact op met het partner centrum- [ondersteuning](https://aka.ms/marketplacepublishersupport).
