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
