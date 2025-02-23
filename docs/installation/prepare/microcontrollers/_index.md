---
title: "Microcontroller Setup: the Micro-RDK"
linkTitle: "Microcontroller Setup"
weight: 50
no_list: true
type: docs
image: "installation/thumbnails/esp32-espressif.png"
imageAlt: "E S P 32 - espressif"
images: ["/installation/thumbnails/esp32-espressif.png"]
description: "Set up the Espressif ESP32 with the micro-RDK."
aliases:
    - /installation/microcontrollers

# SMEs: Nicolas Menard

---

{{% readfile "/static/include/micro-rdk.md" %}}

## Get Started

Follow these steps to install and build the micro-rdk on your ESP32:

1. Install the [required software](#software-requirements)
2. [Set up your development enviroment](#set-up-your-development-enviroment) manually or with Viam's Canon CLI utility *(recommended)*
3. [Install the Micro-RDK](#install-the-micro-rdk)

### Hardware Requirements

You need an Espressif ESP32 microcontroller to use the micro-RDK.
Viam recommends purchasing the ESP32 with a [development board](https://www.espressif.com/en/products/devkits).

* **Minimal configuration:** 384kB Ram 4MB flash
* **Recommended configuration:** 384kB Ram + 8MB SPIRAM + 4MB Flash

### Software Requirements

The micro-RDK is written in Rust.
To be able to program the ESP32 on macOS and Linux systems, you must install the following software on your development machine:

#### Install Dependencies

{{< tabs >}}
{{% tab name="Linux" %}}

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
sudo apt-get install git libssl-dev dfu-util libusb-1.0-0 libudev-dev
```

{{% /tab %}}
{{% tab name="macOS" %}}

If you haven't already, install Homebrew:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then, install `dfu-util`:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
brew install dfu-util
```

{{% /tab %}}
{{% /tabs %}}

#### Install `Rust`

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

See the [Rust Installation guide](https://www.rust-lang.org/tools/install) for more information and other installation methods.

#### Install `cargo-generate` with `cargo`

 `cargo` installs automatically when downloading Rust with rustup.

Run the following command to install `cargo-generate`:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
cargo install cargo-generate
```

#### Install `espflash`

Run the following command to install `espflash`

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
cargo install espflash
```

### Set up your development enviroment

{{< tabs >}}
{{% tab name="Canon CLI (recommended)" %}}

[Canon](https://github.com/viamrobotics/canon) is a CLI utility for managing a Docker-based canonical environment.

Canon requires a working installation of Docker Engine.

[Install Docker Engine](https://docs.docker.com/engine/install/) before proceeding.
If you are running Docker Engine on Linux, make sure that you go through the [post installation steps](https://docs.docker.com/engine/install/linux-postinstall/) to run Docker automatically on startup.

{{< tabs >}}
{{% tab name="Direct Go Install (Linux)" %}}

Make sure your system has [Go 1.19](https://golangtutorial.dev/news/go-1.19-version-released/#major-changes-in-go-119-version) or later installed.
Verify your version of Go with `go version`.

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
go install github.com/viamrobotics/canon@latest
```

Make sure to [add the go binary folder to your `PATH`](https://go.dev/doc/gopath_code) by running:
`export PATH=$PATH:$(go env GOPATH)/bin`.

{{% /tab %}}
{{% tab name="With Homebrew (Linux/MacOS)" %}}

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
brew install viamrobotics/brews/canon
```

{{% /tab %}}
{{% /tabs %}}

{{% /tab %}}
{{% tab name="Manual" %}}

{{< alert title="Tip" color="tip" >}}
You only need to follow these steps if you are not using Canon to build the micro-RDK.

If you have completed your set up with Canon, skip this section and continue to [install the Micro-RDK](#install-the-micro-rdk).

{{< /alert >}}

To set up the Docker development environment for ESP manually, complete the following instructions:

#### Install build dependencies

{{< tabs >}}
{{% tab name="Linux" %}}

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
sudo apt-get install git wget flex bison gperf python3 python3-pip python3-venv cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0
```

{{% /tab %}}
{{% tab name="macOS" %}}

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
brew install cmake ninja dfu-util
```

{{% /tab %}}
{{% /tabs %}}

#### Install and Activate the ESP-IDF

Clone Viam's fork of the ESP-IDF, the development framework for Espressif SoCs (System-on-Chips) supported on Windows, Linux and macOS:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
mkdir -p ~/esp
cd ~/esp
git clone --depth 1 -b v4.4.4 --single-branch --recurse-submodules --shallow-submodules https://github.com/npmenard/esp-idf
```

Then, install the required tools for ESP-IDF:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
cd ~/esp/esp-idf
./install.sh esp32
```

To activate ESP-IDF, run the following command to source (`.`) the activation script `export.sh`:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
. $HOME/esp/esp-idf/export.sh
```

{{< alert title="Tip" color="tip" >}}
To avoid conflicts with other toolchains, adding this command to your `.bashrc` or `.zshrc` is not recommended.
Instead, save this command to run in any future terminal session where you need to activate the ESP-IDF development framework.
{{< /alert >}}

#### Install the Rust ESP Toolchain and Activate the ESP-RS Virtual Environment

First, install the following tools with `cargo`:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
cargo install espup
```

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
cargo install cargo-espflash v2.0.0-rc.1
```

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
cargo install ldproxy
```

To download and install the esp-rs toolchain, run:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
espup install -s -f ~/esp/export-rs.sh -v 1.67.0
```

To activate the ESP Rust toolchain, run the following command to source (`.`) the activation script `export-rs.sh`:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
. $HOME/esp/export-rs.sh
```

{{< alert title="Tip" color="tip" >}}

To avoid conflicts with other toolchains, adding this command to your .bashrc or .zshrc is not recommended.
Instead, save this command to run in any future terminal session where you need to activate the ESP-IDF development framework.

{{< /alert >}}

{{% /tab %}}
{{% /tabs %}}

## Install the Micro-RDK

### Create a New Robot

Navigate to [the Viam app](https://app.viam.com) and [add a new robot](/manage/fleet/robots/#add-a-new-robot) in your desired location.

Click on the name of the robot to go to the robot's page.
Then, navigate to the **Config** tab.

### Configure an esp32 board

{{< alert title="Info" color="info" >}}

The`esp32` [board](/components/board/) model is not currently provided for you as a built-in option in [the Viam app](https://app.viam.com), so you cannot use the **Config Builder** to configure this board.

{{< /alert >}}

<!--
TODO: UNCOMMENT WHEN AVAILABLE IN APP UI, AND MOVE THIS CHUNK TO BOARD PAGE (SG)

Navigate to the **Config** tab of your robot's page in [the Viam app](https://app.viam.com).
Click on the **Components** subtab and navigate to the **Create component** menu.
Enter a name for your board, select the type `board`, and select the `numato` model.

Click **Create component**.

 ![An example configuration for an esp21 board in the Viam app Config Builder.](/components/board/esp32-ui-config.png)

 Edit and fill in the attributes as applicable.
 -->

To add an `esp32` board, navigate to the **Config** tab of your robot's page in [the Viam app](https://app.viam.com) and select **Raw JSON** mode.

Copy the following JSON template and paste it into your configuration inside the `"components"` array:

{{< tabs name="Configure an esp32 Board" >}}
{{% tab name="JSON Template"%}}

```json {class="line-numbers linkable-line-numbers"}
{
  "attributes": {
    "pins": [
      <int>
    ],
    "analogs": [
      {
        "pin": "<number>",
        "name": "<your-analog-name>"
      }
    ]
  },
  "depends_on": [],
  "model": "esp32",
  "name": "<your-board-name>",
  "type": "board"
}
```

{{% /tab %}}
{{% tab name="JSON Example" %}}

```json {class="line-numbers linkable-line-numbers"}
{
  "attributes": {
    "pins": [
      15
    ],
    "analogs": [
      {
        "pin": "34",
        "name": "sensor"
      }
    ]
  },
  "depends_on": [],
  "model": "esp32",
  "name": "board",
  "type": "board"
}
```

{{% /tab %}}
{{< /tabs >}}

Edit and fill in the attributes as applicable.
Click **Save config**.

The following attributes are available for `esp32` boards:

| Name | Type | Inclusion | Description |
| ---- | ---- | --------- | ----------- |
| `pins` | object | Required | The {{< glossary_tooltip term_id="pin-number" text="pin number" >}} of any GPIO pins you wish to use as input/output with the [`GPIOPin` API](/program/apis/#gpio-pins). |
| `analogs` | object | Optional | Attributes of any pins that can be used as analog-to-digital converter (ADC) inputs. See [configuration info](/components/board/#analogs). |
<!-- | `webhook` | string | Optional | A webhook's URL. See [GitHub](viam-labs/webhook-template) for an example. |
| `webhook-secret` | string | Optional | The robot secret for a configured `webhook`. See [GitHub](viam-labs/webhook-template) for an example. | -->

### Generate a New Project from the Micro-RDK Template

[Start Docker](https://docs.docker.com/config/daemon/start/) on your development machine.
If you haven't already, complete Docker's [Linux post installation steps](https://docs.docker.com/engine/install/linux-postinstall/) to set up Docker to run whenever your system boots up.

Use [the Micro-RDK template](https://github.com/viamrobotics/micro-rdk-robot-template.git) to create a new Micro-RDK project to upload to your ESP32 by running:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
cargo generate --git https://github.com/viamrobotics/micro-rdk-robot-template.git
```

You will be prompted to paste your robot's JSON configuration into the terminal.
To obtain this:

* Navigate to [your new robot's](/installation/prepare/microcontrollers/#create-a-new-robot) page on [the Viam app](https://app.viam.com).
* Click on the **Setup** tab.
Keep your `Mode` and `Architecture` selections at default.
* Click the **Copy viam-server config** button on the right side of the **Setup** tab.
The micro-RDK needs this JSON file, which contains your robot part secret key and cloud app address, to connect to the [Viam app](https://app.viam.com).
* Paste the `viam-server` config into your terminal when prompted.

{{% snippet "secret-share.md" %}}

### Upload the Micro-RDK to your ESP32

Now, upload the project to connect to your ESP32 and remotely control it live on [the Viam app](https://app.viam.com):

{{< tabs >}}
{{% tab name="Use Canon" %}}

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
cd <your-path-to/your-project-directory>
canon bash -lc "make build-esp32-bin"
make flash-esp32-bin
```

{{% /tab %}}
{{% tab name="Local environment" %}}

Make sure you have run `. ~/dev/esp/export-rs.sh` and `. ~/dev/esp/esp-idf/export.sh` before running the following command:

```sh {id="terminal-prompt" class="command-line" data-prompt="$"}
make upload
```

{{% /tab %}}
{{% /tabs %}}

If successful, you will retain a serial connection to the board until you press `Ctrl-C`.
To manage this connection, consider running it within a dedicated terminal session, or under `tmux` or `screen`.
While the serial connection is live, you can also restart the currently flashed image with `Ctrl-R`.

Navigate to your new robot's page on [the Viam app](https://app.viam.com).
If successful, **Live** should be displayed underneath **Last online**.

### Troubleshooting

If you run into the error `Failed to open serial port` when flashing your ESP32 with Linux, make sure the user is added to the group `dialout` with `sudo gpasswd -a $USER dialout`

You can find additional assistance in the [Troubleshooting section](/appendix/troubleshooting/).

{{< snippet "social.md" >}}
