# Quick Start Tutorial

This tutorial walks you through creating your first channel to receive, transform, and route an HL7 message.

---

## Prerequisites

- OIE Server is installed and running (see [Installation Guide](Installation-Guide))
- You can access the Administrator at `https://localhost:8443`

---

## Step 1: Log In

1. Launch the Administrator client
2. Enter `https://localhost:8443` as the server address
3. Log in with your credentials (default: `admin` / `admin`)
4. On first login, you will be prompted to change the default password

---

## Step 2: Create a New Channel

1. In the left-hand panel, click **Channels**
2. Click the **New Channel** button in the toolbar (or use the Channel Wizard)
3. Fill in the **Summary** tab:
   - **Name:** `My First Channel`
   - **Description:** `Receives HL7 messages and writes to file`
   - **Data Types:** Leave as default (HL7 v2.x)
4. Click **Next** or navigate to the **Source** tab

---

## Step 3: Configure the Source Connector

The source connector defines how messages enter the channel.

1. Select the **Source** tab
2. Set **Connector Type** to **TCP Listener**
3. Configure:
   - **Host:** `0.0.0.0` (listen on all interfaces)
   - **Port:** `6661`
   - **Transmission Mode:** MLLP (default for HL7)
4. The source will now listen for incoming HL7 messages on port 6661

---

## Step 4: Configure a Destination Connector

Destinations define where processed messages are sent.

1. Select the **Destinations** tab
2. The default destination `Destination 1` is already created
3. Set **Connector Type** to **File Writer**
4. Configure:
   - **Directory:** `/tmp/oie-output` (or a Windows path like `C:\oie-output`)
   - **File Name:** `message_${message.messageId}.hl7`
   - **Template:** `${message.encodedData}` (writes the transformed message)

---

## Step 5: Add a Simple Transformer (Optional)

1. On the **Destinations** tab, click the **Edit Transformer** button
2. Add a new **Mapper** step:
   - **Variable:** `patientName`
   - **Mapping:** `msg['PID']['PID.5']['PID.5.1'].toString()` (extracts patient last name)
3. Add a **Message Builder** step to modify a field if desired
4. Click **Accept** to save

---

## Step 6: Deploy and Test

1. Click **Save** (Ctrl+S) to save the channel
2. Return to the **Dashboard**
3. Select your channel and click **Deploy** in the toolbar
4. The channel status should show as **Started** (green)

### Send a Test Message

You can send a test HL7 message using any TCP client. Example using the **Edit Message** dialog:

1. In the Dashboard, right-click the channel and select **Send Message**
2. Paste a sample HL7 message:

```
MSH|^~\&|SENDING|FACILITY|RECEIVING|FACILITY|20240101120000||ADT^A01|MSG00001|P|2.3
EVN|A01|20240101120000
PID|1||12345^^^MRN||DOE^JOHN||19800101|M
PV1|1|I|ICU^101^A
```

3. Click **Process Message**
4. Check your output directory for the written file

---

## Step 7: Monitor Results

1. Go to the **Dashboard** to see message statistics
2. Click on the channel name to open the **Message Browser**
3. View individual messages, their raw/transformed content, and any errors

---

## Next Steps

- Learn about [Channels and Connectors](Channels-and-Connectors) in depth
- Explore all available [Connectors](Connector-Reference)
- Set up [Filters and Transformers](Filters-and-Transformers) for advanced routing
- Read the [JavaScript Scripting Reference](JavaScript-Scripting-Reference) for custom logic

[Back to Home](Home)
