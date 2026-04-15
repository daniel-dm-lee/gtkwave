# GTKWave

GTKWave is a fully featured GTK+ based wave viewer for Unix and Win32 which reads FST, and GHW files as well as standard Verilog VCD/EVCD files and allows their viewing.

## Building GTKWave from source

### Installing dependencies

Debian, Ubuntu:

```sh
apt install build-essential meson gperf flex desktop-file-utils libgtk-3-dev \
            libbz2-dev libjudy-dev libgirepository1.0-dev
```

Fedora:

```sh
dnf install meson gperf flex glib2-devel gcc gcc-c++ gtk3-devel \
            gobject-introspection-devel desktop-file-utils tcl
```

macOS:

```sh
brew install desktop-file-utils shared-mime-info       \
             gobject-introspection gtk-mac-integration \
             meson ninja pkg-config gtk+3 gtk4
```

### Building GTKWave


```sh
git clone "https://github.com/gtkwave/gtkwave.git"
cd gtkwave
meson setup build && cd build && meson install
```

### Running GTKWave
```sh
gtkwave [path to a .vcd, .fst, .ghw dump file or a .gtkw savefile]
```
For more information about available command line parameters refer to the built in-help (`gtkwave --help`) or the [`gtkwave` man page](https://gtkwave.github.io/gtkwave/man/gtkwave.1.html).


## NEW FEATURE #1
##  GTKWave Smart Search & Navigation Feature (Custom Fork)

We have enhanced the traditional GTKWave 4.0 interface to include a modern, highly responsive "Smart Search" workflow. This allows developers to find deeply nested signals across expansive modules with a fraction of the effort previously required.

### Key Features

1. **Global Keyboard Shortcuts**
   - **`Cmd + Shift + F`** (Mac) or **`Ctrl + Shift + F`** (Windows/Linux)
   - **`/`** (Slash key - Vim style)
   - These shortcuts utilize a **GTK Key Snooper**, meaning they will instantly trigger the search bar *regardless* of which internal panel you are currently clicking on.

2. **Automated Search & Navigation (SST Auto-Expand)**
   - When a signal is queried (e.g., `mip`), the system first checks the visible Wave Window.
   - If not found, it iterates through the entire **SST (Signal Search Tree)** structure.
   - Upon locating the signal, it **automatically expands the hierarchy** (e.g., `top` -> `system` -> `csr`), jumping directly to the corresponding location.

3. **Synchronized Signal List Auto-Selection**
   - Once the SST expands, a deferred task (`g_timeout_add`) waits for the bottom module view to load, then **automatically scrolls and highlights** the exact signal inside the 'Type / Signals' box.
   - **Benefit:** You no longer need to manually fish for the signal after finding its parent module. Just drag it directly into the viewer or press `Enter` to append it.

4. **Intelligent Multiple Result Resolution**
   - If you search a generic term (e.g., `time_`) resulting in multiple hits, GTKWave will compile all matches and spawn a **"Select Signal" popup window**.
   - Browse the matching entries and **double-click** (or select and click "OK") on the right one to instantly perform the auto-expand & list selection action.

5. **Cross-Platform Compatibility & Pinch-Zoom**
   - Implemented compiler directives (`#if defined(MAC_INTEGRATION) || defined(__APPLE__)`) to ensure the keyboard hooks respect standard OS bindings on both macOS and Windows.
   - Restored and attached native trackpad **pinch-to-zoom** capability natively mapped over existing GTKGesture event streams.

### Usage Example

1. Start GTKWave and load your dump file (`.fst`, `.vcd`, etc.).
2. Tap the **`/`** key anywhere in the application.
3. Type `clk` and press **Enter**.
4. If there are multiple different clocks, a prompt will let you pick the specific clock path.
5. Select `top.cpu.clk`. The left tree will snap open, selecting `cpu`, and the bottom list will snap directly to `clk`.


## NEW FEATURE #2
###  Value Search (Right-Click Menu)

A "Search Value Forward" feature has been added to the signal list context menu, allowing you to jump to specific data patterns instantly.

1. **Context-Aware Searching**
   - Simply **Right-Click** on any signal name (e.g., `rd_en` or `data_bus`).
   - Select **"Search Value Forward..."** at the very top of the menu.

2. **Advanced Search Engine**
   - **Single Bit:** Search for `1`, `0`, `x`, or `z`.
   - **Vectors/Buses:** Search for specific Hex, Decimal, or String values based on the current display format.
   - **Directional:** The search starts from the current **Primary Marker** position (or the left edge of the screen if no marker is set) and moves forward in time.

3. **Instant Navigation & Feedback**
   - **Success:** The waveform automatically scrolls to the next occurrence, and the Primary Marker is snapped to the exact timestamp.
   - **Clear Alerts:** If the value is not found in the remaining duration of the simulation, a dedicated popup dialog will notify the user.

---

## NEW FEATURE #3
#### How to use:
1. Right-click the signal `rd_en`.
2. Click **"Search Value Forward..."**.
3. Type `1` and press Enter.
4. GTKWave will immediately jump to the next clock cycle where `rd_en` becomes high.


### Large VCD to FST Auto-Conversion

Handling massive VCD files (e.g., 30GB+) can heavily slow down GTKWave and consume excessive memory. This feature automatically detects large files and offers a seamless conversion to the highly optimized FST format before loading.

1. **Smart Size Detection**
   - Automatically detects if the input `.vcd` file size exceeds 500MB using 64-bit safe checks.
2. **Interactive UI & Real-time Progress**
   - Prompts the user with an "Always-on-top" dialog to perform the conversion.
   - Displays a smooth, non-blocking progress bar while `vcd2fst` processes the file in the background (no UI freezing).
3. **Automatic Loading**
   - Once the conversion is complete, GTKWave silently swaps the target file and opens the lightweight `.fst` file instead.

---

## NEW FEATURE #4
### Auto-Save & Session Restore

No more losing your complex signal setups when you accidentally close GTKWave! This feature automatically backs up your workspace and asks if you want to restore it the next time you launch the application.

1. **Seamless Auto-Save**
   - Whenever you exit GTKWave (via menu, window close 'X' button, or `Cmd+Q`), your current signal traces and formats are silently saved to a `.gtkwave_autosave.gtkw` file.
2. **Smart Restore Prompt**
   - Upon launching GTKWave, if a previous session backup is detected, a priority popup dialog will ask if you want to restore it.
   - Simply click **"Yes"** to instantly reload all your previously configured signals into the Time window.


## NEW FEATURE #5
###  Real-time Cycle & Value Delta Monitor (Verdi-style)

Quickly measure time differences, count clock cycles, and calculate bus value changes between two points in time, just like Synopsys Verdi.

1. **Real-time Non-blocking UI**
   - The monitor window stays on top without freezing the main application.
   - As you drag your markers (M1 and M2) across the waveform, the cycle counts and time delta update instantly in real-time.
2. **Smart Signal Handling**
   - **1-bit Signals (e.g., clk):** Automatically counts the number of rising edges ('1' or 'H') within the selected range.
   - **Multi-bit Buses (e.g., mcycle):** Calculates the exact mathematical difference (Value Delta) between the start and end marker values.

---

####  How to use:
1. Place the **Primary Marker (M1)** using `Left-Click`.
2. Place the **Baseline Marker (M2)** using `Middle-Click` (or `Shift + Left-Click`).
3. Right-click on a target signal (like `clk` or `mcycle`) and select **"Count Cycles in Range..."**.
4. A persistent monitor window will appear. Leave it open and adjust your markers to see real-time updates!
