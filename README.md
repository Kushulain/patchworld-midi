# Patchworld Direct MIDI Importer

This repository contains a web-based tool designed to run directly inside the **Patchworld** in-game browser, leveraging the Vuplex Webview plugin. 

Patchworld is a music and world-building sandbox available on **Meta Quest** and **PCVR (Windows)**.

## Live Page

The current operational page for this tool is `midi3.html`. You can access it directly in the Patchworld in-game browser at:
**[http://kushulain.github.io/patchworld-midi/midi3.html](http://kushulain.github.io/patchworld-midi/midi3.html)**

**If you fork this project**, your GitHub Pages URL will automatically be:
**`https://<your-username>.github.io/<your-repo-name>/midi3.html`**
*(The web page automatically detects your username and repo name from the URL so it loads the MIDI files directly from your own fork!)*

*(Note: Other HTML files in this repository are currently ignored/obsolete).*

## The Web Bridge Block

This project demonstrates the power of a new Patchworld feature: the **Web Bridge** block. The Web Bridge enables real-time, two-way communication between a live web page and the in-game blocks. 

* **Overview & Tutorial:** [Patchworld Wiki - Web Bridge](https://wiki.patchxr.io/en/patching/web-bridge)
* **API Documentation:** Please see the complete [API_Reference.md](API_Reference.md) for details on available methods and events.

## How It Works (Wireless Jolts & MIDI)

The `midi3.html` page acts as an importer that parses standard MIDI files and squirts their data directly into the Patchworld environment using the Web Bridge API.

Here is a quick breakdown of the workflow:

1. **Fetching & Parsing MIDI:** The script retrieves `.mid` files directly from this repository and uses `@tonejs/midi` to parse the binary data into distinct, playable events (such as *Note On*, *Note Off*, *Control Change*, etc.).
2. **Connecting to Patchworld:** Upon loading in the in-game browser, it establishes a connection using the injected `PatchWorld` JavaScript object.
3. **Wireless Jolts:** The communication heavily relies on **Wireless Jolts** to transmit the data. 
   * The page sends data arrays sequentially by sending Jolts to specific channels (e.g., `midi_time_index`, `midi_type_value`, `midi_data1_value`, etc.).
   * It also utilizes the `PatchWorld.subscribeWifi(channel)` method to listen for incoming Jolts from the game.
4. **Reliable Communication (ACK/NAK):** To ensure no data is lost during the transfer, the importer implements an Acknowledgment system. When a value is written, the webpage waits for a `midi_ack` (success) or `midi_nak` (failure) Jolt from Patchworld before proceeding to the next chunk of data.
5. **Completion:** Once all the parsed MIDI events are sent and acknowledged, a final `midi_event_count` Jolt is triggered, letting Patchworld know the sequence is completely imported and ready to play!
