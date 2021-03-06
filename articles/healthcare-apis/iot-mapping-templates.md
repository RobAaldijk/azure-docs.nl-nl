---
title: 'Concepten: sjablonen toewijzen in azure IoT connector voor FHIR-functie (preview) van Azure API voor FHIR'
description: Meer informatie over het maken van twee typen toewijzings sjablonen in azure IoT connector voor FHIR (preview). Met de sjabloon voor apparaattoewijzing worden apparaatgegevens getransformeerd tot een genormaliseerd schema. Met de sjabloon voor FHIR-toewijzing wordt een genormaliseerd bericht getransformeerd tot een op FHIR gebaseerde observatieresource.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 63484361a6d5a331fd9dc646c53627918ce8b246
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630546"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Toewijzingssjablonen in Azure IoT Connector for FHIR (preview)
In dit artikel wordt uitgelegd hoe u Azure IoT connector kunt configureren voor snelle bronnen voor de gezondheids zorg (FHIR&#174;) * met toewijzings sjablonen.

De Azure IoT connector voor FHIR vereist twee typen toewijzings sjablonen op basis van JSON. Het eerste type, **apparaattoewijzing** , is verantwoordelijk voor het toewijzen van de nettoladingen van apparaten die zijn verzonden naar het `devicedata` eind punt van de Azure Event hub. Het extraheert typen, apparaat-id's, meting datum en meet waarde (n). Het tweede type, **FHIR-toewijzing** , bepaalt de toewijzing voor FHIR-resource. Hiermee kunt u de lengte van de observatie periode, het gegevens type FHIR dat wordt gebruikt voor het opslaan van de waarden en terminologie code (s), configureren. 

De toewijzings sjablonen zijn samengesteld in een JSON-document op basis van hun type. Deze JSON-documenten worden vervolgens toegevoegd aan uw Azure IoT-connector voor FHIR via de Azure Portal. Het document apparaattoewijzing wordt toegevoegd via de pagina apparaattoewijzing **configureren** en het FHIR-toewijzings document via de pagina **toewijzing van FHIR configureren** .

> [!NOTE]
> Toewijzings sjablonen worden opgeslagen in een onderliggende Blob-opslag en geladen vanuit BLOB per Compute-uitvoering. Na de update worden ze onmiddellijk van kracht. 

## <a name="device-mapping"></a>Apparaattoewijzing
Apparaattoewijzing biedt toewijzings functionaliteit voor het extra heren van inhoud van een apparaat in een gemeen schappelijke indeling. Elk ontvangen bericht wordt geëvalueerd op basis van alle sjablonen. Met deze aanpak kan één binnenkomend bericht worden geprojecteerd aan meerdere uitgaande berichten, die later worden toegewezen aan verschillende waarnemingen in FHIR. Het resultaat is een genormaliseerd gegevens object dat de door de sjablonen geparseerde waarde of waarden vertegenwoordigt. Het genormaliseerde gegevens model bevat enkele vereiste eigenschappen die moeten worden gevonden en geëxtraheerd:

| Eigenschap | Beschrijving |
| - | - |
|**Type**|De naam/het type waarmee de meting moet worden geclassificeerd. Deze waarde wordt gebruikt om een binding te maken met de vereiste sjabloon FHIR-toewijzing.  Meerdere sjablonen kunnen naar hetzelfde type worden uitgevoerd, zodat u verschillende voors tellingen op meerdere apparaten kunt toewijzen aan één enkele algemene uitvoer.|
|**OccurenceTimeUtc**|De tijd die de meting heeft plaatsgevonden.|
|**DeviceId**|De id voor het apparaat. Deze waarde moet overeenkomen met een id op de apparaatnaam die bestaat op de doel server van FHIR.|
 |**Eigenschappen**|Extraheer ten minste één eigenschap zodat de waarde kan worden opgeslagen in de door de resource gemaakte waarneming.  Eigenschappen zijn een verzameling sleutel waardeparen die tijdens normalisatie worden geëxtraheerd.|

Hieronder ziet u een conceptueel voor beeld van wat er gebeurt tijdens normalisatie.

![Voor beeld van normalisatie](media/concepts-iot-mapping-templates/normalization-example.png)

De nettolading voor inhoud zelf is een Azure Event hub-bericht, dat bestaat uit drie delen: Body, Properties en SystemProperties. De `Body` is een byte matrix die een UTF-8-gecodeerde teken reeks vertegenwoordigt. Tijdens de evaluatie van de sjabloon wordt de byte matrix automatisch geconverteerd naar de teken reeks waarde. `Properties` is een sleutel waarde-verzameling voor gebruik door de maker van het bericht. `SystemProperties` is ook een sleutel waarde-verzameling die door het Azure Event hub-Framework is gereserveerd met vermeldingen die automatisch door de service worden ingevuld.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Toewijzing met JSON-pad
De twee typen van de inhouds sjabloon voor het apparaat die nu worden ondersteund, zijn afhankelijk van het JSON-pad naar de vereiste sjabloon en geëxtraheerde waarden. Meer informatie over het JSON-pad vindt u [hier](https://goessner.net/articles/JsonPath/). Beide sjabloon typen gebruiken de [JSON .net-implementatie](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) voor het oplossen van EXPRESSIES van JSON-paden.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
Met de JsonPathContentTemplate kunt u waarden uit een event hub-bericht vergelijken en extra heren met behulp van JSON-pad.

| Eigenschap | Beschrijving |<div style="width:150px">Voorbeeld</div>
| --- | --- | --- 
|**TypeName**|Het type dat moet worden gekoppeld aan de metingen die overeenkomen met de sjabloon.|`heartrate`
|**TypeMatchExpression**|De JSON-padexpressie die wordt geëvalueerd op basis van de nettolading van de Event hub. Als er een overeenkomende JToken wordt gevonden, wordt de sjabloon als een overeenkomst beschouwd. Alle volgende expressies worden geëvalueerd op basis van de geëxtraheerde JToken die hier overeenkomen.|`$..[?(@heartRate)]`
|**TimestampExpression**|De expressie JSON-pad om de time stamp-waarde voor de OccurenceTimeUtc van de meting te extra heren.|`$.endDate`
|**DeviceIdExpression**|De expressie JSON-pad voor het uitpakken van de apparaat-id.|`$.deviceId`
|**PatientIdExpression**|*Optioneel* : de expressie voor het JSON-pad om de patiënt-id op te halen.|`$.patientId`
|**EncounterIdExpression**|*Optioneel* : de expressie van het JSON-pad om de ondertredende id op te halen.|`$.encounterId`
|**Values []. ValueName**|De naam die moet worden gekoppeld aan de waarde die is geëxtraheerd door de volgende expressie. Wordt gebruikt om de vereiste waarde/component in de FHIR-toewijzings sjabloon te koppelen. |`hr`
|**Values []. ValueExpression**|De expressie voor het JSON-pad om de vereiste waarde op te halen.|`$.heartRate`
|**Values []. Vereist**|Moet de waarde aanwezig zijn in de payload.  Als dit niet wordt gevonden, wordt er geen meting gegenereerd en wordt er een InvalidOperationException gegenereerd.|`true`

##### <a name="examples"></a>Voorbeelden
---
**Hart frequentie**

*Bericht*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Sjabloon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Bloed druk**

*Bericht*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Sjabloon*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Meerdere metingen van één bericht projecteren**

*Bericht*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Sjabloon 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Sjabloon 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Meerdere metingen van de matrix in het bericht projecteren**

*Bericht*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Sjabloon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate
De IotJsonPathContentTemplate is vergelijkbaar met de JsonPathContentTemplate, met uitzonde ring van de DeviceIdExpression en TimestampExpression niet vereist zijn.

De veronderstelling bij het gebruik van deze sjabloon is dat de berichten die worden geëvalueerd, worden verzonden met behulp van de [apparaat-sdk's van Azure IOT hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks). Wanneer u deze Sdk's gebruikt, wordt de identiteit van het apparaat (ervan uitgaande dat de apparaat-id van Azure IOT hub/centraal is geregistreerd als een id voor een apparaatbestand op de doel FHIR-server) en de tijds tempel van het bericht bekend. Als u de Sdk's van Azure IoT Hub apparaat gebruikt, maar aangepaste eigenschappen gebruikt in de bericht tekst van de apparaat-id of meting tijds tempel, kunt u nog steeds de JsonPathContentTemplate gebruiken.

*Opmerking: wanneer de IotJsonPathContentTemplate wordt gebruikt, moet de TypeMatchExpression worden omgezet in het hele bericht als een JToken. Zie de onderstaande voor beelden.* 
##### <a name="examples"></a>Voorbeelden
---
**Hart frequentie**

*Bericht*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Sjabloon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Bloed druk**

*Bericht*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Sjabloon*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

## <a name="fhir-mapping"></a>FHIR-toewijzing
Zodra de inhoud van het apparaat is geëxtraheerd naar een genormaliseerd model, worden de gegevens verzameld en gegroepeerd op basis van de apparaat-id, het meet type en de tijds periode. De uitvoer van deze groepering wordt verzonden voor conversie naar een FHIR-resource (huidige[waarneming](https://www.hl7.org/fhir/observation.html) ). Hier wordt de FHIR-toewijzings sjabloon bepaalt hoe de gegevens worden toegewezen aan een FHIR-waarneming. Moet er een waarneming worden gemaakt voor een punt in de tijd of gedurende een periode van een uur? Welke codes moeten worden toegevoegd aan de observatie? Moet de waarde worden weer gegeven als [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) of een [hoeveelheid](https://www.hl7.org/fhir/datatypes.html#Quantity)? Deze gegevens typen zijn allemaal opties voor de configuratie besturings elementen voor FHIR toewijzing.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
De CodeValueFhirTemplate is momenteel de enige sjabloon die op dit moment wordt ondersteund in FHIR-toewijzing.  U kunt hiermee codes, de ingangs periode en de waarde van de waarneming definiëren. Meerdere waardetypen worden ondersteund: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)en [quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Naast deze Configureer bare waarden wordt de id voor de observatie resource en de koppeling naar de juiste apparaten en patiënten-bronnen automatisch verwerkt.

| Eigenschap | Beschrijving 
| --- | ---
|**TypeName**| Het type meting waaraan deze sjabloon moet worden gebonden. Er moet mini maal één toewijzings sjabloon voor een apparaat zijn die dit type uitvoert.
|**PeriodInterval**|De tijd die de gemaakte waarneming moet vertegenwoordigen. Ondersteunde waarden zijn 0 (een exemplaar), 60 (een uur), 1440 (per dag).
|**Categorie**|Een wille keurig aantal [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) voor het classificeren van het type waarneming dat is gemaakt.
|**Verdelen**|Een of meer [code ringen](http://hl7.org/fhir/datatypes-definitions.html#coding) die moeten worden toegepast op de gemaakte waarneming.
|**Codes []. Gecodeerd**|De code voor het [coderen](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes []. Opgehaald**|Het systeem voor de [code ring](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes []. Display**|De weer gave voor de [code ring](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Waarde**|De waarde die moet worden opgehaald en weer gegeven in de waarneming. Zie [waardetype-sjablonen](#valuetypes)voor meer informatie.
|**Onderdelen**|*Optioneel:* Een of meer onderdelen die op de waarneming moeten worden gemaakt.
|**Onderdelen []. Verdelen**|Een of meer [code ringen](http://hl7.org/fhir/datatypes-definitions.html#coding) die moeten worden toegepast op het onderdeel.
|**Onderdelen []. Value**|De waarde die moet worden opgehaald en weer gegeven in het onderdeel. Zie [waardetype-sjablonen](#valuetypes)voor meer informatie.

### <a name="value-type-templates"></a>Value type-sjablonen <a name="valuetypes"></a>
Hieronder worden de momenteel ondersteunde typen waardetype-sjablonen beschreven. In de toekomst kunnen verdere sjablonen worden toegevoegd.
#### <a name="sampleddata"></a>SampledData
Vertegenwoordigt het [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR-gegevens type. Waarnemingen worden geschreven naar een waardestroom, te beginnen op een bepaald moment en worden verder verhoogd met de gedefinieerde periode. Als er geen waarde aanwezig is, `E` wordt er een geschreven naar de gegevens stroom. Als de periode zodanig is dat twee meer waarden dezelfde positie in de gegevens stroom innemen, wordt de meest recente waarde gebruikt. Dezelfde logica wordt toegepast wanneer een observatie met behulp van de SampledData wordt bijgewerkt.

| Eigenschap | Beschrijving 
| --- | ---
|**DefaultPeriod**|De standaard periode in milliseconden die moet worden gebruikt. 
|**Eenheid**|De eenheid die moet worden ingesteld op de oorsprong van de SampledData. 

#### <a name="quantity"></a>Hoeveelheid
Hiermee wordt het gegevens type [hoeveelheid](http://hl7.org/fhir/datatypes.html#Quantity) FHIR. Als er meer dan één waarde in de groepering aanwezig is, wordt alleen de eerste waarde gebruikt. Wanneer een nieuwe waarde arriveert die is toegewezen aan dezelfde observatie, wordt de oude waarde overschreven.

| Eigenschap | Beschrijving 
| --- | --- 
|**Eenheid**| Eenheids representatie.
|**Code**| Gecodeerde vorm van de eenheid.
|**Systeem**| Systeem dat het gecodeerde eenheids formulier definieert.

### <a name="codeableconcept"></a>CodeableConcept
Vertegenwoordigt het [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR-gegevens type. De werkelijke waarde wordt niet gebruikt.

| Eigenschap | Beschrijving 
| --- | --- 
|**Tekst**|Tekst zonder opmaak. 
|**Verdelen**|Een of meer [code ringen](http://hl7.org/fhir/datatypes-definitions.html#coding) die moeten worden toegepast op de gemaakte waarneming.
|**Codes []. Gecodeerd**|De code voor het [coderen](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes []. Opgehaald**|Het systeem voor de [code ring](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes []. Display**|De weer gave voor de [code ring](http://hl7.org/fhir/datatypes-definitions.html#coding).

### <a name="examples"></a>Voorbeelden
**Hart-rate-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Stappen-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Bloed druk-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Bloed druk-hoeveelheid**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Het apparaat is verwijderd-CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Volgende stappen

Bekijk veelgestelde vragen over Azure IoT connector voor FHIR (preview).

>[!div class="nextstepaction"]
>[Veelgestelde vragen over Azure IoT connector voor FHIR](fhir-faq.md)

* In de Azure Portal wordt Azure IoT connector voor FHIR aangeduid als IoT-connector (preview). FHIR is een geregistreerd handels merk van HL7 en wordt gebruikt met de machtiging van HL7.