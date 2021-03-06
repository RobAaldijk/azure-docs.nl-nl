---
title: Queue Storage gebruiken vanuit Java-Azure Storage
description: Meer informatie over het gebruik van wachtrij opslag voor het maken en verwijderen van wacht rijen. Meer informatie over het invoegen, bekijken, ophalen en verwijderen van berichten met de Azure Storage-client bibliotheek voor Java.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 08/19/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: c2ee32b3ced8fdcd5f9f889c4fd0183e46ad5d8d
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346006"
---
# <a name="how-to-use-queue-storage-from-java"></a>Queue Storage gebruiken met Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Overzicht

In deze hand leiding wordt uitgelegd hoe u code kunt gebruiken voor algemene scenario's die gebruikmaken van de Azure Queue Storage-service. De voorbeelden zijn geschreven in Java en maken gebruik van de [Azure Storage SDK voor Java][Azure Storage SDK for Java]. Scenario's zijn het **Invoegen** , **inspecteren** , **ophalen** en **verwijderen** van wachtrij berichten. De code voor **het maken** en **verwijderen** van wacht rijen wordt ook behandeld. Zie de sectie [volgende stappen](#next-steps) voor meer informatie over wacht rijen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Een Java-toepassing maken

# <a name="java-v12"></a>[Java-V12](#tab/java)

Controleer eerst of uw ontwikkel systeem voldoet aan de vereisten die worden vermeld in de [Azure Queue Storage-client bibliotheek voor Java-V12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue).

Een Java-toepassing maken met de naam *Queues-How-to-V12* :

1. In een console venster (zoals cmd, Power shell of bash), gebruikt u maven om een nieuwe console-app te maken met de naam *wachtrijen-How-to-V12*. Typ de volgende **mvn** -opdracht om een 'Hallo wereld!' te maken. Java-project.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. De uitvoer van het project zou er ongeveer als volgt moeten uitzien:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *queues-howto-v12* directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand *pom.xml* in uw teksteditor. Voeg het volgende afhankelijkheidselement toe aan de groep met afhankelijkheden.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Controleer eerst of uw ontwikkel systeem voldoet aan de vereisten die worden vermeld in de [Azure Storage SDK voor Java V8](https://github.com/azure/azure-storage-java). Volg de instructies voor het downloaden en installeren van de Azure Storage bibliotheken voor Java. Vervolgens kunt u een Java-toepassing maken met behulp van de voor beelden in dit artikel.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Uw toepassing configureren voor toegang tot de wachtrij opslag

Voeg de volgende import instructies toe aan de bovenkant van het Java-bestand waar u Azure Storage-Api's wilt gebruiken voor toegang tot wacht rijen:

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Een Azure Storage-connection string instellen

Een Azure Storage-client maakt gebruik van een opslag connection string voor het openen van gegevens beheer Services. Haal de naam en de primaire toegangs sleutel voor uw opslag account op in de [Azure Portal](https://portal.azure.com). Gebruik deze als de waarden *AccountName* en *AccountKey* in de Connection String. In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks:

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

U kunt deze teken reeks opslaan in het service configuratie bestand met de naam *ServiceConfiguration. cscfg*. Voor een app die binnen een Microsoft Azure-rol wordt uitgevoerd, opent u de connection string door **RoleEnvironment. getConfigurationSettings** aan te roepen. Hier volgt een voor beeld van het ophalen van de connection string van een **setting** -element met de naam *StorageConnectionString* :

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

In de volgende voor beelden wordt ervan uitgegaan dat u een **teken reeks** object hebt met de opslag Connection String.

## <a name="how-to-create-a-queue"></a>Procedure: een wachtrij maken

# <a name="java-v12"></a>[Java-V12](#tab/java)

Een **QueueClient** -object bevat de bewerkingen voor interactie met een wachtrij. Met de volgende code wordt een **QueueClient** -object gemaakt. Gebruik het object **QueueClient** om de wachtrij te maken die u wilt gebruiken.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Met een **CloudQueueClient** -object kunt u referentie objecten voor wacht rijen ophalen. Met de volgende code wordt een **CloudQueueClient** -object gemaakt. (Opmerking: er zijn meer manieren om **Cloud Storage account** -objecten te maken. Zie **Cloud Storage account** in de naslag informatie voor de [Azure Storage-client SDK]voor meer gegevens.

Gebruik het object **CloudQueueClient** om een verwijzing naar de wachtrij die u wilt gebruiken op te halen. U kunt de wachtrij maken als deze niet bestaat.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>Procedure: een bericht aan een wachtrij toevoegen

# <a name="java-v12"></a>[Java-V12](#tab/java)

Als u een bericht wilt invoegen in een bestaande wachtrij, roept u de **SendMessage** -methode aan. Een bericht kan een teken reeks zijn (in UTF-8-indeling) of een byte matrix. Hier is code die een teken reeks bericht naar de wachtrij verzendt.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Voor het invoegen van een bericht in een bestaande wachtrij maakt u eerst een nieuwe **CloudQueueMessage**. Vervolgens roept u de methode **addMessage** aan. Een **CloudQueueMessage** kan worden gemaakt vanuit een tekenreeks (in UTF-8-indeling) of een bytematrix. Hier is code waarmee een wachtrij wordt gemaakt (als deze niet bestaat) en het bericht ' Hello, World ' wordt ingevoegd.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>Procedure: het volgende bericht bekijken

U kunt het bericht aan de voor kant van een wachtrij bekijken zonder het uit de wachtrij te verwijderen door **peekMessage** aan te roepen.

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedure: de inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werk taak vertegenwoordigt, kunt u deze functie gebruiken om de status bij te werken. Met de volgende code wordt een wachtrij bericht met nieuwe inhoud bijgewerkt en wordt de time-out voor de zicht baarheid ingesteld op een periode van 30 seconden. Als u de time-out voor de zicht baarheid uitbreidt, kan de client nog 30 seconden verder werken aan het bericht. U kunt ook een aantal nieuwe pogingen volgen. Als het bericht meer dan *n* keer opnieuw is geprobeerd, verwijdert u het. Dit scenario biedt beveiliging tegen een bericht dat telkens wanneer het wordt verwerkt een toepassings fout activeert.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Met het volgende code voorbeeld wordt gezocht in de wachtrij met berichten, wordt de inhoud van het eerste bericht gevonden dat overeenkomt met een zoek reeks, wordt de inhoud van het bericht gewijzigd en wordt het proces afgesloten.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Met de volgende voorbeeld code wordt gezocht in de wachtrij met berichten en wordt de eerste bericht inhoud gezocht die overeenkomt met ' Hello, World ', wordt de inhoud van het bericht gewijzigd en wordt het proces afgesloten.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

In het volgende code voorbeeld wordt alleen het eerste zicht bare bericht in de wachtrij bijgewerkt.

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>Procedure: de lengte van de wachtrij ophalen

U kunt een schatting ophalen van het aantal berichten in de wachtrij.

# <a name="java-v12"></a>[Java-V12](#tab/java)

De methode **getProperties** vraagt de Queue-service voor verschillende huidige waarden. Een van de waarden is een telling van het aantal berichten in een wachtrij. De telling is alleen geschatte omdat berichten kunnen worden toegevoegd of verwijderd na uw aanvraag. De methode **getApproximateMessageCount** retourneert de laatste waarde die is opgehaald door de aanroep van **getProperties** , zonder de Queue-service aan te roepen.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

De methode **downloadAttributes** vraagt de Queue-service voor verschillende huidige waarden. Een van de waarden is een telling van het aantal berichten in een wachtrij. De telling is alleen geschatte omdat berichten kunnen worden toegevoegd of verwijderd na uw aanvraag. De methode **getApproximateMessageCount** retourneert de laatste waarde die is opgehaald door de aanroep van **downloadAttributes** , zonder de Queue-service aan te roepen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>Procedure: het volgende bericht uit de wachtrij verwijderen

# <a name="java-v12"></a>[Java-V12](#tab/java)

Met uw code wordt een bericht uit een wachtrij in twee stappen uit de wachtrij verwijderd. Wanneer u **receiveMessage** aanroept, wordt het volgende bericht in een wachtrij weer gegeven. Een bericht dat wordt geretourneerd door **receiveMessage** , wordt onzichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook **deleteMessage** aanroepen. Als uw code een bericht niet kan verwerken, zorgt dit proces met twee stappen ervoor dat u hetzelfde bericht kunt ophalen en het opnieuw probeert. Uw code aanroepen **deleteMessage** direct nadat het bericht is verwerkt.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Met uw code wordt een bericht uit een wachtrij in twee stappen uit de wachtrij verwijderd. Wanneer u **retrieveMessage** aanroept, wordt het volgende bericht in een wachtrij weer gegeven. Een bericht dat wordt geretourneerd door **retrieveMessage** , wordt onzichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook **deleteMessage** aanroepen. Als uw code een bericht niet kan verwerken, zorgt dit proces met twee stappen ervoor dat u hetzelfde bericht kunt ophalen en het opnieuw probeert. Uw code aanroepen **deleteMessage** direct nadat het bericht is verwerkt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>Aanvullende opties voor het dequeuing van berichten

Er zijn twee manieren om het ophalen van berichten aan te passen vanuit een wachtrij. Haal eerst een batch berichten op (Maxi maal 32). Stel ten tweede een langere of korte time-out voor onzichtbaar in, waardoor uw code meer of minder tijd nodig is om elk bericht volledig te verwerken.

# <a name="java-v12"></a>[Java-V12](#tab/java)

In het volgende code voorbeeld wordt de methode **receiveMessages** gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp **van een for** -lus. Ook wordt de time-out voor onzichtbaar ingesteld op vijf minuten (300 seconden) voor elk bericht. De time-out begint voor alle berichten tegelijk. Wanneer vijf minuten zijn verstreken sinds de aanroep van **receiveMessages** , worden alle berichten die niet worden verwijderd, weer zichtbaar.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

In het volgende code voorbeeld wordt de methode **retrieveMessages** gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp **van een for** -lus. Ook wordt de time-out voor onzichtbaar ingesteld op vijf minuten (300 seconden) voor elk bericht. De time-out begint voor alle berichten tegelijk. Wanneer vijf minuten zijn verstreken sinds de aanroep van **retrieveMessages** , worden alle berichten die niet worden verwijderd, weer zichtbaar.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>Procedure: de wacht rijen weer geven

# <a name="java-v12"></a>[Java-V12](#tab/java)

Voor het verkrijgen van een lijst met de huidige wacht rijen roept u de methode **QueueServiceClient. list Queues ()** aan, die een verzameling **QueueItem** -objecten retourneert.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Voor het verkrijgen van een lijst met de huidige wacht rijen roept u de methode **CloudQueueClient. list Queues ()** aan, die een verzameling **CloudQueue** -objecten retourneert.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>Procedure: een wachtrij verwijderen

# <a name="java-v12"></a>[Java-V12](#tab/java)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de **Delete** -methode aan in het **QueueClient** -object.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de methode **deleteIfExists** aan voor het object **CloudQueue** .

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van wachtrij opslag hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslag taken.

- [Azure Storage SDK voor Java][Azure Storage SDK for Java]
- [Azure Storage Client SDK-referentie][Azure Storage Client SDK Reference]
- [REST-API voor Azure Storage-services][Azure Storage Services REST API]
- [Blog van het Azure Storage-team][Azure Storage Team Blog]

[Azure SDK for Java]: https://github.com/azure/azure-sdk-for-java
[Azure Storage SDK for Java]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage
[Azure Storage Client SDK-referentie]: https://azure.github.io/azure-sdk-for-java/storage.html
[Azure Storage Services REST API]: /rest/api/storageservices/
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
